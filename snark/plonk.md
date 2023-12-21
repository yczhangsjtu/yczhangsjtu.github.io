---
title: Introduction to PLONK
---

# Introduction to PLONK

This passage will introduce a zkSNARK called PLONK.

We compare PLONK with Groth16 and summarize their similarities and differences as follows:

- Similar to Groth16, PLONK works on arithmetic circuits.
- PLONK has constant verification time and constant proof size, which is as good as Groth16 theoretically, though Groth16 has better concrete efficiency.
- They both need trusted setup. However, the PLONK setup is universal, while the Groth16 setup is per-circuit.

## Polynomial Oracle Protocols

Before we introduce PLONK, we first introduce some tools. These tools are interesting on their own and some are used in many zkSNARKs.

The most important concept underlying all these tools is the polynomial oracle. Although this concept does not appear in the PLONK paper, the polynomial oracle makes describing the protocols much easier.

To understand polynomial oracles, consider the following scenario: Alice wants to know if a polynomial $f(X)$, say $X^{10000}+cX+d$, which has a short representation, but very high degree, can be factored into two polynomials of equal degree. But Alice has very weak computation power, so she asks Bob for help. Bob has large memory and strong computation power, and Bob answers: Yes, $f(X)$ can be factored into $a(X)\cdot b(X)$ each of degree no more than $5000$.

But Bob is potentially malicious and Alice cannot just trust Bob. So how does Alice be sure that the answer is indeed Yes?

It seems that the only way to convince Alice is that Bob sends the polynomials $a(X)$ and $b(X)$ to Alice. However, the polynomials $a(X)$ and $b(X)$ have too many coefficients that the Alice does not have enough memory to store them, let alone the computation power to compute their products.

So Bob takes out two magical boxes, and put $a(X)$ and $b(X)$ in each of them respectively.

![PLONK-AB](/assets/PLONK-AB-3991947.png)

Bob gives these two magical boxes to Alice. Alice picks a random number $z$, and asks the box that contains $a(X)$, what is the value of $a(z)$? Then asks the box that contains $b(X)$, what is the value of $b(z)$? After getting the answers, Alice computes $a(z)\cdot b(z)$ and compares the result with $f(z)$, which Alice is able to evaluate by herself. Since for two different polynomials, it is very unlikely that they evaluate to the same value at a random number, Alice is convinced that $a(X)\cdot b(X)$ is indeed $f(X)$.

![PLONK-Check](/assets/PLONK-Check.png)

You may already have figured out that the two magical boxes in the above scenario are just the polynomial oracles.

In summary, a polynomial oracles has the following properties:

1. Small, so that Alice can hold them with limited memory, but they can contain very long polynomials
2. When queried with any number, they can evaluate the polynomial they contain, and reply honestly

The polynomial oracles can achieve much more powerful scenarios than the above example. In fact, this is the fundamental tool working under almost all the zkSNARKs.

Do polynomial oracles really exist? NOT really. However, there is a cryptographic tool called **polynomial commitment** that achieves the same functionality.

A polynomial commitment is a cryptographic compression of the original polynomial. It looks like a purely random bit-string and Alice can learn almost nothing from the commitment.

Alice may ask Bob to evaluate this polynomial at any point. Bob computes the result and answers. They then engage in a protocol to check the answer. This protocol involves the commitment that Bob has sent Alice. The security of this polynomial commitment scheme ensures that if Bob lies, it is very unlikely that Bob passes the test.

Formally, a polynomial commitment scheme consists of the following algorithms and protocol:

1. **Setup**($N$): This algorithm outputs some public parameters that are used in the following algorithms. These parameters allow committing to polynomials up to degree $N-1$. The setup algorithm may require a trusted third party or not.
2. **Commit**($f(X),r$): This algorithm outputs a commitment to the polynomial $f(X)$ using randomness $r$
3. **Eval**($c_f,z,y;f(X),r$): In this protocol, the prover and the verifier both get input a commitment $c_f$ and a number pair $(z,y)$, the prover additionally learns about $f(X)$ and $r$ that are used to generate the commitment $c_f$. After the protocol, the verifier outputs $1$ or $0$ indicating if it believes that $y=f(z)$ or not

The left side of the following graph illustrates what is acutally going on when Bob sends a polynomial oracle to Alice.

