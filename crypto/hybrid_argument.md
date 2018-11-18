---
title: Hybrid Argument in Security Proof
layout: math
---

# Hybrid Argument in Security Proof

We show an example of how to use **hybrid argument** in security proof. First, we brief the problem of constructing a $q$-bit RPG from a $1$-bit RPG. Then we propose a method solving this problem. Finally, we outline how to use hybrid argument to prove that our method is secure.

Suppose we have a PRG \(Pseudo Random Generator\), denoted by $g: \\{0,1\\}^n\to\\{0,1\\}^{n+1}$, such that when $x$ is drawn from $U\_n$ \(uniform distribution over $\\{0,1\\}^n$\), $g(x)$ is $\epsilon(n)$-indistinguishable from $U\_{n+1}$ by any distinguisher within time $t(n)$. Therefore, $g$ extends random source by $1$ bit. We want to build another PRG based on $g$, which extends the input by $q$ bits.

The solution is simple. Let the input be $s\_0$, and $\\{s\_{i+1},r\_{i+1}\\}:=g(s\_i)$ for $i=0,\cdots,q-1$, where $r\_{i+1}$ is one bit. We take the combination $\\{r\_1,\cdots,r\_q,s\_q\\}$ as the output. Denote this new PRG by $g^q$.

Now we want to prove that this $g^q$ is $q\epsilon(n)$-indistinguishable from uniform distribution over $\\{0,1\\}^{n+q}$ by $(t(n)-q\cdot\mathbf{poly}(n))$-time distinguisher. We accomplish this by **hybrid argument**. Specificly, we define the following **hybrid distributions**:

$$\begin{eqnarray*}
H_0 &=& g^q(U_n) \\
H_1 &=& \{g^{q-1}(U_n),U_1\} \\
H_2 &=& \{g^{q-2}(U_n),U_2\} \\
&\vdots& \\
H_{q-1} &=& \{g(U_n),U_{q-1}\} \\
H_q &=& U_{n+q} \end{eqnarray*}$$

We prove the following chain of propositions:

1. For any distinguisher $$ D $$ of running time $t(n)-q\cdot\mathbf{poly}(n)$, $H\_0$ is $q\epsilon(n)$-indistinguishable from $H\_q$, because
2. For any distingsuiher $D$ of running time $t(n)-q\cdot\mathbf{poly}(n)$, $H\_i$ is $\epsilon(n)$-indistinguishable from $H\_{i+1}$, $\forall 0\leq i\leq q-1$, because if not
3. We can build a $t(n)$-time distinguisher $D'$ which distinguishes $g(U\_n)$ from $U\_{n+1}$ by probability $\epsilon(n)$, contradicting to the assumption of $g$.

We need to prove:

1. The third proposition is correct;
2. If the third proposition is correct, then the second is correct;
3. If the second proposition is correct, then the first is correct.

And the first proposition is our statement.

In this brief introduction of hybrid argument, we introduced the problem of extending $1$-bit PRG to $q$-bit, provided a solution, and finally presented how to use hybrid argument to outline the security proof of our solution.
