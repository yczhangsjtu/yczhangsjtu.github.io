---
title: IVC and the Nova Folding Scheme
layout: math
---

# IVC and the Nova Folding Scheme

IVC stands for _incremental verifiable computation_. IVC allows a prover to generate a proof for some computation incrementally. For example, let $F$ be a function and $x^{(1)}=F(x^{(0)})$. The prover produces a proof $\pi^{(1)}$ for validating the statement that given $x^{(0)},x^{(1)}$, they satisfy $x^{(1)}=F(x^{(0)})$. Next, let $x^{(2)}=F(x^{(1)})$, and the prover wants to generate a proof $\pi^{(2)}$ for the statement that given $x^{(0)},x^{(2)}$, there is $x^{(2)}=F(F(x^{(0)}))$. Using IVC, the prover does not need to produce $\pi^{(2)}$ from scratch. Instead, the prover updates $\pi^{(1)}$ into $\pi^{(2)}$. This procedure can be repeated indefinitely, such that the prover can efficiently generate the proof $\pi^{(k)}$ for the statement that given $x^{(0)},x^{(k)}$, they satisfy $x^{(k)}=F^{\circ k}(x^{(0)})$, where $F^{\circ k}$ stands for the function from repeating $F$ by $k$ times. The prover generates $\pi^{(k)}$ by updating $\pi^{(k-1)}$, with input $x^{(k)},x^{(k-1)}$ and $x^{(0)}$, without learning the intermediate values from the previous steps. Moreover, the running time of the prover and the verification of $\pi^{(k)}$ is independent of $k$.

IVC is useful in two scenarios:

1. There is a function $F$ that is being repeated slowly, and the prover does not know the non-deterministic input to $F$ in the future. One example is blockchain. In this scenario, each block can attach with it a proof $\pi^{(k)}$ that validates all the blocks up to this block. When the next block pops up, the miner can efficiently generate the next proof $\pi^{(k+1)}$ that validates all the blocks, without going through the entire blockchain.
2. To generate a proof for a large computation $C$ usually takes time that is super linear in $C$, e.g., the running time of SNARK provers are mostly $C\cdot\mathrm{polylog}(C)$. By decomposing $C$ into the composition of many $F$, and proving $C$ using IVC, the running time of the prover can be reduced to depend only linearly on the size of $C$.

How to construct an IVC?

## SNARK-based IVC

The most straightforward way to build an IVC is to invoke SNARK recursively. Since SNARK can be used to prove, theoretically, any computation, or any NP statement, it certainly can be used to prove statements like: given $x^{(0)},x^{(k)}$, there exists a proof $\pi^{(k-1)}$ and data $x^{(k-1)}$, such that $x^{(k)}=F(x^{(k-1)})$ and $\pi^{(k-1)}$ is a valid proof for a statement almost the same with this statement but is with respect to $x^{(0)},x^{(k-1)}$ instead.

Formally, we use SNARK to prove the following relation:

- Instance: $x^{(0)},x^{(k)},k$
- Witness: $\pi^{(k-1)}$, $x^{(k-1)}$
- Logic:
  - $k\geq 1$
  - $x^{(k)}=F(x^{(k-1)})$
  - If $k>1$, then
    - $\mathsf{SNARKVerify}((x^{(0)},x^{(k-1)},k-1),\pi^{(k-1)})=1$
  - If $k=1$, then $x^{(k-1)}=x^{(0)}$
  

However, note that the logic of this relation involves the verification procedure of this SNARK. Because the verification operates the base field of the elliptic curve, which is different from the field of the arithmetic circuit supported by this SNARK, i.e., the scalar field of the elliptic curve, expressing the verification logic in the arithmetic circuit requires simulating non-native field operations, which can make the circuit very large.

Nova mitigates this issue by introducing the _folding scheme_.

## Nova

Intuitively, a folding scheme allows the prover to fold two pairs of instance-witness into a single pair, such that if the folded instance is correct, then the original two instances are also correct with overwhelming probability. Formally, a non-interactive folding scheme for relation $\mathcal{R}$ consists of two algorithms $\mathsf{Fold}$ and $\mathsf{FoldVerify}$.

