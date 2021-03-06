# Lightning Network

## 密钥存储

因为每个Commitment都设计很多一次性用的公私钥对，整个支付通道运行的过程中双方会需要存储大量公私钥对。为了节省存储空间，可以把这些私钥关联起来，有组织地生成。比如，Alice可以生成长度为一百万的私钥链表，每一个私钥都由上一个私钥生成。她从最后一个私钥开始用。最后一个私钥用来生成第一个Commitment用到的全部公私钥对。当第一个Commitment成为历史的时候，Alice可以将最后一个私钥泄露给Bob，来使得第一个Commitment不再有效。同理，第二个Commitment的全部公私钥对由链表上倒数第二个私钥生成，当需要非法化第二个Commitment的时候，把倒数第二个私钥泄露给Bob。这时Bob可以把Alice给他的其他私钥删掉，因为从倒数第二个私钥可以生成倒数第一个私钥。随着时间推移，Bob始终只需要存储Alice的一个私钥即可。而Alice也并不需要存储整个链表，可以只存其中一部分，需要的时候重新进行计算。这样可以大大减少密钥的存储空间。

## 双向通道中的交易费

本来应该不是什么问题，只要双方商量好各出一部分放到交易费里就可以了。不过这一节还考虑到需要一个第三方来协调，似乎有点多此一举了。或者是让不那么合作的一方出交易费，也有点一厢情愿的感觉。总之，这一节完全不知道作者在想些什么。这并不是什么关键问题，暂时略过。

## 向合约支付

似乎只是重复了一下HTLC的功能，不过换种方式来实现。不知道为什么专门用一节来提这件事。

## 闪电网络

可以把支付通道连成一条路，相邻的用户建立HTLC，越往后时间限制越短。这样可以实现去中心化的支付系统。

### 递减时间

Alice通过Bob和Carol向Dave转钱，Alice确定一个随机的R值，两两之间关于Hash(R)建立HTLC，Alice向Bob付钱仅当三天之内Bob能够告诉Alice随机数R的值，Bob和Carol也一样，不过时限为两天，以及Carol和Dave，时限一天。Alice预先告诉Dave随机数R的值，让Dave从Carol那里拿到钱。然后Carol从Bob那里拿到，以此类推。任何一个没能在时限到达前告诉上家R值的用户承担损失（相当于替Alice付了给Dave的钱），不过因为时限递减，每家在拿到R值的时候至少还有一天时间告诉上家。

### 支付金额

支付金额最好小一点，因为整个过程不上链，一旦出问题损失也会小一点。

### 消除失败支付以及重新规划路径

不是很理解这一节到底想解决什么问题，这个问题什么情况下会发生。如果问题是通道没能连到目标，只需要永不再播出R，大家都把这个HTLC忘掉好了。这一节反复提到用同一个哈希发起相反方向的支付通道以抵消的做法，不清楚到底有什么意义。

### 寻找路径

这一节提到了一些网络结构，不过真正高效的闪电网络到底该长什么样还需要深入研究。

### 闪电网络的费用

建立支付路径的时候会麻烦到人家，所以要多少支付点费用。不过这都是细节问题。

## 风险

风险主要来自时间锁的过期。另外，还有些人需要把私钥保存在线上。

攻击者可能会发起大量的通道然后一起结束掉，使得交易填满区块，强迫一个不合法交易的时间锁过期（因为更早的区块都被填满了）。文中提到可以限制每次交易的金额，使其非常小，大额交易用多次小额交易来完成，反正也没什么损失。不过这到底有什么意义？虽然每次交易额很小，但攻击者又不是只能发上一个过期的Commitment。

在线的私钥有可能被黑客攻击泄露，但这个问题又不是闪电网络自己的问题。

文中提到如果通道一方丢失了数据，另一方就有可能偷钱，所以要选一个靠谱的队友。不过，现实中如果真的应用这个技术，一般人也只会和自己的好友建立通道，一般也不会和陌生人建立通道吧！或者是个人和一个大型机构建立通道，那么大型机构需要最起码的信誉，不会为了一点小利冒被客户起诉的风险偷客户的钱，而大型机构如果数据丢失了损失的就不仅仅是通道里的钱了。

文中提出的很多问题的解决方案都只有一个词，“第三方”。感觉这很自相矛盾，因为第三方需要被信任，而从区块链到闪电网络花这么大工夫设计的方案解决的就是信任问题。

闪电网络需要软分叉实现到比特币中，可能造成不稳定。不过这都是其他问题了。

矿工合谋也可以强行把一个不合法的交易到期以偷钱，不过这种攻击实现起来代价太大，难度太高，不大可能存在。

## 区块大小和共识

同样，不知道这一节到底想讲什么。开始讲虽然闪电网络可以大大减少需要上链的交易量，但如果要支持全世界的人使用，还是要增大区块。然后又开始讲矿工选择交易的策略可以增大合法交易上链的概率，还有UTXO和对共识协议的攻击。

## 使用场景

立即支付、小额支付还有跨链支付这些功能都比较明显，但说到复杂的智能合约可以挪到线下来解决，不知道这是哪门子的优点。还有提到交易所可以让客户快速大量地挪动资金，也是不知道和立即支付有什么区别。另外还有交易所不再需要冷钱包，减少了钱被偷的风险，这说得更是莫名其妙。