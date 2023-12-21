---
title: Introduction to Groth16
layout: math
---

# Groth16

Groth16 is one zkSNARK that is most widely deployed in practice. This is the zero-knowledge proof scheme that is employed in Zcash. It also has the most efficient verifier and the shortest proof among all SNARKs. 

The most significant drawback of Groth16 is the need of trusted setup for each circuit.

In short, a zkSNARK solves the following problem of "computation verification".

## Computation Verification Problem

Every computation can be represented by an arithmetic circuit. An arithmetic circuit consists of addition, multiplication gates, and possibly constant gates, like the following example

![Groth16-Circuit.png](/assets/Groth16-Circuit.png)

The above example of arithmetic circuit consists of 15 gates. This circuit represents a computation that takes as input five numbers $x_1$ to $x_5$, and outputs four numbers.

An arithmetic computes on numbers instead of 0s and 1s in a boolean circuit.

Given a set of inputs, one must compute the output of every gate, to finally obtain the output of the entire circuit. In this example, given inputs $(1,2,3,4,5)$, the output $(-27, 14, 80, 171)$ is obtained after computing all the gates in the entire circuit, as is shown in the following picture.

![Groth16-Assignment.png](/assets/Groth16-Assignment.png)

On the other hand, if a verifier is given both the input and output of a computation, how can the verifier make sure that the output is indeed the result of a correct computation? In this example, the task of the verifier is "verify that on input $(1,2,3,4,5)$, the output of this circuit is indeed $(-27,14,80,171)$". A simple and naive way is to recompute the output, i.e. compute the outputs of all the internal gates. This way of verification is **inefficient**.

![Groth16-Verification.png](/assets/Groth16-Verification.png)

The efficiency is not the only problem. Sometimes the input may contain some **secret data**, which the verifier is not expected to learn. In this example, the $(3,4,5)$ part of the input is secret. The verifier only learns the $(1,2)$, and the task becomes "verify that it is possible for the circuit to output $(-27,14,80,171)$ when the first two inputs are $(1,2)$". In this case, even the naive recomputing doesn't work. So, how can the verifier validate the computation result?

![Groth16-ZK.png](/assets/Groth16-ZK.png)

## From Circuit to R1CS

A zkSNARK allows a prover to generate a short proof for the computation result. The verifier doesn't need to learn the secret inputs, neither recompute the entire circuit. Instead, the verifier only needs to verify the short proof.

There are many constructions of zkSNARK, and Groth16 is one most popular. We will introduce the ideas behind the Groth16 construction.

To begin with, let's reexamine the task of the verifier. Take a careful look at this problem: given that the first two inputs are $(1,2)$, and the outputs are $(-27,14,80,171)$, the verifier wants to know if the outputs are correct, given appropriate secret inputs.

The above problem, is actually equivalent to the following restatement: given the circuit, in which some of the positions have been filled, can you find a way to fill in the question marks (?) such that the logic of each gate is respected?

From this new point of view, the computation verification problem is transformed into a "fill in the blank" problem that somehow resembles Sudoku.

Next, let's give this "fill in the blank" problem a more mathematical formulation. We view each blank as a variable and give it a name, as illustrated as follows

![Groth16-Variables.png](/assets/Groth16-Variables.png)

We name the variables whose values are already known by $x_i$'s and the blanks that we need to fill in by $w_i$'s. Now the above "fill in the blank" problem is equivalent to the following system of equations

$$
\begin{array}{rcl}
w_4 &=& x_1+x_2 \\
w_5 &=& x_2\times w_1 \\
w_6 &=& x_2\times w_2 \\
&\cdots& \\
x_3 &=& w_8\times w_9 \\
x_4 &=& w_9+5 \\
x_5 &=& 5+w_{10} \\
x_6 &=& w_{10}\times w_{11} \\
\end{array}
$$

Each equation corresponds to a gate in the circuit.

Finally, we reformulate the above equations by matrices and vectors. To accomplish this, we view each equation as in the form $*=*\times *$. Note that not all the right hand sides contain multiplications. We unify them by viewing them as multiplying one.

$$
\begin{array}{rcl}
w_4&=&(x_1+x_2)\times 1 \\
w_5 &=& x_2\times w_1 \\
w_6&=&x_2\times w_2 \\
&\cdots& \\
x_3&=&w_8\times w_9 \\
x_4&=&(w_9+5)\times 1 \\
x_5&=&(5+w_{10})\times 1 \\
x_6&=&w_{10}\times w_{11} \\
\end{array}
$$

