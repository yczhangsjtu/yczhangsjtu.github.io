# PERUN - Virtual Payment Channels over Cryptographic Currencies

Channel virtualization is used instead of routing transactions as in Lightning Network. The virtual payment channels are built upon **multistate** channels generalizing the concept of state channels.

Perun has been implemented in Ethereum.

State channels are a generalization of payment channels, enabling users to execute some smart contract inside a channel in an off-chain way. The current state of the channel is represented by a string $\sigma$. The latest version of $\sigma$ is published on the ledger, and the ledger will finish the execution of the smart contract starting for $\sigma$.

* Contributions:
  * **Channel Virtualization** means establishing a channel between Alice and Bob without the ledger, instead with the help of parties connecting them. Alice and Bob only needs to interact with the intermediate parties when the channels are established and closed. Other times they can use the channel directly. In contrast to virtual channels, the usual channels established directly on ledger are called **baisc** channels.
  * **Multistate Channels** are designed to meet the need of virtual channels, but they can be useful without involving virtual channels.
    * Allows Alice to propose a **proposal to update**, which Bob can accept under specific circumstances.
    * It consists of several "independent" contract storage states $\sigma_1,\cdots,\sigma_m$. One cannot simply consider $(\sigma_1,\cdots,\sigma_m)$ as a big $\sigma$, as the multiple states provides tremendous flexibility in presense of update proposals.
  * **NO** full formalization of the protocols or complete proof in the UC-framework.

In comparison, Lightning network only supports payment.

Raiden network developed by Ethereum is still under development, and is still "in its infancy". It is an implementation of state channel.

## The model

* The model is an $n$-party protocol where the parties are connected by secure communication channels.
* There exists adversary who can corrupt any party $P_i$.
* The communication is synchronized, i.e. the execution of the protocol is in rounds.
* A message sent in round $i$ arrives to the destination at the beginning of the next round.
* The adversary can decide the order in which the messages arrive in a round.
* Each party is identified by a public key of a signature scheme.


## Protocols and Contracts

* **Notations**:
  * $\pi$ initial parameter
  * $\sigma$ contract storage
  * $\tau$ time
  * $x,y$ non-negative real value
  * $m,z$ messages, or bit string
* **Protocols**: This protocol consists of a number of subprotocols, each involving some number of parties, consisting some procedures. One of the parties is called the **initiator** of the subprotocol, and runs a procedure with a `Start` in the name.
* **Contracts**: Generally speaking, a **contract** can refer to both **contract code** and **contract instance**.
  * A **contract code** can be presented as a set of functions written in informal pseudocode operating on contract's storage $\sigma$, which is an attribuet tuple.
  * A **contract instance** can be considered as an independent entity that receives coins and messages from the parties and sends coins and messages back to them. We say a party $P$ sends $y$ coins to a contract (instance) $C$, or a contract (instance) $C$ sends $y$ coins to a party $P$.
  * A **contract instance** maintains a storage $\sigma$.
  * A **function** in a **contract code** is considered as a storage transformation, accompanied by messages and coin transfers. The initialization function **Init($\tau$,$\pi$)** is special, which does not have a $\sigma$ as input, but parameters agreed on by the parties denoted by $\pi$. It outputs $\sigma$ by simply letting $\sigma=\pi$ and $\sigma$.**init-time**=$\tau$.
  * _Invoking a function on a contract instance may be considered as generating a transaction, which inputs a contract $C$, (possibly) coins from the parties, and some other parameters, together with the function id, and outputs to $C$ with updated storage, and (possibly) coins to the parties, and some other messages to the parties._
  * In this paper the only contracts considered are **bilateral contracts** that are executed by two parties Alice and Bob. The initial parameter $\pi​$ consists of $\pi​$.**id**, $\pi​$.**Alice** and $\pi​$.**Bob** which are the identifiers of the contract, Alice and Bob respectively. Then there is $\pi​$.**cash** which is a function maping $\{\pi.Alice,\pi.Bob\}​$ to $\mathbb{R}_{\geq0}​$ indicating the value of cash owned by Alice.

## Payment Channel

### Basic payment channel

A basic payment channel is a tuple $\gamma=(\gamma.id,\gamma.Alice,\gamma.Bob,\gamma.cash)$.

The form of $\gamma.id$ consists the information of a tuple $(\gamma.Alice,\gamma.Bob,nonce_1,nonce_2)$ where the nonces are some long random strings. The requirement that it contains so many information is to ensure it's uniqueness. $nonce_1$ is uniquely chosen for the contract instance, and $nonce_2$ is chosen for each channel.

Each channel has a corresponding contract instance $PaymentContract_{\gamma.id}$ on the ledger. Each party maintains a channel space containing all the channels he is participating in.

For simplicity, denote $\gamma.cash$ by $(x_a,x_b)$ implying that $\gamma.cash(\gamma.Alice)=x_a$ and $\gamma.cash(\gamma.Bob)=x_b$. This is the only attribute that can change in this contract.

The protocol `PaymentChannels` consists the following subprotocols:

* **create a channel**: `CreateStart`, `CreateWait`
* **update a channel**: `CloseStart`, `CloseWait`
* **close a channel**: `UpdateStart`, `UpdateWait`

The contract $PaymentContract$ consists the following functions:

* Constructors: $confirm(), refund()$
* Close the contract: $close(), finalize()$


The protocols internally add two attributes $\gamma.vernum$ and $\gamma.sign$ which are invisible to other users. $\gamma.vernum$ is the version number, and $\gamma.sign$ is the signature of a party.

_This looks like a formalization of the Lightning network, with the difference that the parties do not sign anything like penalty in updating the channel. Instead, when closing the channel, the contract waits for the other party to respond with his version of the updatest state, then take the state with higher version number. This looks like a much more straightforward way. The reason that Lightning network does not take this approach may be that Bitcoin script is too simple to implement such functionality. In another word, the smark contract in Bitcoin cannot be abstracted in the way this paper presents._

### Multistate channel

Multistate channel allows users to execute a **nanocontract** inside the channel and totally off the chain.