- $\mathsf{Fold}$ takes two instance-witness pairs $(x_1,w_1),(x_2,w_2)$ and produces a new instance-witness pair $(x,w)$, and a proof $\pi$.
- $\mathsf{FoldVerify}$ takes $x_1,x_2,x$ and $\pi$, and outputs 1 or 0 indicating if it is convinced that $x$ is the correct folding of $x_1$ and $x_2$.

Note that $\mathsf{FoldVerify}$ is not responsible for verifying the correctness of $x$. It only ensures that if $x$ is correct, then $x_1,x_2$ must also be correct. Therefore, $\mathsf{FoldVerify}$ can be much simpler than $\mathsf{SNARKVerify}$.

The Nova paper provides a folding scheme for the committed relaxed R1CS relation. Sangria relies on a folding scheme for the relaxed PLONK relation. HyperNova provides a folding scheme for CCS.

We will not explain how these folding schemes are constructed for now, since the basic IVC of Nova can be described regardless of the details of the folding scheme. However, we do need to note that in all these folding schemes, $\mathsf{FoldVerify}$ involves one or two scalar multiplications in elliptic curves, i.e., operations in mismatching field from what the SNARK supports. However, this is already much simpler than $\mathsf{SNARKVerify}$, which involves bilinear pairing, or thousands of scalar multiplications.

How does the folding scheme simplify the IVC verification logic?

Intuitively, in the previous recursive-SNARK-based IVC, the proof $\pi^{(k)}$ serves as an _accumulator_ that accumulates the validity of all the instances along the way, which means it is correct if and only if all the accumulated instances are correct.

What can play the role of the accumulator when SNARK is replaced with folding scheme?

Let's start with an empty accumulator $U^{(0)}$, which is a trivially correct instance (the formal definition of the NP relation will be given later). After folding it with the instance corresponding to $x^{(1)}$, we get $U^{(1)}$, and a proof $\pi^{(1)}$ for the correctness of this folding. Then we accumulate $x^{(2)}$, and get $U^{(2)}$ and $\pi^{(2)}$. So it seems that $U^{(k)},\pi^{(k)}$ together can serve as our accumulator.

However, here is a problem. In the SNARK-based IVC, the correctness of the accumulator can be checked directly against the corresponding instance. But here, to check the correctness of $U^{(k)},\pi^{(k)}$, the verifier must also know $U^{(k-1)}$, i.e., the previous accumulator, to accomplish the verification.

We fix this problem, temporarily, to also include $U^{(k-1)}$ in the accumulator. Now we try to give a formal definition of the NP relation for this IVC, and try to make it as close to the SNARK-based IVC as possible. We mark the parts different from the SNARK-based relation by bold font.

- Instance: $x^{(0)},x^{(k)},k$
- Witness: $\boldsymbol{\Pi^{(k-1)}=(U^{(k-1)},U^{(k-2)},\pi^{(k-1)})}$, $x^{(k-1)}$
- Logic:
  - $k\geq 1$
  - $x^{(k)}=F(x^{(k-1)})$
  - If $k>1$, then
    - $\boldsymbol{\mathsf{FoldVerify}((x^{(0)},x^{(k-1)},k-1),U^{(k-2)},U^{(k-1)},\pi^{(k-1)})=1}$
  - If $k=1$, then $x^{(k-1)}=x^{(0)}$

At first glance, it seems that everything fits perfectly. However, there is a tricky issue here: there is no guarantee that the witness corresponding to the instance $(x^{(0)},x^{(k-1)},k-1)$, which should be $((U^{(k-2)},U^{(k-3)},\pi^{(k-2)}),x^{(k-2)})$, contains the same $U^{(k-2)}$ as the witness of the current instance. Without this guarantee, the prover can still generate a valid proof even when $x^{(k-2)}$ is wrong. In that case, $U^{(k-2)}$ is an invalid instance. But this doesn't bother the malicious prover, because the prover can conveniently switch it to an arbitrarily selected valid instance as $U^{(k-2)}$, and compute an entirely new witness $\Pi^{(k-1)}$.

