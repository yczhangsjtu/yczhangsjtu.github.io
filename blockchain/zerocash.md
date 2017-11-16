---
title: Zerocash简要介绍
---

# Zerocash简要介绍

假设：已经对区块链和比特币的基础知识比较熟悉。

Zerocash是在比特币上增加一套匿名支付机制得到的货币。这套匿名支付机制叫做DAP（Decentralized Anonymous Payment）方案。按照Zerocash设计者的初衷，DAP可以搭建在任何基于账本的数字货币（比特币和它的山寨币们）上层，提供一套匿名机制，和这个数字货币的具体细节相对独立。我们把底层的数字货币称为基础币。

Zerocash is constructed by overlaying a Decentralized Anonymous Payment (DAP) scheme over Bitcoin or any other ledger-based cryptocurrencies, which we call the basecoin. DAP is designed to be independent of the implementation of the basecoin.

DAP在基础币之上，增加了一套被称为暗币概念。相对地，我们把基础币称为明币。DAP还引入了两类新的交易，一类负责把明币转化为暗币，称为铸币交易，另一类负责进行暗币的支付，称为花币交易。花币交易在将暗币支付为暗币的同时，也可以把部分暗币转回明币。

DAP introduces a new kind of coin called *shielded coin* (by contrast, we call the unspent outputs in basecoin *transparent coins*). DAP also introduces two kinds of transactions to handle shielded coins: the *mint* transactions transform transparent coins into shielded coins, and the *pour* transactions conduct payments between shielded coins. A pour transaction could also transform part of the input shielded coins back to transparent coins.

为了保证暗币系统的交易匿名性，暗币将其所有信息隐藏在一个承诺里。这些信息包括它的面值$v$和它的所属地址。为了和基础币的实现完全独立，DAP引入自己的地址系统，称为暗地址，每个地址也有一个对应的私钥。暗币的承诺是带陷门的，要打开一个暗币的承诺需要知道暗币的面值和用户暗地址，以及这个陷门。

To ensure the anonymity of the shielded coins, the data of a shielded coin is concealed in a information-hiding trapdoor commitment. The data of a shielded coin consist of the denomination and a *shielded address* which is generated from a secret key. To open the commitment requires knowledge about the data of the shielded coin and the trapdoor to the commitment.

铸币交易的输入是明币，输出是等额的暗币（暂不考虑交易费）。为了不打开暗币就能够验证暗币的面值是正确的，对暗币的承诺分两步进行：先承诺除了面值外的信息，得到一个中间承诺，再将中间承诺和面值一起做一个最终承诺。在铸币交易中第二步的承诺相当于是打开的，以便大家能够验证这个交易的合法性。

A mint transaction takes transparent coins as input, and produces one shielded coin with equal denomination (for now we neglect the transaction fee). For others to check that the transaction issuer has committed the correct denomination into the shielded coin, the commitment is conducted in two steps: all the data except the denomination are committed into an intermediary commitment, which is then committed together with the denomination to obtain to final commitment. The second commitment is literally opened in the mint transaction for others to verify.

花币交易的输入是两个暗币，输出是两个新的暗币和可能存在的部分明币，输入和输出的面值相等。除了输出的明币之外，花币交易不泄露任何交易信息，连输入的两个暗币的承诺也隐藏了起来。花币交易的合法性由零知识证明来保证。生成花币交易的用户需要为以下声明提供零知识证明：账本上存在两个暗币的承诺，我知道打开它们所需的信息；这两个新的暗币是合法生成的，我也知道打开它们所需的信息；输入和输出的面值的和相等。

A pour transaction takes two shielded coins as input, and produces two newly generated shielded coins, together with a (possibly zero-value) transparent coin. The pour transaction reveals nothing but the commitments to the output shielded coins. The validity of a pour transaction is gauranteed by zero-knowledge proof. The transaction issuer is expected to provide zero-knowledge proof for the following statement: the input shielded coins are two shielded coins on the ledger whose commitments I can open; the output shielded coins are valid and I can open them; the input and the output are balanced.