![PLONK-PC](/assets/PLONK-PC-3992132.png)

Although it is the polynomial commitment sheme that is working under the hood, the abstraction of polynomial oracle is a convenient abstraction. This ideal world abstraction is called the **Polynomial Oracle Model**. In this model, we can directly say things like:

1. Bob sends a polynomial $a(X)$ to Alice
2. Alice queries for $a(z)$

Those words do not make sense in the real world. Remember that they are just convenient abstractions of a complex protocol.

We will not introduce how to construct a polynomial commitment scheme in this article. We will leave this to another article specially for introducing constructions of polynomial commitments.

### Polynomial Division

As a simple showcase and a very basic functionality, we demonstrate how does Alice check that $a(X)$ divides $b(X)$, given their polynomial oracles.

In fact, Alice cannot accomplish this task on her own. She needs the help of Bob who knows the complete values of $a(X)$ and $b(X)$. Bob's job is very simple: computes the quotient polynomial $q(X)=b(X)/a(X)$, and sends $q(X)$ to Alice. Alice checks that $b(X)=q(X)\cdot a(X)$ by checking if $b(z)=q(z)\cdot a(z)$ at a random $z$.

This method does not require Alice to trust Bob, because if $a(X)$ does not divide $b(X)$, such a polynomial $q(X)$ does not exist at all.

### Vector to Polynomial

Polynomial oracle is a powerful tool. In some sense, it allows Bob to "send" a large amount of information to Alice with very small cost.

Can we use a polynomial to encode other information, like a vector, so that Bob can "send" a very large vector to Alice?

A natural idea is directly using the polynomial coefficients to represent the vector. We call this method encoding the vector using the **monomial base**, because the resulting polynomial is the inner product of this vector and the monomials $(1,X,X^2,\cdots)$.

PLONK takes another encoding method, which we will focus in this article. Suppose we want to encode the vector $\vec{v}=(v_0,\cdots,v_{N-1})$ of length $N$ into a polynomial. First, select a domain $H=(z_0,\cdots,z_{N-1})$ of size $N$ and interpolate $v$ on this domain to get a polynomial $f(X)$ such that $f(z_i)=v_i$ for every $z_i\in H$. This polynomial interpolation method is also called the **Lagrange base** encoding, because the resulting polynomial is the inner product of $\vec{v}$ and the Lagrange base polynomials $(L_0(X),L_1(X),\cdots,L_{N-1}(X))$ where each $L_i(X)$ satisfies $L_i(z_i)=1$ for any $i$ and $L_i(z_j)=0$ for any $j\neq i$.

![PLONK-Vector-Encoding](/assets/PLONK-Vector-Encoding-3996953.png)

In conclusion, polynomial oracles allow Bob to "send" to Alice a vector encoded by a polynomial. Note that the "send" is quoted because Alice will not actually receive the complete information, but only query for small pieces of data related to them. As demonstrated in the next sections, these "small pieces of data" can be very useful. They allow Alice to check a wide range of properties of the encoded vector.

### Vector Equality

Now we introduce a method to check if two polynomials oracles $f(X)$ and $g(X)$ encode the same vector, i.e., $f(z)=g(z)$ for all $z$ in the interpolation domain $H$.

Note that this does not imply that $f(X)$ and $g(X)$ are the same polynomial, because there exist more than one interpolations of the same vector.

A naive method is to query these two polynomial oracles for all elements in $H$, but Alice does not have that much time.

In fact, $f(X)$ and $g(X)$ are interpolations of the same vector if and only if $f(X)-g(X)$ is an interpolation of the zero vector, that is equivalent to say, $f(X)-g(X)$ is divided by the vanishing polynomial $Z(X):=\prod_{z\in H}(X-z)$ over $H$.

However, Alice cannot let Bob send a polynomial oracle for $Z(X)$ because Alice cannot be sure that Bob is sending the correct polynomial. So Alice must evaluate $Z(X)$ herself. Therefore, we should pick $H$ with some special structure, such that $Z(X)$ has a short representation.

Therefore, Bob sends a polynomial oracle for $q(X)=(f(X)-g(X))/Z(X)$, and Alice checks for random $z$ that $q(z)\cdot Z(z)=f(z)-g(z)$, then Alice is convinced that $f(X)$ and $g(X)$ encode the same vector.

