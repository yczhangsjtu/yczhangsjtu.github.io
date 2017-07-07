# Pollard rho 算法实现

## Pollard rho 算法

计算椭圆曲线群 $\langle P\rangle$ 中的点 $Q$ 的离散对数，即寻找整数 $k$ 使得 $kP = Q$。

1. 选取迭代函数 $f$；
2. 随机选择初始点 $X = cP + dQ$，令 $X' = X, c' = c, d' = d$；
3. 每次迭代，令 $X = f(X)$，同时得到对应的 $c$ 和 $d$；令 $X' = f(f(X'))$，同时得到对应的 $c'$ 和 $d'$；
4. 若发现 $X = X'$，退出循环；
5. 若此时 $d = d'$，算法失败；
6. 若此时 $d != d'$，则 $cP + dQ = c'P + d'Q，Q = (c'-c)/(d-d') P$，即 $k = (c'-c)/(d-d')$。

核心部分代码实现如下：

```c
/* Initialize c,d and X */
BN_rand_range(c,order);
BN_rand_range(d,order);
EC_POINT_mul(ec_group,X,c,Q,d,ctx);/*X=cP+dQ*/
	
/* Copy c,d,X to c',d',X' */
BN_copy(cc,c);
BN_copy(dd,d);
EC_POINT_copy(XX,X);
	
count = 0;
	
/* Start the iteration */
while(count < MAX_COUNT) {
	/* Iterate c,d,X for one step */
	iterate(X,c,d,c,d);
	/* Iterate c',d',X' for two steps */
	iterate(XX,cc,dd,cc,dd);
	iterate(XX,cc,dd,cc,dd);
	/* Stop when X=X' */
	if(EC_POINT_cmp(ec_group,X,XX,ctx)==0) break;
		
	count++;
}
```
	
/* If d=d' then fails */
if(BN_cmp(d,dd)==0) return 0;
	
BN_mod_sub(c,c,cc,order,ctx);
BN_mod_sub(d,d,dd,order,ctx);
BN_mod_inverse(k,d,order,ctx);
BN_mod_mul(k,k,c,order,ctx);
	
目前已实现的迭代函数：

## 原始的 Pollard rho 迭代

预处理：

1. 选取常数 $L = 32$，随机产生 32 个随机点 $R_i = c_i P+d_i Q$，$0 \leq i < 32$；
2. 选取分类函数 $H$ 为将椭圆曲线上的点 $X = (x,y)$ 映射到 $x \mod L$ 的余数上。

迭代过程：

1. 计算 $j = H(X)$；
2. 令 $X = X + R_i，c = c + c_i，d = d + d_i$。

迭代过程代码如下：
	
```c
j = H(X);
	
/* Add R[j] to X (only operate on c,d) */
BN_add(nc,c,Rc[j]);
BN_add(nd,d,Rd[j]);
EC_POINT_add(ec_group,X,X,R[j],ctx);
```

## Wiener and Zuccherato 迭代

预处理：

1. 前面两步与原始的 Pollard rho 迭代相同；
2. 计算 Frobenius 变换的特征值 lambda，计算出 lambda 从 0 到 112 次方并存储。

迭代过程：

1. 计算 j = H(X)；
2. 令 X = X + R_i，c = c + c_i，d = d + d_i；
3. 对 i 从 0 到 112，计算 sigma^i(X)，从中选取横坐标 x 最小的 sigma^i(X) 作为最终结果；
4. 令 c = c lambda^i，d = d lambda^i。

迭代过程代码如下：

```c
j = H(X);
	
/* Add R[j] to X (only operate on c,d) */
BN_add(sc,c,Rc[j]);
BN_add(sd,d,Rd[j]);
EC_POINT_add(ec_group,X,X,R[j],ctx);
	
EC_POINT_get_affine_coordinates_GF2m(ec_group,X,tmp2,NULL,ctx);
EC_POINT_copy(tX,X);
besti = 0;
/* Apply Frobenius map */
for(i = 1; i < 113; ++i){
sigma(tX,tX);
EC_POINT_get_affine_coordinates_GF2m(ec_group,tX,tmp1,NULL,ctx);
result = BN_cmp(tmp1,tmp2);
	if(result < 0){
	  besti = i;
	  BN_copy(tmp2,tmp1);
	  EC_POINT_copy(X,tX);
	}
}
BN_mod_mul(nc,sc,lambdas[besti],order,ctx);
BN_mod_mul(nd,sd,lambdas[besti],order,ctx);
```

并行化的 Pollard rho 算法（尚未实现）：

1. 设有一个线程作为 Server，$N$ 个 Client 线程作为计算节点；
2. 选定一类“特殊”的椭圆曲线上的点集，比如将横坐标 $x$ 的前 30 个比特为零的点看做“特殊”点；
3. 每个 Client 线程各自随机选择初始点 X 运行 Pollard rho 算法，每次迭代令 $X = f(X)$；
4. 若发现 $X$ 为“特殊”点，将 $X$ 和对应的 $c$ 和 $d$ 报告 Server；
5. Server 接受所有 Client 传来的 $(X,c,d)$ 并存储，若发现有两个 $X$ 相同但对应的 $d$ 不同则计算 $k = (c'-c)/(d-d')$ 并输出结束程序。