当Alice用暗币支付Bob（比如1暗币）时，Alice找出自己的两枚暗币作为花币交易的输入，输出两枚暗币，其中一枚面值为1，地址为Bob的地址，另外一枚是Alice给自己的找零。当花币交易上链之后，Alice把地址为Bob的暗币中打开承诺的信息发送给Bob，Bob就可以花费这枚暗币了。但是，此时Alice也可以花费这枚暗币，因此这枚暗币并没有完全属于Bob。为了解决这个问题，我们要求花币交易的零知识证明中，增加一个声明：我知道一个私钥，它和承诺中的公钥是对应的。

When Alice pays Bob by one shielded coin, Alice chooses two of her shielded coins whose denominations sum to larger or equal to one. She issues a pour transaction with these two coins as input, and produces two new shielded coins, one of which commits in it denomination one and the address of Bob, another pays the rest values back to Alice. After the pour transaction gets on the ledger, Alice sends Bob all the data for opening the commitment for his shielded coin. Note that at this time both Alice and Bob is able to spend this shielded coin. To solve this issue, we add another zero-knowledge proof in the pour transaction, stating: I know the secret key corresponding to the address committed in the coin.

为了防止双花，我们给暗币定义一个序列号作为这枚暗币的唯一标识符，当这个暗币被花掉时，它的序列号要公布出来。一个序列号第二次出现时，包含它的交易则被视为双花。为了保证隐私，一个暗币对应的序列号不应当被暗币拥有者以外的任何人知道。因此我们要求这个序列号的生成过程需要包含暗币拥有者的私钥。为保证其唯一确定性，暗币中需要承诺生成序列号所需的随机数。暗币的所有者花掉这个币时，需要零知识证明这个序列号确实是用自己的私钥和暗币中承诺的随机数生成的。

Now it is possible to issue two pour transactions spending the same shielded coin without being found out. To prevent such double-spend, we define for each shielded coin a serial number which serves as a unique identifier and is revealed as the shielded coin is spent. When a serial number appears for the second time, the containing pour transaction is considered double-spending. For anonymity, the serial number should be kept secret to the coin owner. To accomplish this, we bind the serial number of a shielded coin with the secret key of the owner. For uniqueness of a serial number among all serial numbers belonging to one user, the serial number should also take a random string which is committed into the shielded coin. For the owner to spend a shielded coin, he reveals the serial number, and zero-knowledge proves that he knows the secret key which results in the serial number with the random string committed in the coin.

以上是零币的主要思想。此外还有许多细节问题，不再详细陈述，只将一些主要的问题及其解决思想列在这里。

* 证明一个暗币属于链上所有暗币的集合：所有暗币维护在一个Merkle树当中，树根随着每一个暗币的加入变动一次。证明一个暗币的承诺是Merkle树的叶子，需要提供一个树根曾经在树根的历史上存在过，以及从这个叶子到该树根的路径。
* Alice把打开暗币承诺所需的信息发送给Bob，可以将这些信息用Bob的公钥加密，并把密文也附在花币交易中。这样，每个用户的地址中，需要额外包含一个用于加密的公钥。
* 花币交易需要将一部分暗币转换会明币。这个明币的地址是公开的，因此没有受到零知识保护，容易被篡改。为了保护花币交易中所有可能存在的公开信息，交易需要包含一个签名，只不过签名用的公钥是每次临时生成的。而公钥本身则用零知识保护起来。

Above are the main ideas of Zerocash. There are many other issues which are already solved but we will only provide a brief description to save space.

* To prove the existence of a shielded coin commitment on the ledger, all commitments are maintained in a Merkle-tree, and the witness for membership in the leaves is a path from the leaf to the root.
* To enable Alice to send Bob all the data needed to open a commitment, Alice encrypts the data with Bob's public key, and append the ciphertext in the pour transaction. Thus each address additionally contains a public key for encryption.
* To protect all the public information in the pour transaction (for example, the part of the denomination that transforms back to transparent coin), the pour transaction should be protected by a signature, whose verification key is generated on the fly, and protected by zero-knowledge proof.