### Vector Product

Next, we will start building the core protocol underlying PLONK.

We first demonstrate a protocol for Alice to check that $\prod_{i=0}^{N-1}u_i\stackrel{?}{=}\prod_{i=0}^{N-1}v_i$ given the polynomial oracles $u(X)$ and $v(X)$ that encode $\vec{u}$ and $\vec{v}$ of size $N$ respectively. We assume for simplicity that all the $u_i,v_i$ are nonzero. This functionality will be a building block for more powerful tools underlying PLONK.

Again, Alice needs the help of Bob who knows the complete values of $\vec{u}$ and $\vec{v}$. Bob computes another vector $\vec{r}$ of size $N$ as follows:

1. $r_0=1$
2. For $i$ from $1$ to $N$, $r_i=r_{i-1}\cdot u_{i-1}/v_{i-1}$, where $r_N$ is an alias of $r_0$ for convenience.

![PLONK-Accumulator](/assets/PLONK-Accumulator-4261696.png)

Note that such $\vec{r}$ exists if and only if the equality $\prod_{i=0}^{N-1}u_i\stackrel{?}{=}\prod_{i=0}^{N-1}v_i$ is correct.

Bob encodes $\vec{r}$ into a polynomial oracle $r(X)$ and sends it to Alice.

Now Alice needs to check:

1. $r_0=1$
2. $r_{i+1}/r_i=u_i/v_i$ for every $i$ from $0$ to $N-1$

If we translate those into the language of polynomials (interpolated over domain $H=(z_0,z_1,\cdots,z_{N-1})$), we get

1. $r(z_0)=1$
2. $r(z_{i+1})\cdot v(z_i)=u(z_i)\cdot r(z_{i})$ for every $i$ from $0$ to $N-1$, where $z_N$ is an alias of $z_0$

Alice checks the first condition straightforwardly by querying $z(X)$ with $z_0$.

To check the second condition without looping through the domain $H$, we pick $H$ with special structure. Specifically, let $H=\{1,g,g^2,g^3,\cdots,g^{N-1}\}$, i.e., a cyclic group generated by some generator $g$. Therefore, $z_i=z_{i-1}\cdot g$ for every $i$. The second relation becomes

$$
r(z\cdot g)\cdot v(z)=u(z)\cdot r(z)\quad\forall z\in H
$$

Now the problem reduces to the vector equality problem described previously. Alice simply checks that the polynomial $r(X\cdot g)\cdot v(X)-u(X)\cdot r(X)$ encodes the zero vector by checking its divisibility by $Z(X)$.

> Note that if the polynomial is over the field of real numbers or rational numbers, $H$ would never have this property. However, in zkSNARKs we usually deal with finite fields, in which we can find such cyclic subgroups.

![PLONK-Vector-Product](/assets/PLONK-Vector-Product.png)

### Polynomial Permutations

Now, Alice has two polynomial oracles, $a(X)$ and $b(X)$, encoding $\vec{a}$ and $\vec{b}$ respectively. Then Bob claims that $\vec{a}$ and $\vec{b}$ are permutations of each other. We call such $\vec{a}$ and $\vec{b}$ a "permutation pair". How does Alice check that $\vec{a}$ and $\vec{b}$ form a permutation pair?

Thie following figure shows a simple example where both polynomials encode a permutation of $(1,1,2,3,5,6)$.

![PLONK-Poly-Permutation](/assets/PLONK-Poly-Permutation-4041460.png)

By the protocol in the last section, Alice is able to verify $\prod_{i=0}^{N-1} a_i=\prod_{i=0}^{N-1} b_i$. How can she exploit this capability?

Obviously, if $\vec{a}$ and $\vec{b}$ are a permutation pair, their products are equal. However, this is obviously insufficient.

Note that if we simultaneously add any number $\gamma$ to $\vec{a}$ and $\vec{b}$, they are still a permutation pair, and their products are still equal, i.e., $\prod_{i=0}^{N-1} (a_i+\gamma)=\prod_{i=0}^{N-1} (b_i+\gamma)$ for any $\gamma$.

