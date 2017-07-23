## 偏差的计算

 PPT Chap03 分组密码与DES 8.2 线性密码分析 page 44

 S盒的输入输出为一张表。如果输入为4比特，输出4比特，则表有16项，8列。

| X1   | X2   | X3   | X4   | Y1   | Y2   | Y3   | Y4   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 1    | 1    | 1    | 0    |

 每列取或者不取，可以有约256种取法，比如 $X1\oplus X2 \oplus Y4$。
 对每一项，这个式子可能等于0或者1。
 在这16项中随机取（相当于X1,X2,X3,X4）均匀分布，该式子为1的概率和1/2的差即为偏差。
 如果恰好有8项为1，8项为0，则没有偏差。

 利用堆积引理，得到最后一轮前的偏差。
 穷举最后一轮秘钥，对每个秘钥解密t个明密文对中的密文，若为正确的秘钥则试验中的偏差应该等于理论计算出的偏差。

## 安全的认证加密:定义与构造

 Chap06 消息认证码 认证加密方案 page 34

 CCA2安全性：Adaptive Chosen-Ciphertext Security，即攻击者可以多次查询密文的解密，并根据解密结果选择下次的密文。
 不可伪造性，即攻击者无法生成合法的Mac。

 构造：CPA 安全的分组加密ENK + Adaptive CMA 下存在性不可伪造的 MAC
- 只有先加密再认证为安全的组合方式
- 必须使用不同的秘钥

## CBC模式加密/认证的要点 (特点?原因?攻击?)

-  Chap04 高级加密标准与分组加密模式 page 31
  - 每个密文分组均依据于之前的全部分组
  - 对任意一个分组的改动都会影响后继的所有密文分组
  - 需要初始向量，初始向量必须随机
  - 若CBC中分组加密为伪随机置换，则CBC满足CPA安全性
  - 若攻击者获得了IV，可以通过预先改变IV中的某些位，使得接收者的明文中部分位被取反
  - 若IV不随机，攻击者可以预先获知IV，构成CPA攻击：
   1. 攻击者查询m，IV为IV1，得到密文C
   2. 攻击者预先获知IV2，便可以生成(m+IV1+IV2)||(m+C+IV1)的密文C||C
  - Chap06 消息认证码 CBC-MAC page 12
  - 所使用的加密算法安全，则所构造的MAC在自适应选择消息攻击下存在性不可伪造
  - 基本算法只在固定输入长度时安全
  - 对固定长度为2的CBC-MAC的生日攻击（获得任意(m1,m2)的MAC）：
   1. 变化X和Y，用生日攻击找到一对碰撞(m1,Y)和(X,m2)
   2. 该碰撞等价于Enc(m1)+Y=Enc(X)+m2，即Enc(m1)+m2=Enc(X)+Y
   3. 查询(X,Y)的Mac，即可获得（m1,m2)的Mac
  - 不固定长度时，直接查询m1和m2的Mac得到T1和T2，即可得到(m1||m2+T1)的Mac为T2
  - IV必须固定，否则攻击者通过变化IV可以得到任意消息的Mac
  - 安全的CBC-MAC：将长度作为第一个块输入；使用两个秘钥（或由原始秘钥生成两个秘钥），用第一个秘钥认证消息，再用第二个秘钥认证第一个认证

## 同态加密的定义和特点,实例

 <http://www.boazbarak.org/cs127/chap15_FHE.html>

## RSA的比特安全性

 Chap08 RSA密码体制安全分析 page 31 RSA 的比特安全性

 对一个均匀随机的明文加密，若RSA问题困难，则不存在有效算法可以从RSA加密的密文中恢复出其中一个比特(如LSB)。如果存在这么一个算法，就可以调用它恢复出整个明文。

 设PO可以从密文中恢复出明文最低位的比特。利用二分查找得到明文：
  1. 从i=1开始，第i步查询PO(2^(ie) C)，即明文乘以2^i之后的最低位比特
  2. 每次查询可以将明文所在的范围缩为一半

  3. ElGamal算法: 语义安全性? All or Nothing安全性?

 Chap09 基于离散对数问题的公钥密码体制 page 40 语义安全性 page46

- 通过二次剩余，教科书版本的ElGamal算法不具有语义安全性
- 安全的ElGamal选择p=2q+1，p和q都为素数，在Zp的q阶子群上执行ElGamal加密
- 安全的ElGamal的语义安全性等价于DDH问题

 ElGamal体制的解密：等价于CDH问题 (All-or-Nothing安全性)

## 一次签名为什么只能签一次?

 Chap10 签名方案 page 27 一次签名安全模型

 若攻击者获得多个消息 m1,m2 的签名 s1,s2，有可能用 m1,m2 和 s1,s2 分别组合出新的 m' 和 s' 为合法消息签名对。

 如 Lamport 签名，如果 m1 与 m2 有两个比特 i 和 j 不同，则攻击者获知 yi,1 和 yi,2 ，攻击者翻转 m1 的第 i 比特得到 m'，并将对应的签名中的 yi,b 换为 yi,1-b 就得到了一对合法消息签名。

## 签名体制中不使用Hash函数所带来的攻击

 Chap10 签名方案 page 6

 如果不用Hash函数
- 若签名方案的验证算法是从签名 s 计算出 m，则有存在性伪造攻击。
- 也有可能从已知消息签名对伪造任意消息的签名，如Introduction to Modern Cryptography 12.3.1节（中文版281页，英文版 page 404）对教科书版本RSA签名的攻击。