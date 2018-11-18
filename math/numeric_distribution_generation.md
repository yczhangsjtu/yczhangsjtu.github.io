---
title: Numeric Distribution Generation
layout: math
---

# Numeric Distribution Generation

We introduce sampling methods for several numerical distributions.

**Sample discrete distribution**. To sample $X$ such that $X=x_i$ with probability $p_i$ for $i=1,…,k$. All we have is a uniform random geneartor $U$. Consider $K=floor(kU)$ and $V=(kU)\bmod 1$, i.e. the integral and fractional part of $kU$ respectively. Then we compute $X$ as follows: if $V<P_K$, then set $X= x\_{K+1}$, otherwise $X=Y\_K$. For appropriate tables $(P\_0,…,P\_{k-1})$ and $(Y\_0,…,Y\_{k-1})$, $X$ sampled in this way follows the specific distribution.

**Sample continuous distribution**. To sample a continuous distribution with cdf $F(x)$, consider the inverse function $F^{-1}(x)$. Let $X=F^{-1}(U)$ and $X$ follows the desired distribution.

**Distribution of max and min**. If $X\_1$ and $X\_2$ are independent with distributions $F\_1(x)$ and $F\_2(x)$ respectively,

* $\max(X\_1,X\_2)$ has distribution $F\_1(x)F\_2(x)$.
* $\min(X\_1,X\_2)$ has distribution $F\_1(x)+F\_2(x)-F\_1(x)F\_2(x)$.

**Sample exponential distribution with mean $\mu$**. $X=-\mu\ln U$.

**Sample point on $n$-dimensional sphere of radius one.** Let $X\_1, X\_2, …, X\_n$ be independent normal deviates, then the desired point is $(X\_1/r, X\_2/r, …, X\_n/r)$.

**Sample geometric distribution with probability $p$.** $N=ceil(\ln U/\ln(1-p))$.

For more such algorithms, refer to Non-Uniform Random Numbers by J.H. Ahrens and U. Dieter.