This check is sufficient, because if $\vec{a}$ and $\vec{b}$ are not permutations of each other, it's very unlikely that their products are equal after shifted by a random $\gamma$.
$$
6\Leftarrow(1,2,3)\stackrel{\pm 4}{\leftrightarrow}(5,6,7)\Rightarrow 210\\
6\Leftarrow(3,1,2)\stackrel{\pm 4}{\leftrightarrow}(7,5,6)\Rightarrow 210\\
6\Leftarrow(1,1,6)\stackrel{\pm 4}{\leftrightarrow}(5,5,10)\Rightarrow 250
$$
Therefore, Alice may sample a random $\gamma$ and sends it to Bob. After that, Alice treats the polynomial oracles $a(X)$ and $b(X)$ as if they were $a(X)+\gamma$ and $b(X)+\gamma$ respectively, by adding $\gamma$ to the whatever the polynomial oracles reply. They then apply the checking product protocol to $a(X)+\gamma$ and $b(X)+\gamma$ instead.

![PLONK-Permutation-Pair](/assets/PLONK-Permutation-Pair.png)

Now Alice is able to check two vectors $\vec{a}$ and $\vec{b}$ are permutations of each other, whatever the permutation is. However, what if Alice wants to make sure that $\vec{b}=\sigma(\vec{a})$ for a particular permutation $\sigma$? We call such $\vec{a},\vec{b}$ a $\sigma$-pair, which is a stronger concept that "permutation pair". How does Alice check that?

Before we introduce the protocol, we first explain how does Alice even "know" $\sigma$ in the first place. Remember that all the vectors have very large size such that Alice is incapable of handle them in memory. Obviously, the permutation $\sigma$ would contain approximately the same amount of information. So, Alice must also access $\sigma$ in a polynomial oracle.

What does that even mean? This $\sigma$ is a permutation, not a vector.

However, we can encode the information of $\sigma$ into a vector. We just need to apply $\sigma$ to a standard vector, say $\vec{t}=(0,1,2,\cdots,N-1)$, and encodes both $\vec{t}$ and $\vec{s}=\sigma(\vec{t})$ into polynomial oracles $t(X),s(X)$. Now this pairs of polynomial oracles encodes the complete information of $\sigma$.

![PLONK-Encode-Permutation](/assets/PLONK-Encode-Permutation.png)

How does Alice obtain the polynomial oracle for $t(X),s(X)$ in the first place? It shouldn't be from Bob, because it's very easy for Bob to cheat by giving Alice a completely different permutation $\sigma'$. Therefore, Alice must obtain $t(X),s(X)$ from somewhere that she trusts. We will later see that in PLONK, this $\sigma$ is determined by the circuit, so $t(X)$ and $s(X)$ can be preprocessed by an (untrusted) third party and put in a public website so that everyone with enough computation power can check them. We call them preprocessed public polynomials.

So now Alice has both $t(X)$ and $s(X)$ that encode $\vec{t}$ and $\vec{s}=\sigma(\vec{t})$ respectively. How to make use of them to check that $\vec{a}$ and $\vec{b}$ is a $\sigma$-pair?

Note that $(\vec{t},\vec{s})$ is assured to be a $\sigma$-pair. If we linearly combine two $\sigma$-pairs by a random factor $\beta$, we get $(\vec{a}+\beta\vec{t},\vec{b}+\beta\vec{s})$ which is also a $\sigma$-pair, and thus a permutation pair. So Alice can check this with the previous protocol. This check is sufficient, because if $(\vec{a},\vec{b})$ is not a $\sigma$-pair, adding a random multiple of $(\vec{t},\vec{s})$ to it will most likely result in a vector pair that is not a permutation pair.
$$
\begin{matrix}
(4,5,6)&(6,4,5)&(6,5,4)\\
+ & + & +\\
3\times(1,2,3)&3\times(3,1,2)&3\times(3,1,2)\\
= & = & =\\
(7,11,15)&(15,7,11)&(15,8,10)
\end{matrix}
$$
To summarize, Alice samples a random $\beta$, and sends $\beta$ to Bob. Then they apply the previous protocol to check that $(\vec{a}+\beta\vec{t},\vec{b}+\beta\vec{s})$ is a permutation pair.

![PLONK-Sigma-Pair](/assets/PLONK-Sigma-Pair-4299268.png)

### Identical Elements

Finally, we come to the ultimate protocol that PLONK directly relies on: the ability to check that specific elements in $\vec{v}$ are identical.