To ensure that the prover cannot choose $U^{(k-2)}$ arbitrarily, we include the hash of $U^{(k-2)}$ in the instance $x^{(k-1)}$, i.e., including the hash of $U^{(k-1)}$ in $x^{(k)}$. We cannot include $U^{(k-2)}$ directly in the instance, because $U^{(k-2)}$ is itself an instance. The updated NP relation is as follows, where the updates are marked by bold font.

- Instance: $x^{(0)},x^{(k)},k,\boldsymbol{h^{(k-1)}}$
- Witness: $\Pi^{(k-1)}=(U^{(k-1)},U^{(k-2)},\pi^{(k-1)})$, $x^{(k-1)},\boldsymbol{h^{(k-2)}}$
- Logic:
  - $k\geq 1$
  - $x^{(k)}=F(x^{(k-1)})$
  - $\boldsymbol{h^{(k-1)}=H(U^{(k-1)})}$
  - $\boldsymbol{h^{(k-2)}=H(U^{(k-2)})}$
  - If $k>1$, then
    - $\mathsf{FoldVerify}((x^{(0)},x^{(k-1)},k-1,\boldsymbol{h^{(k-2)}}),U^{(k-2)},U^{(k-1)},\pi^{(k-1)})=1$
  - If $k=1$, then $x^{(k-1)}=x^{(0)}$

In another word, before we fold $U^{(k-1)}$ and the current instance corresponding to $x^{(k)}$, we need to compute the hash of $U^{(k-1)}$ and put it in the instance.

After including the hash, to switch an invalid $U^{(k-2)}$ to a correct one, the prover must also change $h^{(k-2)}$ correspondingly. Otherwise, the witness would be incorrect. However, if the prover changes $h^{(k-2)}$, then for the tuple $(x^{(0)},x^{(k-1)},k-1,h^{(k-2)})$, the prover must also change its $U^{(k-2)}$ in its witness. However, the prover would be unable to prove that this changed $U^{(k-2)}$ is correctly computed from folding.

Note that, to verify the last instance, the verifier can simply ask the prover to supply the witness for the instance $U^{(k)}$. In this way, the IVC is constructed without needing a SNARK at all. Of course, if we want to further speed up the IVC verifier, the prover can also provide a SNARK proof for $U^{(k)}$.

To summarize:

1. We replace the accumulator, i.e., proof $\pi^{(k)}$ in the SNARK-based IVC, with $\Pi^{(k)}=(U^{(k)},U^{(k-1)},\pi^{(k)})$
2. To ensure that two adjacent accumulators $\Pi^{(k)},\Pi^{(k-1)}$ share the same instance, include the hash of the folded instance inside the next instance to accumulate.

## Nova on Cycle of Curves

Nova only partially mitigate the problem of mismatching field operation, because $\mathsf{FoldVerify}$ still involves scalar multiplications of EC group. To completely eliminate this problem, we need to introduce another curve, whose scalar field is the same as the base field of the first curve, and whose base field is the same as the scalar field of the first curve. These two curves form a cycle. We refer to the two groups $G_1$ and $G_2$, where $G_1$ has base field $\mathbb{F}_q$ and scalar field $\mathbb{F}_p$, and $G_2$ has base field $\mathbb{F}_p$ and scalar field $\mathbb{F}_q$.

To translate the IVC relation $\mathcal{R}$ into a NPC language that supports folding, say committed relaxed R1CS over $\mathbb{F}_p$, the instance would contain $G_1$ elements, and the folding proof $\pi$ also contain $G_1$ elements. Those $G_1$ elements are represented by coordinates in its base field $\mathbb{F}_q$.

Now we introduce the idea of CycleFold, which is the most up-to-date solution to this issue. Intuitively, the idea of CycleFold is simple: if a computation is over $\mathbb{F}_p$, then verifying the folding of this computation would be over $\mathbb{F}_q$, and vice versa; therefore, folding provides an effective way to _flip_ the arithmetic field of the computation. In another word, **whenever we have non-native field operations, don't verify these operations, but instead replace it with its folding verification**.

