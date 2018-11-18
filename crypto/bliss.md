---
title: BLISS算法
layout: math
---

# BLISS算法

## Rejection Sampling方法

问题：手里有满足分布$g$的输入，怎样对其进行取样，使得样本满足分布$f$？

以分布$g$进行取样，对每一个样本，以概率$f(x)/(M\cdot g(x))$接受。其中$M$为一个正实数。

简单理解一下这个算法：感觉就是强行把每个x出现的概率乘了一个 $f(x)/g(x)$，也就是强行把概率分布函数从 $g(x)$ 拧成了 $f(x)$。前提是$f(x)/(M\cdot g(x))$一定要一直不大于1。所以$M$要足够大。当然，$M$越大拒绝率越高，为了让输出的数量和原来差不多，这一过程要重复$M$次，所以为效率考虑，$M$越小越好。

## 论文Lattice signatures without trapdoors中的签名方法

私钥是一个$m\times n$矩阵$S$，公钥是一个$n\times m$矩阵$A$和矩阵$T=AS\bmod{q}$。$S$中的元素为小整数，$A$中的元素为$\mathbb{Z}\_q$中均匀分布的元素。

签名消息$m$的时候，首先选取向量$y$，满足$\mathbb{Z}^m$上的离散高斯分布，方差为$\sigma$。
计算$c=H(Ay\bmod{q},m)$，这里$H$为哈希函数，输出为$\mathbb{Z}^n$中的向量。
计算$z=Sc+y$。签名为$(z,c)$。

验签消息$m$和签名$(z,c)$的时候，检查$\|z\|$足够小，并且$c=H(Az-Tc\bmod{q},m)$。

很容易验证算法的正确性，但安全性还不够。因为同一个私钥大量的签名会泄露$Sc$的分布，从而泄露$S$的分布。

为了隐藏$S$的信息，就要用到刚刚提到的Rejection Sampling方法了。我们对$y$的选择过程用这个方法，每选择一个$y$，就算出一个$z$。我们的目标是让$z$的分布为方差为$\sigma$的离散高斯分布。（原分布也是这样的离散高斯分布，只不过偏移了$Sc$。）

为了确定$M$，经过一番可以忽略的复杂计算，$M$大约选为$e$。此时需要选择一个$\sigma=\tau\|Sc\|$使得$f(x)>M\cdot g(x)$的概率足够小（但无论$\tau$取多大，这个概率都不会等于1，所以$\tau$在这里起到了一个安全参数的作用）。想想看，两个高斯函数方差相同，中心差了几步，把其中一个无论怎样压扁，也不能让它在无穷远处比另一个小。

## 这篇文章做了什么？

首先就是要摆脱$\tau$的限制。方法是对原来的算法做一点小改动。选取了$y$之后，再随机选一个比特$b\in\\{1,-1\\}$，让$z=bSc+y$。这时$z$的分布就变成了两个中心对称的高斯分布的平均（这就是论文题目的来源，bimodal）。这个分布就比原来好多了，可以保证$M=e$而$\sigma=\|Sc\|/\sqrt{2}$的时候$f(x)\leq M\cdot g(x)$永远成立。

但这个方法用上后，原来的签名算法就不对了：只有当$Az-Tc\bmod q =Ay\bmod q $，也就是$bTc=Tc$时，验证才能通过。为了让算法正确，把模换成$2q$，让$T$永远等于$qI$，这样$T$就等于$-T$了。

好，现在问题就是，把$T$固定了，公私钥对该怎么生成？换句话说，怎么得到一对矩阵$A$和$S$使得$AS=qI$？论文给出的方法是只随机选取$A$和$S$的左边部分和上边部分，各留出$n\times n$的矩阵稍后确定。$A$的左边$m-n$列随机在$\mathbb{Z}\_q$中选取，记作$A'$；$S$的上边$m-n$行随机选一些小的整数。接下来，把$S$的下边$n$行填成$-I$，而$A$的右边$n$列设为$2A'S'\bmod q+qI$，左边$m-n$列改为$2A'$。很容易验证，这样得到的$A$和$S$乘积确实是$qI$。