For example, Alice has a polynomial oracle that encodes $\vec{v}=(v_0,v_1,v_2,v_3,v_4,v_5,v_6)$, and Alice needs to check if $v_0=v_1=v_3=v_6$ and $v_2=v_4$.

We can formalize this problem by the concept of partition over the integer set $\{0,1,\cdots,N-1\}$. Let a partition be $\pi=\{S_1,S_2,S_3\}=\{\{0,1,3,6\},\{2,4\},\{5\}\}$. Then Alice wants to check that for each partition $S\in\pi$, the elements of $\vec{v}$ in this partition are identical. For convenience, we say such a $\vec{v}$ is a "nice vector" under this partition.

How is this related to permutations?

If we draw an arrow from each $i$ to $\sigma(i)$, a permutation can be visualized as follows.

![cb9f641a3790874ea612500a8f48ca86.png](/assets/fbca5f5468094f71a2b6197c72341dac.png)

Starting from any number, say $i$, and follow the arrows, we will eventually go back to $i$, and obtain a cycle. All the cycles form a partition over $\{0,1,2,\cdots,N-1\}$. We call this the "Cycle Partition" of $\sigma$. On the other hand, for any partition $\pi$, it is easy to find at least one permutation $\sigma$ whose cycle partition is exactly $\pi$.

![5e55073ba4aa4f5d20f7cde9a77cfb62.png](/assets/d514b4bc1f9746558d3c4265e2a6f047.png)

Note that if $\vec{v}$ is a "nice vector" under the cycle partition of $\sigma$ if and only if $\sigma(\vec{v})=\vec{v}$, as demonstrated in the following figure. Note that the color of the elements in the same partition is identical, and when we apply the above permutation to this vector, the colors stay the same.

![PLONK-Static](/assets/PLONK-Static.png)

Alice is able to check $\sigma(\vec{v})=\vec{v}$ using the protocol described in the last section, as long as Alice has access to polynomial oracles $t(X)$ and $s(X)$ that encodes the permutation $\sigma$. The only difference is that now $\vec{a}$ and $\vec{b}$ are the same vector $\vec{v}$, which does not make much difference.

## The Problem

Having introduced the tools, now we briefly recall the problem that PLONK and other zkSNARKs are trying to solve.

PLONK deals with computations represented by arithmetic circuits. An arithmetic circuit consists of *addition gates*, *multiplication gates*, and *constant gates*. An example is as follows

![08ac8d7b30a8c9bb930bcc1da6034d9d.png](/assets/b62de97be73c491eb6f33dea421330ec.png)

For the input wires $x_1,\cdots,x_n$ and output wires $y_1,\cdots,y_m$, we say $x_i$ or $y_j$ is public input/output if its value can be publicly known.

Given the values of the public inputs and outputs, the goal of PLONK, as well as any circuit-based zkSNARK, is to produce a proof for the fact that these values are part of a consistent computation.

## Math Formuation

To formulate the above statement as a math problem, let's assign three variables to each gate. Let each gate have an index. Then we assign $a_i$ to the left input of gate $i$, $b_i$ to the right input, and $c_i$ to the output of this gate, as illustrated in the following picture.

![a05d9511e3bcdc0107240b572b1295bd.png](/assets/acc4bbe7095a456e81e5ce1a68001478.png)

Intuitively, an assignment to these variables represents a consistent computation if and only if the following conditions are satisfied:

1. **The arithmetic condition**: for each gate, let the index be $i$, then
	1. $a_i+b_i=c_i$ if it is an addition gate
	2. $a_i\cdot b_i=c_i$ if it is a multiplication gate
	3. $c_i=C$ if it is a constant gate with constant $C$
2. **The public input-output condition**: for each gate, let the index be $i$, then
	1. If the left input of this gate is a public value $x$ of this circuit, then $a_i=x$
	2. If the right input of this gate is a public value $x$ of this circuit, then $b_i=x$
	3. If the output of this gate is a public value $y$ of this circuit, then $c_i=y$
3. **The copy condition**: if two variables are connected in the circuit, their values should equal.

Let's deal with these conditions one by one.

### Arithmetic Condition

For the first condition, note that all the subconditions can be unified in a single formulation.

