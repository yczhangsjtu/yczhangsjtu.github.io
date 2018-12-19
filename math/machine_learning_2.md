---
title: 机器学习笔记（二）
layout: math
---

# 机器学习笔记（二）

## 牛顿法

牛顿法用来求函数的零点。用牛顿法求解导数的零点，可以求出函数的极值。一般来说，牛顿法收敛得比梯度下降更快。

以一维函数为例，直观地描述牛顿法的计算过程。首先在$x$轴上选取一个起始点，计算函数图像在这点的切线，求出该切线和$x$轴的交点，以这个交点作为新的起点。重复这一步骤，直到收敛。每一步的迭代公式为：

$$
\theta^{(t+1)}=\theta^{(t)}-\frac{f(\theta^{(t)})}{f'(\theta^{(t)})}
$$

如果目标是求出函数$\ell(\theta)$的最大（小）值，则用牛顿法求$\ell'(\theta)$的零点，迭代公式为：

$$
\theta^{(t+1)}=\theta^{(t)}-\frac{\ell'(\theta^{(t)})}{\ell''(\theta^{(t)})}
$$

拓展到高维函数的情况，则迭代公式为：

$$
\theta^{(t+1)}=\theta^{(t)}-H^{-1}\nabla_{\theta}\ell(\theta)
$$

其中$H$为Hassian矩阵，计算公式为：

$$
H_{ij}=\frac{\partial^2\ell}{\partial\theta_i\partial\theta_j}
$$

牛顿法比梯度下降更慢，因为对Hassian矩阵的求逆的计算代价更大。

## 指数分布族

高斯分布和伯努利分布都可以看做**指数分布族**中的两类特殊分布。指数分布族的分布函数的一般形式为：

$$
p(y;\eta)=b(y)\exp\left(\eta^T T(y)-a(\eta)\right)
$$

其中，$\eta$是参数，可能为标量或者向量。$T(y)$是一个**充分统计量**，一般取常函数即$T(y)=y$。$a,b,T$是三个函数，这三个函数的选取决定了得到的是哪一类分布。

从下面的推导可以看出，伯努利分布属于指数分布族：

$$
\begin{eqnarray*}
p(y;\phi) &=& \phi^y(1-\phi)^{1-y} \\
&=& \exp\left(\log\phi^y(1-\phi)^{1-y}\right) \\
&=& \exp\left(y\log\phi+(1-y)\log(1-\phi)\right) \\
&=& \exp\left(\log\frac{\phi}{1-\phi}\cdot y+\log(1-\phi)\right)
\end{eqnarray*}
$$

上式中第一步来自伯努利分布的定义，最后一步是将$y$的系数合并得到的。

令$\eta=\log\frac{\phi}{1-\phi}$，得到$\phi=\frac{1}{1+e^{-\eta}}$，令$a(\eta)=-\log(1-\phi)=\log(1+e^{\eta})$，$b(y)=1$，$T(y)=y$，就得到了伯努利分布的函数。

类似的推导也可以得到高斯分布对应的函数和参数选择。只考虑标准差为1的高斯分布，即$\sigma=1$。于是

$$
\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}(y-\mu)^2\right)=\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}y^2\right)\exp\left(\mu y+\frac{1}{2}\mu^2\right)
$$

因此，高斯分布对应的参数为：$\mu=\eta$，$T(y)=y$，$a(\eta)=-\frac{1}{2}\eta^2$，$b(y)=\frac{1}{\sqrt{2\pi}}\exp\left(-\frac{1}{2}y^2\right)$。

## 一般线性模型

之前，我们分别用高斯分布和伯努利分布，推导出优化目标概率函数$\ell(\theta)$，分别得到了线性回归模型和用于分类问题的对数回归模型。现在，使用指数分布族推导$\ell(\theta)$，推导出一个更一般的回归模型，即一般线性模型。

一般线性模型有以下假设：

1. 给定参数$\theta$，$y$关于$x$的条件分布属于指数分布族，即$(y\mid x;\theta)$为指数分布族；
2. 目标是得到预测函数$h\_{\theta}(x)$，使得$h\_{\theta}(x)=E[T(y)\mid x]$，通常$T(y)=y$；
3. 线性：$\eta=\theta^T x$，若$\eta$是向量，则对所有的$i$，$\eta\_i=\theta\_i^T x$。

