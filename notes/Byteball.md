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

A user who want to store information publish a **storage unit**, which contains:

* Messages: information he wants to store. Can be of several types. The **payment** type of message corresponds to transaction in Bitcoin.
* Signature: verified by the public key which identifies the user.
* Hash links to previous units: can be one or more.

All such units form a directed acyclic graph (DAG).

There is no PoW to generate a unit, so anyone can publish a unit at any time. So a new Byteball unit may start accumulating confirmations immediately after being published, from anyone. The irrevisibility is provided by the fact that modifying a unit requires modifying a great number of units depending on it, thus requires cooperation of many many users.

## Bytes

Now it seems that Byteball can be easily DoS attacked. That's what **Byte** prevents from happening.