$$
a_i\cdot L_i+b_i\cdot R_i+c_i\cdot O_i+a_i\cdot b_i\cdot M_i+C_i=0
$$

By different choices of $L_i,R_i,O_i,M_i,C_i$, the above formulation can express different arithmetic restrictions.

1. $(1,1,-1,0,0)\Rightarrow a_i+b_i-c_i=0$
2. $(0,0,1,-1,0)\Rightarrow c_i-a_i\cdot b_i=0$
3. $(0,0,1,0,-C)\Rightarrow c_i-C=0$

Therefore, the logic of gate $i$ is completely described by the choice of $(L_i,R_i,O_i,M_i,C_i)$. For simplicity, we call this tuple the description of a gate.

Now we formulate vectors $\vec{a},\vec{b},\vec{c},\vec{L},\vec{R},\vec{O},\vec{M},\vec{C}$ where $\vec{a}=(a_0,\cdots,a_{N-1})$ and so on.

To make use of the tools we introduced previously, let Bob put all these vectors into polynomial oracles after encoding them into polynomials $a(X),b(X),c(X)$ and preprocessed public polynomials $L(X),R(X),O(X),M(X),C(X)$.

Now, Alice checks that
$$
a(X)\cdot L(X)+b(X)\cdot R(X)+c(X)\cdot O(X)+a(X)\cdot b(X)\cdot M(X)+C(X)=0\quad(\text{over } H)
$$

Recall that Alice only needs to check that the left-hand-side polynomial is divided by $Z(X)=\prod_{z\in H}(X-z)$.

### Public Input-output Condition

For the second condition, let's add some special gates to the circuit, illustrated as follows.

![65ed4af8ba9baa6709d210ddd6beaac8.png](/assets/140e7190bb464e768a10653b09d0820c.png)

For the gate corresponding to public input $x_k$, the description is $(0,0,1,0,-x_k)$. For the gate corresponding to public output $y_k$, the description is $(1,0,0,0,-y_k)$.

However, these special gates no longer have constant descriptions. Their $C_i$'s depend on the public input/output. As a result, they will cause the polynomial $C(X)$ defined in the last section depend on the input and output, and thus cannot be precomputed just for the circuit.

To resolve this issue, we divide $C(X)$ into two parts: the first part is still denoted by $C(X)$, which is the part that depends only on the circuit and can be publicly preprocessed. The other part depend only on the public input/output, denoted by $io(X)$. Note that $io(X)$ has a short representation and Alice can simply evaluate it by herself.

### Copy Condition

Finally, let's deal with the last condition, variables connected by wires should be identical. This constraint depends only on the circuit structure.

![PLONK-Wires](/assets/PLONK-Wires.png)

To deal with the copy condition, recall that we described a protocol about how to check specific elements in a vector $\vec{v}$ are identical. The situation here is more complex: we need to check identical elements across three vectors $\vec{a}$, $\vec{b}$ and $\vec{c}$ instead of one. However, the idea is the same.

First, let $\vec{v}=(\vec{a},\vec{b},\vec{c})$ be the concatenation of three vectors. Then $\vec{v}$ has length $3N$.

Let $\sigma$ be the permutation over $[3N]$, such that any two elements in $\vec{v}$ are in the same cycle of $\sigma$ if and only if they are connected by wires directly or indirectly.

We apply $\sigma$ to $\vec{t}=(0,1,2,\cdots,3N-1)$ to get $\vec{s}$ which is also of size $3N$.

Then we split both $\vec{t}$ and $\vec{s}$ into three vectors, $\vec{t}_1,\vec{t}_2,\vec{t}_3$ and $\vec{s}_1,\vec{s}_2,\vec{s}_3$, each of size $N$.

Note that these six vectors depend only on the circuit structure, so their polynomial oracles $T_1(X),T_2(X),T_3(X)$ and $S_1(X),S_2(X),S_3(X)$ can be prepared in advance.

We will not explain step by step, but the end result is that Alice checks $\prod_{z\in H}f(z)=\prod_{z\in H}g(z)$ where
$$
f(X)=(a(X)+\beta T_1(X)+\gamma)\cdot (b(X)+\beta T_2(X)+\gamma)\cdot (c(X)+\beta T_3(X)+\gamma)\\
g(X)=(a(X)+\beta S_1(X)+\gamma)\cdot (b(X)+\beta S_2(X)+\gamma)\cdot (c(X)+\beta S_3(X)+\gamma)
$$
This check works exactly the same as we discussed previously, i.e., Bob computes and sends an accumulator polynomial $r(X)$ and so on.

