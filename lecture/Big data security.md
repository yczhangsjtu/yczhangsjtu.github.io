# Big Data Security

## How to define subset in complete subtree and subset difference?

* Subset $S_i$ in **complete subtree framework** is defined by the **complete subtrees** rooted at $v_i$ in the binary tree .
* Subset $S_{i,j}$ in **subset difference framekwork** is defined by difference between two **complete subtrees** rooted at $v_i$ and $v_j$.

## How many subsets does a user belong to?

* In the **complete subtree framework** the number is equal to the height of a tree.
* In the **subset difference framework** the number is equal to $\sum_{i=1}^{log_2 N+1} 2^i-i$.

## Given the set R of users that should be revoked, how to find disjoint subsets to cover N/R.

* In the **complete subtree framework**, remove all the paths from $R$ to the root, the subtrees left are exactly the answer.
* In the **subset difference framework**: Given the set $R$ find Steiner's tree $T$.

## How to assign keys in the two frameworks?

* In the **complete subtree framework**, assign each subset a different key. Users need to store $log_2N$ keys corresponding to the subsets.
* In the **subset difference framework**, the number of keys for each user is approximately $O(N)$, if each subset is issued one different key. So we have to take a different approach. Select a pseudorandom function $f()$ from $n$-bits to $3n$-bits. Assign each node a random label of $n$-bits. For node $i$, compute $f(label_i)=x\|y\|z$, put $x$ at the left child, $z$ at the right child $j$. Compute  $f(x)=x'\|y'\|z'$, then $y'$ is the key of subset $S_{i,j}$.

## What is attribute based encryption?

Encrypt $M$ with two labels "SJTU" and "master course". Only people with these two attributes can decrypt it.

## How is attribute baesd encryption?

* Compared with IO-based encryption
	* extra error-tolerance
* Threshold cryptography, random polynomials

## How to construct attribute based encryption?

* Underlying primitives
	* Two groups with prime order $q$, $G_1,G_2$, $g$ denotes a generator of $G_1$
	* Bilinear mapping $\hat{e}:G_1\times G_1\to G_2$
	* The set of all attribuets $U$

* Setup by trusted center
	* public system parameter $pk$
	* secret master key $mk$

	1. assign an integer $i\in\mathbb{Z}_q^*$ to each attribute $U\in\{1,2,3,...,|U|\}$.
	2. select $|U|$ random integer $t_i\in\mathbb{Z}_q^*$, compute $T_1=g^{t_1}, \cdots, T_{|U|}=g^{t_{|U|}}$.
	3. select a random integer $y\in\mathbb{Z}_q^*$
	4. compute $Y=e(g,g)^y$
	4. $pk=\{G_1,G_2,\hat{e},T_1,\cdots,T_{|U|},g,Y\}$, $mk=\{t_1,t_2,\cdots,t_{|U|},y\}$

* Encryption
	* input $M$, identity $w=\{i_1,i_2,\cdots\}$
	* output $$C=(w,E=M\cdot Y^s, \{E_i=T_i^s\}_{i\in w})$$ where $s$ is randomly selected in $\mathbb{Z}_q$
* Key derivation
	* input identity $w$
	* output decryption key which is able to decrypt $Enc_w(M)$ with $w'\cap w\geq d$
	* random polynomials $f(x)$
		1. given $w$, generate random polynomial $f(x)$ with degree $d-1$ and $f(0)=y$
		2. for each $i\in w$ $$D_i = g^{\frac{f(i)}{t_i}}$$
		3. decryption key $\{D_i|i\in w\}$
* Decryption
	* encryption identity $w$
	* decryption identity $w'$
	* $S\subseteq w\cap w'$: $|S|=d$
	* decryption $$E/\prod_{i\in S} \left(\hat{e}(D_i,E_i)\right)^{\Delta_{i,s(0)}}=M$$ $$\Delta_{i,s}(x)=\prod_{j\in S,j\neq i}\frac{x-i}{j-i}$$
	* $E=M\cdot Y^s\Longleftrightarrow$ $$\hat{e}(g,g)^{y\cdot S}=Y^s=\prod_{i\in S}\left(\hat{e}(D_i,T_i)\right)^{\Delta_{i,s}(0)}$$