以伯努利分布为例，预测函数为

$$
h_{\theta}(x)=E[y\mid x;\theta]=P(y=1\mid x;\theta)=\phi=\frac{1}{1+e^{-\eta}}=-\frac{1}{1+e^{-\theta^T x}}
$$

这就得到了对数回归模型。

对任何学习问题，如果用一般线性模型，唯一需要确定的就只有概率分布的选择。

### 用一般线性模型推导多元选择问题的学习模型

对数回归模型只能解决双选项的分类问题。更一般的多元选择问题中，$y$的取值范围为$\{1,2,3,\cdots,k\}$。用$\mathcal{I}\\{\cdot\\}$表示一个事件的指示函数（即这个事件发生则函数值为1，否则为0），则可以将$y$的分布函数表示为

$$
P(y)=\phi_1^{\mathcal{I}\{y=1\}}\phi_2^{\mathcal{I}\{y=2\}}\cdots\phi_k^{\mathcal{I}\{y=k\}}
$$

其中$\phi\_1+\phi\_2+\cdots+\phi\_k=1$。因此，实际上的参数只有$\phi\_1,\cdots,\phi\_{k-1}$这$k-1$个，$\phi\_k$是$1-\phi\_1-\cdots-\phi\_{k-1}$的简写。

定义函数$T(y)$为如下（$k-1$维）向量值函数：

$$
\begin{eqnarray*}
T(1)&=&(1,0,\cdots,0)\\
T(2)&=&(0,1,\cdots,0)\\
&\vdots&\\
T(k-1)&=&(0,0,\cdots,1)\\
T(k)&=&(0,0,\cdots,0)
\end{eqnarray*}
$$

于是对$i=1,\cdots,k-1$，$\mathcal{I}\\{y=i\\}=T(y)\_i$。另外，$\mathcal{I}\\{y=k\\}=1-T(y)\_1-\cdots-T(y)\_{k-1}$。

因此

$$
P(y)=\phi_1^{T(y)_1}\phi_2^{T(y)_2}\cdots\phi_{k-1}^{T(y)_{k-1}}\phi_k^{1-\sum_{j=1}^{k-1}T(y)_j}=\exp\left(\eta^T\cdot T(y)+\log\phi_k\right)
$$

其中

$$
\eta=\begin{bmatrix}
\log(\phi_1/\phi_k)\\
\vdots\\
\log(\phi_{k-1}/\phi_k)
\end{bmatrix}\in\mathbb{R}^{k-1}
$$

求解出

$$
\phi_i=\frac{e^{\eta_i}}{1+\sum_{j=1}^{k-1}e^{\eta_j}}=\frac{e^{\theta_i^T x}}{1+\sum_{j=1}^{k-1}e^{\theta_j^T x}}\qquad 1\leq i\leq k
$$

于是，预测函数为

$$
\begin{eqnarray*}
h_{\theta}(x) &=& E[T(y)\mid x;\theta] \\
&=& E\left[\left.\begin{matrix}\mathcal{I}\{y=1\}\\\vdots\\\mathcal{I}\{y=k-1\}\end{matrix}\right| x;\theta\right] \\
&=& \begin{bmatrix}
\phi_1\\
\vdots\\
\phi_{k-1}
\end{bmatrix} = \begin{bmatrix}
\frac{e^{\theta_1^T x}}{1+\sum_{j=1}^{k-1}e^{\theta_j^T x}}\\
\vdots\\
\frac{e^{\theta_{k-1}^T x}}{1+\sum_{j=1}^{k-1}e^{\theta_j^T x}}
\end{bmatrix}
\end{eqnarray*}
$$

最大化的目标函数则是

$$
\begin{eqnarray*}
L(\theta)&=&\prod_{i=1}^m P(y^{(i)}\mid x^{(i)};\theta) \\
&=& \prod_{i=1}^m \phi_1^{\mathcal{I}\{y^{(i)}=1\}}\cdots\phi_k^{\mathcal{I}\{y^{(i)}=k\}} \\
&=& \prod_{i=1}^m \frac{e^{\theta_{y^{(i)}}^T x}}{1+\sum_{j=1}^{k-1}e^{\theta_j^Tx}}
\end{eqnarray*}
$$

这就得到了Softmax回归模型。