安全性证明指出，如果有算法能在只知道$A$的情况下构造出签名，这个算法可以用来破解最短整数解问题。

具体实现方面，文章设计了新的取样离散高斯分布的方法。用Rejection Sampling需要十倍于平均分布的取样工作量。本文提出的方法如下取样以概率exp(-x/f)为1的01分布（而不用计算exp）：将整数x展开为二进制，即表示为若干2^i的和，对每个加项单独取样概率exp(-2^i/f)的01分布，只要有一个为0取样结果就是0。这样就只需要预计算好log x个exp(-2^i/f)即可。利用exp(-x/f)的01分布，可以取样出离散高斯分布。

## BLISS的具体实现

BLISS算法的具体实现使用的不是$\mathbb{Z}$，而是NTRU加密系统使用的环，即多项式环$\mathcal{R}\_{2q}=\mathbb{Z}\_{2q}[X]/(X^n+1)$，而矩阵则被缩得很小，公钥和私钥矩阵分别为$1\times2$和$2\times1$。在计算机中，多项式由大小为$n$的整数数组来存储。

### 生成公私钥对

BLISS算法参考了NTRU加密系统的算法。生成公私钥对时，采样一对随机多项式$f$和$g$，其中$f$为在$\mathcal{R}\_q=\mathbb{Z}\_q[X]/(X^n+1)$可逆多项式。这两个多项式都满足：所有系数中有$d\_1$个$\pm1$，有$d\_2$个$\pm2$，其余全部是$0$。私钥$S$为$2\times1$的矩阵$(s\_1,s\_2)^T$，其中$s\_1=f$，$s\_2=2g+1$。公钥为$1\times2$的矩阵$(2a\_q,q-2)$，其中$a\_q=(2g+1)/f\bmod{q}$。在实际使用时，只需要存储$a\_q$为公钥即可。

### 签名

签名时，设消息为$m$，首先用离散高斯分布采样出一对多项式$y=(y\_1,y\_2)$，然后计算$u=\zeta Ay\bmod 2q$，这里$\zeta$是$q-2$在模$2q$下的逆。于是$u=2\zeta a\_q y\_1 + y\_2\bmod 2q$。

接下来计算$c$。这时不再直接用$u$和消息$m$一起做Hash，而是将$u$的每个系数去掉低位$d$个比特，然后对$p$取模。这里$p$是一个比$q$小得多的整数，由$q$去掉低位$d$个比特得到。将去掉低位$d$个比特的$u$记为$[u]$。将$[u]\bmod p$和$m$一起Hash，得到$c$。这里$c$所取的空间为所有包含$\kappa$个1的01数组。

接下来计算$z=y\pm v$。这里$v=Sc$不再直接由$S$乘以$c$计算，而是调用一个算法`GreedySC(s,c)`计算，得出的结果相当于计算$Sc'$，其中$c'$和$c$相比将某些位置的符号翻转，使得最终的$Sc'$的L2模尽量小。$\|Sc\|$的减小直接降低签名算法重复次数的期望，直接提高算法效率。这是Accelerated BLISS论文的主要贡献。

接下来进行Resampling，即以一定概率重新启动该算法。

最后，对$z$进行压缩。其实$[u]\bmod p$代替$u$就是为压缩签名做的准备。注意到$u=\zeta Ay=\zeta A(z\pm Sc')=\zeta Az\pm\zeta ASc'=\zeta Az+\zeta qc'=\zeta a\_q z\_1+\zeta qc'+z\_2\bmod 2q$。验证签名时，验证者可以通过公钥从$z\_1$和$z\_2$中计算出$u$来，进而计算$[u]\bmod p$。但$z\_1$和$z\_2$的信息其实是冗余的，令$z\_2'=([u]-[u-z\_2])\bmod p$，将其代替签名中的$z\_2$。注意到$u-z\_2=\zeta a\_q z\_1+\zeta qc'\bmod q$，可以只从$z\_1$中计算出来，因此从$z\_1$和$z\_2'$可以计算出$[u]\bmod p$。

最终，签名为$(z\_1,z\_2',c)$。