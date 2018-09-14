---
title: Number Theoretic Transform
layout: math
---

# Number Theoretic Transform

NTT就是把快速傅里叶变换(FFT)应用到有限域$\mathbb{F}=\mathbb{Z}\_q$上。这里$q$为素数。 

和FFT不同，FFT的结果是有一定“意义”的，即把函数从空域映射到它的频域上。但NTT的结果并没有什么明显的意义，它的主要作用是高效计算多项式环$\mathbb{Z}\_q[X]/(X^n+1)$上的乘法。

直观上，多项式的乘法相当于卷积运算，卷积运算经过NTT变换后得到的空间中，被映射成了对应元素相乘。这一点和傅里叶变换的思想是相同的。同样，求多项式的逆也对应成了NTT空间里对数组的每个元素分别求逆。

## NTT在BLISS算法的作用

BLISS算法的具体实现中使用的环是NTRU密码系统中的环，即$\mathbb{Z}\_q[X]/(X^n+1)$，采取的矩阵由该环上的元素组成。

BLISS算法的效率，便取决于计算这个环上乘法的效率，即多项式模$X^n+1$的效率。NTT便是为此而设计。

## NTT的定义

设$w$为$\mathbb{Z}\_q$上的$n$次单位根，且$n\mid q-1$。

设$(a\_0,\cdots,a\_{n-1})\in\mathbb{Z}\_q^n$，则

$$NTT(a\_0,\cdots,a\_{n-1})=(\tilde{a}\_0,\cdots,\tilde{a}\_{n-1})$$

其中

$$\tilde{a}\_i = \sum\_{j=0}^{n-1} a\_j w^{ij} = f(w^i)$$

NTT是可逆的，它的逆运算INTT为

$$INTT(\tilde{a}\_0,\cdots,\tilde{a}\_{n-1}) = (a\_0',\cdots,a\_{n-1}')$$

其中

$$a\_i' = n^{-1} \sum\_{j=0}^{n-1} \tilde{a}\_j w^{-ij} = n^{-1}\tilde{f}(w^{-i})$$

注意到，其实INTT和NTT是很像的，实际上可以用一份代码来实现，只是前期和后期的处理不同。

## 利用NTT计算多项式乘法

设$\psi^2=w$，于是$\psi$为$2n$次单位根。

利用NTT计算$\mathbb{Z}\_q[X]/(X^n+1)$上的乘法，可分为以下三步进行：

1. **预计算。**令$a\_i'=a\_i\psi^i$，$b\_i'=b\_i\psi^i$
2. **计算NTT。**
    1. 令$(\tilde{a}\_0',\cdots,\tilde{a}\_{n-1}')=NTT(a\_0',\cdots,a\_{n-1}')$，$(\tilde{b}\_0',\cdots,\tilde{b}\_{n-1}')=NTT(b\_0',\cdots,b\_{n-1}')$；
    2. 计算$\tilde{c}\_i'=\tilde{a}\_i'\tilde{b}\_i'$；
    3. 计算$(c\_0',\cdots,c\_{n-1}')=INTT(\tilde{c}\_0',\cdots,\tilde{c}\_{n-1}')$。
3. **尾计算。**令$c\_i=c\_i'\psi^{-i}$

## NTT的计算

NTT的本质就是FFT。所以一切算法和FFT基本相同。FFT本质上是将一个矩阵乘到原来的数组上，这个矩阵类似下面这样：

$$\begin{bmatrix}1 & 1 & 1 & 1\\ 1 & w & w^2 & w^3\\ 1 & w^2 & w^4 & w^6\\ 1 & w^3 & w^6 & w^9\end{bmatrix}$$

矩阵的大小$n$一般取为2的幂，实际运算时，利用这个矩阵的特殊性质，将其分解开来（分解为$\log n$个矩阵乘积），分解出的每个矩阵都可以高效（线性时间$n$）作用在数组上，因此总复杂度为$n\log n$。

NTT的计算没什么不同，只不过FFT的$w$是复数域上的$n$次单位根，而NTT的$w$是$\mathbb{Z}\_q$上的，仅此而已。