Now each equation is in the form $*=*\times *$. Let the vector $\vec{z}=(1,x_1,x_2,\cdots,w_{11})$ consist of all the variables and the constant $1$. Then each "$*$" is a linear combination of $\vec{z}$ by some constant coefficients. In another word, each "$*$" is the inner product of $\vec{z}$ with a constant vector. For example, $x_1+x_2=\langle (0,1,1,0,\cdots,0),\vec{z}\rangle$. Therefore, each equation is in the form of $\langle\vec{c}_i,\vec{z}\rangle=\langle\vec{a}_i,\vec{z}\rangle\times \langle\vec{b}_i,\vec{z}\rangle$ for some constant vectors $\vec{a}_i$, $\vec{b}_i$ and $\vec{c}_i$.

Put all the equations together, we get a matrix formulation of the equation as $A\vec{z}\circ B\vec{z}=C\vec{z}$, where matrix $A$ consists of all the $\vec{a}_i$ as row vectors, similarly for matrices $B$ and $C$. The $\circ$ denotes entrywise multiplication of vectors. The values of these matrices depend only on the structure of the circuit.

With all the above efforts, we transform the original circuit into this math equation, which is called **Rank-1 Constraint System (R1CS)**.

> The reason for the "Rank-1" in the name, if you are interested, can be explained as follows. The right hand side of each equation $\langle\vec{c}_i,\vec{z}\rangle=\langle\vec{a}_i,\vec{z}\rangle\times \langle\vec{b}_i,\vec{z}\rangle$ can be rewritten as $\vec{z}^T(\vec{a}_i\vec{b}_i^T)\vec{z}$. Then the $\vec{a}_i\vec{b}_i^T$ is a square matrix that has rank only $1$.

To summarize, in this section we transformed the computation verification problem into a R1CS problem. In the computation verification problem, the verifier is given the circuit outputs, and part of the inputs, and tries to verify the output is valid. In the R1CS problem, the verifier is given a prefix of the vector $\vec{z}$, and decides if there exists an assignment to the rest of the vector so that $\vec{z}$ forms a solution to the equation $A\vec{z}\circ B\vec{z}=C\vec{z}$.

![ZKP-R1CS-NP.png](/assets/ZKP-R1CS-NP.png)

## From R1CS to QAP

The R1CS problem is the starting point of many zkSNARK constructions. Most zkSNARKs further transform it into an equation of polynomials. The polynomial equation of Groth16 is called the **Quadratic Arithmetic Programming (QAP)** problem.

Here we introduce how to transform a R1CS problem into an equivalent QAP problem.

First, notice that the matrix-vector multiplication can be viewed as a linear combination of the column vectors of the matrix by the vector. From this point of view, the R1CS problem can be viewed as deciding the existence of a set of linear combination coefficients, such that the linear combination of column vectors of $A$, times the linear combination of column vectors of $B$, equals that of $C$.

![Groth16-R1CS.png](/assets/Groth16-R1CS.png)

Next, we replace these column vectors by polynomials, such that the resulting polynomial equation is equivalent to the original equation. The polynomials are obtained by interpolating the column vectors.

### Polynomial Interpolation

The polynomial interpolation is a method to transform a vector, say $\vec{v}=(v_1,\cdots,v_d)$, into a polynomial $f(X)$, such that the evaluation of $f(X)$ at the set $\{r_1,\cdots,r_d\}$ is the same as $\vec{v}$, i.e. $v_1=f(r_1),\cdots,v_d=f(r_d)$. We say $f(X)$ is an interpolation of $\vec{v}$ over the **domain** $\{r_1,\cdots,r_d\}$.

Given a domain $\{r_1,\cdots,r_d\}$, the vector $\vec{v}$ can be interpolated into many polynomials. However, there is always a unique interpolation with degree less than $n$. When we say *the* interpolation of $\vec{v}$, by default we refer to this unique interpolation with lowest degree.

It's easy to see that polynomial interpolation is linear, i.e. if $v(X)$ and $w(X)$ are interpolations of $\vec{v}$ and $\vec{w}$ respectively, then $x\cdot v(X)+y\cdot w(X)$ is the interpolation of $x\cdot\vec{v}+y\cdot\vec{w}$.

![678d298fde0abcc46897d0d81fa0efc0.png](/assets/678d298fde0abcc46897d0d81fa0efc0.png)