### Final Math Formulation

In summary, the original problem "given a circuit $C$ and some public inputs/outputs, prove that there exists a valid assignment to this circuit" is transformed into the following equivalent problem.

Given polynomials $L(X),R(X),O(X),M(X),C(X),Z(X),T_1(X),T_2(X),T_3(X),S_1(X),S_2(X),S_3(X)$ that depend only on the circuit, and $io(X)$ that depend only on the public inputs/outputs, there exist polynomials $a(X),b(X),c(X)$ such that:
$$
a(X)\cdot L(X)+b(X)\cdot R(X)+c(X)\cdot O(X)+a(X)\cdot b(X)\cdot M(X)+C(X)+io(X)\text{ is divided by }Z(X)
$$
and for random $\beta,\gamma$ there exists $r(X)$ such that
$$
\begin{eqnarray}
r(X\cdot g)&\cdot&(a(X)+\beta S_1(X)+\gamma)(b(X)+\beta S_2(X)+\gamma)(c(X)+\beta S_3(X)+\gamma)-\\
r(X)&\cdot&(a(X)+\beta T_1(X)+\gamma)(b(X)+\beta T_2(X)+\gamma)(c(X)+\beta T_3(X)+\gamma)
\end{eqnarray}
\text{ is divided by }Z(X)
$$

## The PLONK Protocol

Having polynomial commitment, we are now ready to formally present the PLONK protocol.

For simplicity, we will first present PLONK without the zero-knowledge property. Then we will briefly explain how to make the scheme zero-knowledge.

This is a simplified version of PLONK. For more details please refer to the original paper.

### Interactive Non-ZK Version

Different from Groth16, PLONK supports a universal trusted setup that is independent from any circuit. So the setup phase is splitted into two parts: the universal setup, which we just call *setup*, and the circuit-specific setup, which we call *index*. The *index* algorithm does not need a trusted third party.

**Setup**($B$, $n$): the **Setup** algorithm of PLONK invokes the Setup algorithm of the polynomial commitment scheme with maximal degree $N=B+n$. The public parameters will enable us to prove statements for any circuits of size bounded by $B$, with no more than $n$ public values. Because PLONK uses the KZG polynomial commitment scheme, this procedure requires a trusted third party.

**Index**($C$): compute the polynomials $L(X),R(X),O(X),M(X),C(X),Z(X),T_1(X),T_2(X),T_3(X),S_1(X),S_2(X),S_3(X)$ according to the circuit $C$, then invokes the **Commit** algorithm of the polynomial commitment scheme to generate commitments for these polynomials $c_L,c_R,c_O,c_M,c_C,c_Z,c_{T_1},c_{T_2},c_{T_3},c_{S_1},c_{S_2},c_{S_3}$. These commitments use public randomnesses so that anyone can validates them.

**Prove**($index,a_0,\cdots,a_{N-1},b_0,\cdots,b_{N-1},c_0,\cdots,c_{N-1}$) $\leftrightarrow$ **Verify**($index$, $x_0,\cdots,x_{n-1}$):

**Round 1 (Prover)**: the prover interpolates polynomials $a(X),b(X),c(X),C_I(X)$ from a valid assignment to the circuit and computes
$$
g(X)=\frac{a(X)\cdot L(X)+b(X)\cdot R(X)+c(X)\cdot O(X)+a(X)\cdot b(X)\cdot M(X)+C(X)+io(X)}{Z(X)}
$$
The prover commits to the polynomials $a(X),b(X),c(X),g(X)$ and gets $c_a,c_b,c_c,c_g$. The prover sends $c_a,c_b,c_c,c_g$ to the verifier.