We extract the $\mathbb{F}_q$ operations, or equivalently, $G_1$ operations in $\mathsf{FoldVerify}$ out as a standalone function. In detail, let $\mathsf{FoldVerify}$ additionally take some inputs that serve as advices, i.e., directly tell $\mathsf{FoldVerify}$ the output of the $G_1$ operations. Formally, let $\mathsf{FoldVerify'}$ and $\mathsf{EC}$ be two functions such that

$$
\mathsf{FoldVerify}(x)=1\Leftrightarrow\exists\mathsf{input},\mathsf{output}, \text{s.t. }\mathsf{FoldVerify}'(x,\mathsf{input},\mathsf{output})=1,\mathsf{EC}(\mathsf{input})=\mathsf{output}
$$

We then modify the previous NP relation to add $\mathsf{output}$ into the witness, and replace $\mathsf{FoldVerify}$ accordingly. Everything is equivalent to before, except that the group operations are singled out in $\mathsf{EC}$.

Next, instead of directly checking $\mathsf{EC}(\mathsf{input})=\mathsf{output}$ in the verification circuit, we replace it by its folding verification. In detail, we initialize another accumulator $U_{\mathsf{EC}}^{(0)}$ at the beginning of the IVC. The NP relation for $U_{\mathsf{EC}}$ is simpler than the one for IVC. It's just pure execution of $\mathsf{EC}$.

- Instance: $\mathsf{input}^{(k)},\mathsf{output}^{(k)}$
- Witness: $\perp$
- Logic:
  - $\mathsf{output}^{(k)}=\mathsf{EC}(\mathsf{input}^{(k)})$

Then, in the IVC relation, we replace $\mathsf{EC}(\mathsf{input})=\mathsf{output}$ with $\mathsf{FoldVerify}_{G_2}((\mathsf{input},\mathsf{output}),U_{\mathsf{EC}}^{(k-1)},U_{\mathsf{EC}}^{(k)},\pi_{\mathsf{EC}}^{(k)})$. This function consists of $G_2$ operations, i.e., $\mathbb{F}_p$ operations, which is native to our arithmetic circuit implementing the IVC.

Finally, to prevent the prover from switching the accumulator $U_{EC}^{(k)}$ to a different one, we can simply include this accumulator inside the instance of the IVC relation. The final NP relation is as follows, where the updates from the previous version is marked by bold font.

- Instance: $x^{(0)},x^{(k)},k,h^{(k-1)},\boldsymbol{U_{\mathsf{EC}}^{(k)}}$
- Witness: $\Pi^{(k-1)}=(U^{(k-1)},U^{(k-2)},\pi^{(k-1)})$, $x^{(k-1)},\boldsymbol{U_{\mathsf{EC}}^{(k-1)},\mathsf{input}^{(k)},\mathsf{output}^{(k)},\pi_{\mathsf{EC}}^{(k)}},h^{(k-2)}$
- Logic:
  - $k\geq 1$
  - $x^{(k)}=F(x^{(k-1)})$
  - $h^{(k-1)}=H(U^{(k-1)})$
  - $h^{(k-2)}=H(U^{(k-2)})$
  - If $k>1$, then
    - $\mathsf{FoldVerify}\boldsymbol{'}((x^{(0)},x^{(k-1)},k-1,h^{(k-2)}),U^{(k-2)},U^{(k-1)},\pi^{(k-1)},\boldsymbol{\mathsf{input}^{(k)},\mathsf{output}^{(k)}})=1$
    - $\boldsymbol{\mathsf{FoldVerify}_{\mathsf{EC}}(U_{\mathsf{EC}}^{(k-1)},(\mathsf{input}^{(k)},\mathsf{output}^{(k)}),U_{\mathsf{EC}}^{(k)},\pi_{\mathsf{EC}}^{(k)})}$
  - If $k=1$, then $x^{(k-1)}=x^{(0)}$

