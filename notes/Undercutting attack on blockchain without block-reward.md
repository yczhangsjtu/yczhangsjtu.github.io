# Undercutting attack on blockchain without block-reward

We consider performing double-spending attack in a blockchain without block-reward. Assume that we are paying a large amount of Bitcoin to a seller. The transaction is $T$. At the same time we generate another transaction $T'$ which pays the same Bitcoin to ourselves. Only one of $T$ or $T'$ can be included in the blockchain. The attack is successful if $T'$ is included in the blockchain and the seller gives us the product.

We consider the following types of targets (sellers), depending on when he decides that the money is paid and he can give us the product:

1. When he sees the transaction $T$.
2. When he sees the transaction $T$ is published.
3. When he sees the transaction $T$ is in a valid block.
4. When he sees the transaction $T$ is in a valid block followed by six blocks.

First, consider the double-spending attack in blockchain where block-reward is still dominant, to compare and see clearly which kind of target will be rendered vulnerable by the absence of block-reward.

For the first kind of seller, we just show him the transaction $T$, which will never be published. This kind of seller is vulnerable whether the block-reward is dominant, thus is not taken into consideration.

For the second kind of seller, we publish the transaction $T$, and once we get the product, we immediately publish another transaction $T'$. Now we have 1/2 chance of success.

For the third kind of seller, we still have to publish the transaction $T$. If the seller not only cares about the blocks, but also examine each transaction he sees, and he is able to tell that $T'$ is trying to double-spend $T$, we cannot publish $T'$ before the seller gives us the product, i.e. before $T$ gets into a block. After $T$ is in a block, the probability for $T'$ to win is very small. However, there is probability the seller does not care about the transactions and only pays attention to the blocks, then we can publish $T'$ earlier. In that case, we can only hope that both $T$ and $T'$ gets into a block, and $T'$ wins at last. This probability is also tiny. The situation would be better if we have some computational power under control. Again, if the seller is able to detect $T'$ and refuses to give the product, we must publish $T$ first and mine on $T'$ secretly. When the block containing $T$ is published and we get the product, we release the block containing $T'$, then continue to mine following $T'$. If the seller cannot see $T'$, things are easier. We just generate a block containing $T$ by ourselves and show him.

For the last kind of seller, there is no hope that the attack will succeed, even when the block-reward is absent.