**Round 1 (Verifier)**: the verifier samples $z$ and checks that
$$
g(z)Z(z)=a(z)\cdot L(z)+b(z)\cdot R(z)+c(z)\cdot O(z)+a(z)\cdot b(z)\cdot M(z)+C(z)+io(z)
$$
by querying the committed polynomials from the prover and running the **Eval** protocol of the polynomial commitment scheme. Note that the verifier does not have a commitment to $io(z)$, however, the verifier can evaluate $io(z)$ efficiently by itself, by computing $\sum_{i=0}^{n-1} x_i L_{N-n+i}(z)$ where $L_i(X)$ is the Lagrange base polynomial that is $1$ at $g^i$ and $0$ elsewhere in the domain $H$. Since $H$ is a multiplicative group, $L_i(X)=C_i\cdot\frac{X^N-1}{X-g^i}$ for some constant $C_i$ which can be precomputed.

The verifier samples $\beta$ and $\gamma$ and sends to the prover.

**Round 2 (Prover)**: The prover computes $\vec{r}$ such that $r_0=1$ and $r_i/r_{i-1}=\frac{\tilde{a}(z_{i-1})\tilde{b}(z_{i-1})\tilde{c}(z_{i-1})}{\hat{a}(z_{i-1})\hat{b}(z_{i-1})\hat{c}(z_{i-1})}$, and interpolates $\vec{r}$ to get $r(X)$. The prover then computes
$$
h(X)=\frac{\begin{matrix}r(X\cdot g)\cdot(a(X)+\beta S_1(X)+\gamma)(b(X)+\beta S_2(X)+\gamma)(c(X)+\beta S_3(X)+\gamma)-\\
r(X)\cdot(a(X)+\beta T_1(X)+\gamma)(b(X)+\beta T_2(X)+\gamma)(c(X)+\beta T_3(X)+\gamma)\end{matrix}}{Z(X)}
$$
The prover computes commitments $c_r$ and $c_h$ to these polynomials and sends $c_r$ and $c_h$ to the verifier.

**Round 2 (Verifier)**: The verifier samples $w$ and checks that
$$
\begin{eqnarray*}
h(w)Z(w)=&&r(w\cdot g)\cdot(a(w)+\beta S_1(w)+\gamma)(b(w)+\beta S_2(w)+\gamma)(c(w)+\beta S_3(w)+\gamma)-\\
&&r(w)\cdot(a(w)+\beta T_1(w)+\gamma)(b(w)+\beta T_2(w)+\gamma)(c(w)+\beta T_3(w)+\gamma)
\end{eqnarray*}
$$
by querying the committed polynomials from the prover and running the **Eval** protocol of the polynomial commitment scheme.



For now, this protocol is not zero-knowledge, because it reveals to the verifier some information of the polynomials $a(X),b(X),c(X),r(X)$, i.e., the evaluation of them at some random points. Next, we will modify the above protocol to make it zero-knowledge.

### Add Zero-Knowledge

To make the above protocol zero-knowledge, we need to rerandomize the witness polynomials.

Sample random numbers $u_1,u_2,\cdots,u_9$, and replace the original polynomials as follows.

Replace $a(X),b(X),c(X)$ with
$$
a'(X)=a(X)+Z(X)\cdot(u_1X+u_2)\\
b'(X)=b(X)+Z(X)\cdot(u_3X+u_4)\\
c'(X)=c(X)+Z(X)\cdot(u_5X+u_6)
$$
This does not affect the divisibility of the above two equations by $Z(X)$. So the prover can compute $g'(X)$ according to the updated witness polynomials.

Similarly, in the second round, the prover replaces $r(X)$ by $r'(X)=r(X)+Z(X)\cdot(u_7X^2+u_8X+u_9)$ and computes $h'(X)$ accordingly. We will not describe the complete updated protocol.

### Make it Non-Interactive

It is straightforward to transform the above interactive protocol into a non-interactive scheme by applying the Fiat-Shamir heuristic. Intuitively, the Fiat-Shamir heuristic works as follows: the non-interactive prover does all the works of the interactive prover, and whenever the prover needs the verifier message, the prover simulates it by putting all the prover messages so far (together with the instance) into a hash function, and use the hash value as the verifier message. Finally, the prover bundles all the prover messages generated during the execution in a string and outputs that as the non-interactive proof string.

## Conclusion

In this article, we explained the mechanism behind the PLONK zkSNARK scheme. We first introduced polynomial oracles and the methods about checking permutations in the polynomial oracle model. We then described how PLONK transforms the circuit satisfaction problem into a math problem consisting of polynomial identities. Finally, we presented the complete PLONK protocol.