Now we learned about the polynomial interpolation, we replace all the column vectors in R1CS matrices by the polynomial interpolations.

![Groth16-QAP.png](/assets/Groth16-QAP.png)

By the linearity of polynomial interpolation, we see that the interpolation of $A\vec{z}$ is exactly the combination of the interpolation of the column vectors by $\vec{z}$. So are the interpolations of $B\vec{z}$ and $C\vec{z}$. For simplicity, denote the interpolations of these three polynomials by $a(X),b(X)$ and $c(X)$.

In the R1CS equation, we have $A\vec{z}\circ B\vec{z}=C\vec{z}$, which is equivalent to $a(r_i)\cdot b(r_i)=c(r_i)$ for all $r_i\in\{r_1,\cdots,r_d\}$. Note that this does not imply that $a(X)\cdot b(X)=c(X)$, because $a(X)\cdot b(X)$ has higher degree than $c(X)$. This does imply that all of $r_1,\cdots,r_d$ are the roots of $a(X)\cdot b(X)-c(X)$. Equivalently, this means the polynomial $t(X)=(X-r_1)\cdots (X-r_d)$ divides $a(X)\cdot b(X)-c(X)$.

In summary, the QAP problem is stated as follows: given
1. three sets of polynomials $\vec{A}(X)=a_1(X),\cdots,a_m(X)$, $\vec{B}=b_1(X),\cdots,b_m(X)$, and $\vec{C}(X)=c_1(X),\cdots,c_m(X)$, each polynomial has degree less than $d$
2. a polynomial $t(X)$ of degree $d$
3. a prefix of size $n$ for $\vec{z}$
decide if there exists $\vec{z}$ that respects the given prefix and such that

$$
\left(\sum_{i=1}^m z_i a_i(X)\right)\cdot\left(\sum_{i=1}^m z_i b_i(X)\right)-\left(\sum_{i=1}^m z_i c_i(X)\right)
$$

​		is divided by $t(X)$.

The QAP problem is the final problem that we are going to construct the zkSNARK for. Now let's quickly recap what we have learned so far:

In the very beginning, the verifier obtains $x$ that encodes the outputs and a part of the inputs for a computation, represented by circuit $C$, and wants to know if $x$ is part of a valid computation.

Now we transform the circuit $C$ into an equivalent QAP problem $\vec{A}(X),\vec{B}(X),\vec{C}(X),t(X)$. By equivalence, we mean $x$ is a part of valid computation, if and only if $x$ is part of a vector $\vec{z}$ such that $t(X)$ divides $\left(\sum_{i=1}^m z_i a_i(X)\right)\cdot\left(\sum_{i=1}^m z_i b_i(X)\right)-\left(\sum_{i=1}^m z_i c_i(X)\right)$.

The verifier cannot verify the validity of $x$ without learning the secret inputs or reexecuting the entire circuit. After all these transformations, the verifier still, sadly, cannot verify $x$ alone. So why do we go through all these trouble, transforming the circuit problem into an equivalent QAP problem that looks more complex?

The answer is, in the form of QAP, it is more convenient for the prover to generate a short proof which allows the verifier to verify $x$. Why is it only possible in QAP? Because polynomials are very powerful tools. We will make this point more clear as we explain the details of Groth16 in the next section.

## zkSNARK for QAP

The Groth16 zkSNARK scheme uses elliptic curves to prove knowledge of linear combination. To appreciate the role that elliptic curves play in the proof, consider a pair of points $P$ and $Q$. We say $(P,Q)$ is an $\alpha$-pair of $Q=\alpha P$. By the Discrete Log assumption, it is hard to compute $\alpha$ given $P$ and $Q$.

Given an $\alpha$-pair $P$ and $Q$, without knowing alpha, how do you generate another $\alpha$-pair? One way to accomplish this is to multiply some $k$ to both $P$ and $Q$, because obviously $kP$ and $kQ$ is another $\alpha$-pair.

Intuitively, this is the only way to produce another $\alpha$-pair. To put it another way, if you produce another $\alpha$-pair $P'$ and $Q'$, you must "know" the multiplier $k$ such that $P'=kP$. This intuition, however, cannot be proved strictly from the Discrete Log assumption, so it remains an assumption.

