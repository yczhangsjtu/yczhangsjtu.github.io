# BFT共识机制小结

## 根据时间有哪些分类？Honey Badger BFT属于哪个？

同步，弱同步，异步。

HoneyBadger BFT属于异步。

## PBFT的过程是什么样的？

## HoneyBadger BFT过程是什么样的？

门限加密

ACS算法

可靠广播算法RBC协议

二级制一致算法BA

## PBFT v.s. HoneyBadger BFT

不超过1/3的容错性

封闭网络（没有新的主机加入网络）

通信复杂度均能达到 $O(N)$

PBFT在网络延时很长时无法工作，HoneyBadger BFT仍能正常工作

HB BFT由多个子算法组成，流程更复杂

PBFT中有主节点，HB BFT所有节点角色相同

PBFT将交易交给主节点排序，HB BFT把交易按照节点编号排序

## 怎样实现PBFT节点的动态加入？

假设存在一个安全第三方CA，负责公钥合法检验，维护节点身份信息

新节点首先在CA处注册，然后向全网广播加入请求

该请求触发一种特殊的VIEW-CHANGE过程

