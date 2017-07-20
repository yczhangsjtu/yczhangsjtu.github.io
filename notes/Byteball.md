# Byteball

* Applications:
  * Conditional payments.
  * P2P payment in chat.
  * Make money by correctly predicting future events (P2P smart contract that can be unlocked if a specific event occurs).
  * P2P betting.
  * Untraceable currency.
* Technologies:
  * Transactions created by users are cryptographically linked to each other, and once you add your new transaction, other users start adding theirs on top of yours, and the number of other transactions that link to your transaction grows like snowball (that's why we call it Byteball).
* Core features:
  * Atomic exchange.
  * Multi-signature.
  * Immutable storage.
  * When transactions are final, not even a powerful attacker can modify it. No 51% attack.

## Introduction

Byteball is designed to generalize Bitcoin to become a tamper proof storage of **any data**. It is different from Bitcoin in a lot of respects.

* **Storage units**: similar to blocks in blockchain, linked by hash, but forms a DAG instead of a chain (a unit may link to multiple previous ones). Transactions trying to double spend may both get on the DAG, but only one will be recognized by the **main chain** in the DAG, selected by **witness users**.
* **Witnesses** are reputable users in real world. It is expected that a majority of witnesses are honest.
* **PoW**: NO PoW. Based on another consensus algorithm based on an old idea that was known long before
  Bitcoin.
* **Finality of transaction**: In Bitcoin, there is no black-white criteria that a transaction is **final**, instead there is always a probability that a a transaction is **irreversible**, which is decaying exponentially. This probability is even related to the amount of the transaction. In Byteball, **there are such deterministic criteria**.
* **Price**: Bitcoin price is not justified, volatile and wild. In Byteball, 1 Byte currency is paid to store 1 byte of information into Byteball database, providing a basis for users to estimate the value of Byte.
* **Privacy**: Transactions in bytes are visible, but there is **blackbyte** significantly less traceable.

## Databse Structure

A user who wants to store information publishes a **storage unit**, which contains:

* Messages: information he wants to store. Can be of several types. The **payment** type of message corresponds to transaction in Bitcoin.
* Signature: verified by the public key which identifies the user.
* Hash links to previous units: can be one or more.

All such units form a directed acyclic graph (DAG).

There is no PoW to generate a unit, so anyone can publish a unit at any time. So a new Byteball unit may start accumulating confirmations immediately after being published, from anyone. The irrevisibility is provided by the fact that modifying a unit requires modifying a great number of units depending on it, thus requires cooperation of many many users.

## Bytes

Now it seems that Byteball can be easily DoS attacked. That's what the introducing of **Byte** prevents from happening.

To store your data in this global decentralized database you have to pay a fee. The number of **Bytes** to pay is equal to the number of bytes to store.

To encourage users to link at least two parents, in counting the size of a unit, the linking part is always assumed to be of the size of two links, regardless of the real number. And to encourage users to include as many and latest parents as possible, the fee for storing each unit is always paid to the **first** unit that follows it.

A **payment** message is similar to a transaction in Bitcoin. It includes an array of inputs and an array of outputs. Each input refers to the input unit (by hash) containing the utxo, the index of the payment message in the input unit and the index of the output of the payment message. Each output contains a target address and the amount.

All the $10^{15}$ bytes are issued in the genesis unit, then transfered from user to user. This number is selected to be the largest round integer that can be represented in JavaScript. Amounts can only be integers. Size units can be KB or MB.

## Double Spends

There is possibility that two units which do not have a partial order spend the same output. Then they are both buried deep in new units. In that case, the one that appears earlier on the total order is deemed valid.

To define the total order, it is required that if the same address posts more than one unit, it should include (directly or indirectly) all its previous units. In other words, all units from the same address should be totally ordered. The units violating this rule is considered as double-spending, even if he is not. As a result, there is no way to spend one unit twice (since the spending units must be of the same address).

When two double-spending units are ordered, the one that loses is called **nonserial unit**. The entire content of a **nonserial unit** is considered invalid.

## The Main Chain

Start from a unit, define an algorithm to pick a **best parent**. The algorithm should be based on the information involving only this unit and all its parents. Follow the link of best parents, one can go from anywhere to the genesis unit. This path is called the **main chain (MC)** of this unit.

Then we index the MC by setting the index of the genesis to 0, and increment it along the chain. For units not in the MC, the index is defined to be that of the first MC unit that includes it. Thus we give an index to all the units in this DAG. The indices defined this way is called **MCI**.

For two double spending units, the one with lower MCI wins. If the MCIs are equal, there would be a tiebreaking: the unit with lower hash value wins.

Choosing a parent is equivalent to choosing a version of history. The best parent selection algorithm should favor the history that is real from the point of view of the child unit.

## Witnesses

Witnesses are people who have long established reputation in real life, or those who are interested in keeping the network healthy.

The **witness level** of a MC is the number of addresses of some witness along this MC (multiple appearances of one address count only once). The unit bearing the highest witness level is selected as the **best parent**. The tie is broken by favoring the parent whose own level is lowest, then the one with lower hash value.

At the same time, the parents themselves may have different witness lists and different definitions of reality. We want the different histories that follow from them to converge around something common, so we need an additional rule: **the near-conformity rule**. That is, the witness list of the best parents must not differ from that of the child by more than one mutation. Such parents are called **compatible** parents. If there is no compatible parent, one should select parents from older units.

Each unit must list its witness list. The size of witness list is fixed to be 12. To save space, if a unit's witness list is the same to an early one, he can simply reference that unit which lists his witnesses explicitly.

## Finality

The current MC will constantly change as new units arrive, yet a part of the current MC that is old enough will stay invariant.

We expect that a majority of witnesses behave honestly, therefore include their previous units in the next unit, thus choose only the recent units as parents. We can expect that there is a stability point on the MC before which the MC will never change.

If we only consider the best parent links, the DAG will be reduced to a tree. If at the stability point the tree does not branch, and that a majority of witnesses lie after the stability point, there is no possibility that another branch will start in the middle of the MC and exceeds the current MC. In that case we can advance the stability point.

If at the stability point the best parent tree branches, even if all the remaining minority witnesses gather on the alternative branches, the witnessed level on the alternative branches will never exceed the maximum level, and if this maximum level is less than the minimum witnessed level in the current MC, then the alternative branch never have a chance to win. We can advance the stability point along the current MC.

The units before the stability point are considered fixed with 100 percent probability.

## Storage of Nonserial Units

To prevent attackers from abusing nonserial units as a way to store data for free, nonserial units are removed from database and replaced by its hash. It is better that they are removed from the database from the beginning: when a unit is considered nonserial and is still childless, the parent selection algorithm excludes them directly.

## Balls

When a unit is stable, a new sturcture called **ball** is constructed based on this unit. A ball includes information about all its ancestor balls (via hashes of balls based on its parents). There is also a flag indicating if it is considered nonserial.

To protect the balls, it is required that each new unit include a hash of the last ball that he knows about (the ball from the last stable unit on MC). This hash can also be used to let users see what their peers think about the stability point of the MC.

It is required that the last ball should never be earlier than those of its parents.

It is further required that, a unit's witness list must be compatible with that of each unit lying on the trailing part of the unit's MC between this unit and the last ball's unit.

## Structure of a Unit

With all the concepts introduced, it is ready to present the structure of a unit, in the form of JSON.

```json
{
  version: '1.0',
  alt: '1', // identifier of alternative currency
  messages: [ {
    app: 'payment', // type of message. This is a payment message
    payload_location: 'inline', // where to find the payload.
                                // 'inline' means just in this message
                                // it may also be 'url' or 'none'
    payload_hash: 'base64code==',
    payload: {
      inputs: [ {
        unit: 'base64code==',
        message_index: 0,
        output_index: 1
      } ],
      outputs: [ {
        {address: 'base64code==', amount: 208},
        {address: 'base64code==', amount: 3505},
      } ]
    },
  } ],
  authors: [ {
    address: 'ENCODING1OF2HASH3OF4PUBLIC5KEY',
    authentifiers: {
      r: 'base64code==' // signature
    }
  } ],
  parent_units: [
    'base64code==',
    'base64code==',
    'base64code'
  ],
  last_ball: 'base64code==',
  last_ball_unit: 'base64code==',
  witness_list_unit: 'base64code=='
}
```

Note that there is no timestamp in this structure, as the Byteball does not rely on clock time, as it is enough to rely on the order of events.

## Commissions

The bytes paid for the size of this unit is called **commision**. Commissions are devided into two parts, the **payload commission** and the **headers commission**.

The headers commission goes to the **first** child that includes it. The payment is spendable only after all the children are stable. Take the children with MCI equal to or 1 more than the MCI of the payer. The hashes of each of these children are concatenated with the hash of the unit lying on the next MCI (relative to the payer), and the child with the smallest hash value wins.

Payload commission goes to witnesses, to incentivize witnesses to post frequently. Payload commision is splitted equally among all witnesses (in the witness list of the payer) quick enough to post within 100 MCI indices after the paying unit. If no witness posted within that interval, they all receive 1/12 of the payload commission. The result is rounded to nearest integer.

To spend the commissions, the following input is used:

```json
inputs: [
  {
    type: "headers_commission",
    from_main_chain_index: 123, // Sweeps through main chain indices
    to_main_chain_index: 196    // add all the commissions together
  },
  {
    type: "witnessing",
    from_main_chain_index: 60,
    to_main_chain_index: 142
  }
]
```

## Performance

The time for a new unit to be confirmed is called the **confirmation time**. The best confirmation time is estimated to be 30 seconds. If a node trusts its peers may assume that once the unit is included by a witness the unit will most likely reach finality and be deemed valid.

## Security

There is no partitioning risk in Byteball, since the part where the majority of witnesses is lost will not advance its stability point, and double spending transactions will never be confirmed.

It is also quite hard to prevent any particular type of data from entering the database.

## Choosing Witnesses