Similarly, if you have a collection of $\alpha$-pairs $(P_1,Q_1)$, $(P_2,Q_2)$, $\cdots$, $(P_n,Q_n)$, the only way to produce another $\alpha$-pair is to compute a linear combination of these $\alpha$-pairs. That is to say, if you produce $P',Q'$ that is an $\alpha$-pair, you must "know" $k_1,\cdots,k_n$ such that $P=k_1P_1+\cdots+k_nP_n$ and $Q=k_1Q_1+\cdots+k_nQ_n$.

The above assumption is called the Knowledge-of-Exponent (KoE) assumption.

What does the KoE assumption have to do with the QAP problem? Because we are going to represent polynomials by elliptic curve points, and the KoE assumption forces the prover to compute polynomials by linearly combining existing ones.

Let $G$ be an elliptic curve point, and $x$ be a secret number. If you are given two sets of points, one set is $G,xG,x^2G,\cdots,x^dG$, and another set is $\alpha G,\alpha xG,\alpha x^2G,\cdots,\alpha x^dG$, then by KoE assumption, the only way to produce an $\alpha$-pair is to compute a linear combination of these given points. In another word, if you can output an $\alpha$-pair $P,\alpha P$, then you must know some polynomial $f(x)$ of degree at most $d$ such that $P=f(x)G$.

Similarly, if you are given two sets of points $a_1(x)G,\cdots,a_m(x)G$ and $\beta a_1(x)G,\cdots,\beta a_m(x)G$, the only way to produce a $\beta$-pair is to compute their linear combinations, and generate $f(x)P$ and $\beta f(x)P$ such that $f(x)$ is a linear combination of $a_1(x),\cdots,a_m(x)$.

However, we have neglegted two important problems:
- Since $\alpha$ is a secret and should not be learned by anyone, how do we generate the original $\alpha$-pairs in the first place?
- Furthermore, if some claims that $P$ and $Q$ is an $\alpha$-pair, how do we know if this claim is true?

The solution to the first problem, unfortunately, is to introduce a trusted setup, who will produce the original $\alpha$-pairs and then "forget" the secret $\alpha$.

The solution to the second problem is to use **bilinear pairings**. A bilinear pairing scheme involves three elliptic curve groups $\mathbb{G}_1$, $\mathbb{G}_2$ and $\mathbb{G}_T$ of the same size. Then there is an efficiently computable map $e:\mathbb{G}_1\times\mathbb{G}_2\to\mathbb{G}_T$, which is bilinear, i.e. for any $\alpha P\in\mathbb{G}_1$ and $\beta Q\in\mathbb{G}_2$, $e(\alpha P,\beta Q)=\alpha\beta \cdot e(P,Q)$.

If the claimed $\alpha$-pair $(P,Q)$ is in the first group $\mathbb{G}_1$, and the trusted setup also generated an $\alpha$-pair $H,\alpha H$ in the second group $\mathbb{G}_2$. Then we can easily check that $(P,Q)$ is indeed an $\alpha$-pair by comparing $e(P,\alpha H)$ and $e(Q,H)$.

Now our tools are ready: the KoE assumption and the bilinear pairing. We are ready to construct the zkSNARK for QAP problem.

### Setup

Recall that in the QAP problem, the polynomial sets $\vec{A}(X)$, $\vec{B}(X)$ and $\vec{C}(X)$ each contains $m$ polynomials of degree at most $d-1$, and $t(X)$ is of degree $d$. A solution $\vec{z}$ has $m$ elements.

The prover is given a prefix of length $n$ for $\vec{z}$, where $n<m$. The prover "knows" the rest $m-n$ elements of $\vec{z}$, such that the entire $\vec{z}$ satisfies the QAP problem.

In the setup phase, the trusted third party samples random numbers $\alpha,\beta,\gamma,\delta$ and $x$. Let $G$ and $H$ be a generator of $\mathbb{G}_1$ and $\mathbb{G}_2$ respectively. Then the third party outputs the following elliptic points:

1. The points in the group $\mathbb{G}_1$:

$$
\alpha G,\quad \beta G,\quad \gamma G
$$

$$
G,\quad xG,\quad x^2G,\quad \cdots\quad x^{d-1}G
$$

$$
\frac{1}{\delta} t(x)G,\quad \frac{1}{\delta} x t(x)G,\quad \frac{1}{\delta} x^2 t(x)G,\quad \cdots\quad \frac{1}{\delta} x^{d-2} t(x)G,
$$

$$
\frac{1}{\gamma}(\beta a_i(x)+\alpha b_i(x)+c_i(x))G\qquad \forall i\in[1:n]
$$

