# Use Hybrid Argument to Prove Indistinguishability

Suppose we have a PRG (Pseudo Random Generator), which given an $n$ bit input drawn from $U_n$ (uniform distribution over $\{0,1\}^n$), generates an $n+1$ bit output, which is $\epsilon(n)$-indistinguishable from $U_{n+1}$ by $t(n)$-time distinguisher.

We want to build another PRG, which could generate more pseudo-random outputs, based on the one we have, which could only expand the input by 1 bit.

The idea is simple. Let the input be $s_0$, and $g$ be the PRG we have. Denote $g(s_i) = \{s_{i+1},r_{i+1}\}$ where $r_{i+1}$ is one bit. We repeat this process on $s_i$ for $q$ times to get $r_1,r_2,\cdots,r_q$, and the final $s_q$. We take the combination $\{r_1,\cdots,r_q,s_q\}$ as the output of the longer PRG, and want to prove that it is still secure.

Denote this new PRG by $g^q$. Now we want to prove that this $g^q$ is $q\epsilon(n)$-indistinguishable from uniform distribution over $\{0,1\}^{n+q}$ by $(t(n)-q\cdot\mathbf{poly}(n))$-time distinguisher.

## Hybrid Argument

We use **hybrid argument** as the basic idea for our proof. Specificly, we define the following **hybrid distributions**:

$$\begin{eqnarray*}
H_0 &=& g^q(U_n) \\
H_1 &=& \{g^{q-1}(U_n),U_1\} \\
H_2 &=& \{g^{q-2}(U_n),U_2\} \\
&\vdots& \\
H_{q-1} &=& \{g(U_n),U_{q-1}\} \\
H_q &=& U_{n+q}
\end{eqnarray*}$$

We prove the following chain of propositions:

1. For any distinguisher $$ D $$ of running time $t(n)-q\cdot\mathbf{poly}(n)$, $H_0$ is $q\epsilon(n)$-indistinguishable from $H_q$, because
2. For any distingsuiher $D$ of running time $t(n)-q\cdot\mathbf{poly}(n)$, $H_i$ is $\epsilon(n)$-indistinguishable from $H_{i+1}$, $\forall 0\leq i\leq q-1$, because if not
3. We can build a $t(n)$-time distinguisher $D'$ which distinguishes $g(U_n)$ from $U_{n+1}$ by probability $\epsilon(n)$, contradicting to the assumption of $g$.

We need to prove 3 and 3->2, 2->1.