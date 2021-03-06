---
title: NTRU加密系统
layout: math
---

# NTRU加密系统

NTRU加密是基于格上困难问题的一套加密算法。它本来有可能和RSA齐名的，但可能因为被申请了专利，使用者不多，知名度很低。

不清楚NTRU系统能不能用来签名，但它主要是用来加密的。

一个NTRU加密系统有三个参数，$(N,p,q)$，其中$q$远大于$p$。$p$和$q$不一定是素数，但要互素。

NTRU系统处理的对象是环$\mathcal{R}=\mathbb{Z}[X]/(X^N-1)$中的多项式。令$\mathcal{R}_q=\mathbb{Z}_q[X]/(X^N-1)$，令$\mathcal{R}_p=\mathbb{Z}_p[X]/(X^N-1)$

## 密钥生成

随机产生一对多项式$f,g\in\mathcal{R}$，其中$f$在$\mathcal{R}_p$和$\mathcal{R}_q$上都可逆，设它在这两个环上的逆分别为$F_p$和$F_q$。令$h=F_q\cdot g \bmod q$。则$h$为公钥，$f$为私钥。另外，考虑到效率，$F_p$一般和私钥一起存储，而不必每次加密时都计算一次。

注：

* $f$从满足以下条件的多项式集合中均匀随机选择：所有系数中包含$d_f$个1和$d_f-1$个$-1$，其他系数为0
* $g$从满足以下条件的多项式集合中均匀随机选择：所有系数中包含$d_g$个1和$d_g$个$-1$，其他系数为0

其中$d_f$和$d_g$为系统参数。

## 加密

设消息为多项式$m$，随机产生多项式$\phi$，计算密文$e=p\phi\cdot h+m\bmod q$。

加密相当于在明文$m$上增加一个$p$的倍数的混淆项。这个$p$在解密时会通过模$p$消掉。

## 解密

首先计算$a=f\cdot e\bmod q$，将$a$限定在$-q/2$到$q/2$之间，则$m=F_p\cdot a\bmod p$。

乘上$f$之后，混淆项变为$p\phi\cdot g\bmod q$。选定参数，使得$p\phi\cdot g+f\cdot m\bmod q$落在$-q/2$和$q/2$之间，才能正确解密。