$$
\frac{1}{\delta}(\beta a_i(x)+\alpha b_i(x)+c_i(x))G\qquad\forall i\in [n+1:m]
$$

2. The points in the group $\mathbb{G}_2$

$$
\beta H,\quad\gamma H,\quad\delta H
$$

$$
H,\quad xH,\quad x^2H,\quad\cdots\quad x^{d-1}H
$$

### Prove

The prover samples two numbers $r$ and $s$, computes
$$
h(X)=\frac{\left(\sum_{i=1}^m z_i a_i(X)\right)\cdot\left(\sum_{i=1}^m z_i b_i(X)\right)-\left(\sum_{i=1}^m z_i c_i(X)\right)}{t(X)}
$$
and generates the following three elliptic curve points.

1. The point $A\in\mathbb{G}_1$

$$
\alpha G+\sum_{i=1}^m z_i a_i(x)G+r\delta G
$$

2. The point $B\in\mathbb{G}_2$

$$
\beta H+\sum_{i=1}^m z_i b_i(x)H+s\delta H
$$



3. The point $C\in\mathbb{G}_1$

$$
\frac{1}{\delta}\sum_{i=n+1}^m z_i (\beta a_i(x)+\alpha b_i(x)+c_i(x))G + \frac{1}{\delta}h(x)t(x)G + (As + Br - rs\delta)G
$$




Remark: in Groth16 the prover doesn't produce $\alpha$-pairs for the KoE assumption explicitly. The $\alpha$-pairs are somehow merged together. The KoE assumption is working under the hood.

Remark: The $r$ and $s$ are used for randomizing the proof. You can just remove the items involving $r$ and $s$, the proof still works but it is no longer zero-knowledge.

### Verify

The verifier first computes a point $D\in\mathbb{G}_1$, which depends only on the public inputs

$$
D=\frac{1}{\gamma}\sum_{i=1}^n z_i (\beta a_i(x)+\alpha b_i(x)+c_i(x))G
$$


Given a proof $(A,B,C)$, the verifier checks that

$$
e(A,B)=e(\alpha G,\beta H)\cdot e(D,\gamma H)\cdot e(C,\delta H)
$$


Here we briefly explain the intuition behind the verification. The key is to verify that the prover "knows" a polynomial $h(X)$ such that
$$
\left(\sum_{i=1}^m z_i a_i(X)\right)\cdot\left(\sum_{i=1}^m z_i b_i(X)\right)=t(X)h(X)+\left(\sum_{i=1}^m z_i c_i(X)\right)
$$
which is equivalent to say $t(X)$ divides $\left(\sum_{i=1}^m z_i a_i(X)\right)\cdot\left(\sum_{i=1}^m z_i b_i(X)\right)-\left(\sum_{i=1}^m z_i c_i(X)\right)$.

Note that in the proof point $A$, there is one item $\sum_{i=1}^m z_ia_i(X)G$, and in $B$, there is one item $\sum_{i=1}^m z_ib_i(X)H$. On the left hand side, the verifier evaluates $e(A,B)$, these two items are multiplied together.

Note also that $C$ contains is an item $\frac{1}{\delta}\sum_{i=n+1}^m z_i c_i(X)G$ and an item $\frac{1}{\delta}h(x)t(x)G$, so $e(C,\delta H)$ contains an item $\sum_{i=n+1}^m z_i c_i(X)e(G,H)$ and an item $h(x)t(x)e(G,H)$. This sum of $z_i c_i(X)$ misses the part for $i$ from $1$ to $n$. This part is provided by $e(D,\gamma H)$.

Therefore, if the prover knows the correct $h(X)$, then the $\left(\sum_{i=1}^m z_ia_i(X)\right)\left(\sum_{i=1}^m z_ib_i(X)\right) e(G,H)$ in the left hand side cancels out with the $\left(\sum_{i=1}^m z_i c_i(X)+h(x)t(x)\right) e(G,H)$ in the right hand side.

The other items also cancel out by design. We will not discuss the details.

## Conclusion

In this article, we explained the Groth16 zkSNARK scheme. We introduced the arithmetic circuit, R1CS problem and QAP problem. Then we explained how does Groth16 zkSNARK work for proving knowledge of a solution to a QAP problem. The Groth16 scheme makes use of KoE assumption and bilinear pairings. It requires a trusted setup for each separate circuit. In return, the Groth16 scheme enjoys the most efficient verifier and the shortest proof size.