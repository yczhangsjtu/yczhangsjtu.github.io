---
title: Hawk - The Blockchain Model of Cryptography and Privacy-Preserving Smart Contracts
---

# Hawk: The Blockchain Model of Cryptography and Privacy-Preserving Smart Contracts

[TOC]

## Preview

### Preview Questions

* What is the conclusion of the researcher?
    * **Hawk** can be used as a decentralized smart contract system
    * **Hawk** retains transactional privacy from the public's view
    * **Hawk** allows programmer to write a smart contract in intuitive manner
    * The **blockchain model of cryptography** presented in this paper is formal and could demonstrate the security of **Hawk** system
* What are the methods used by the researcher?
* What kind of experiment is done for the conclusion?
    * The major conclusion is reached by design and evaluation, only a little experiment is carried out solely for the purpose of evaluating the performance of the model.
* Are there any problems left?

### Questions from Preview
* **Hawk** compiles program into protocols, then what kind of protocol are they? Is it like each protocol defines an entire currency system, or just a type of transaction in a larger currency system?
* What is the meaning of existence of **manager**?
    * Manager exists because someone has to execute the **smart contract**, since in reality there is no such a blockchain oracle.
* There is a compilation procedure of the **Hawk** program. Can the protocols be modified in the fly, i.e. the currency system is already in use? If not, the programmability is actually not better than Zerocash, right?

### Structure of the Paper

* Intruduction
    * Why this paper?
    * Hawk Overview
        * The structure of Hawk 
        * The security of Hawk
    * Example of how Hawk can be used
    * Contributions
    * Background and related work
* The Blockchain Model of Cryptography
* Cryptography Abstractions
    * Abstraction of what Hawk want to achieve
* Cryptographic Protocols
    * Private money transfer
    * Privacy and programmable logic
    * Extensions
* Using SNARK
* Implementation and Evaluation
    * Compiler implementation
    * Examples of the Hawk usage
    * Performance evaluation

## Introduction

### Why is this paper?
* Blockchain is powerful tool.
* It is assumed that the consensus system is secure, the blockchain is viewed as a centralized party trusted with correctness and availability but no privacy.
* The present blockchain lacks **transactional privacy**.
* Although there already exists Zerocash, they lacks **programmability**.

### Hawk overview

#### Structure of Hawk
* **Hawk** is a framework for building privacy preserving smartcontracts.
* **Hawk** compiler compiles a Hawk program into a cryptographic protocol **between the blockchain and users**.
* **Hawk** program contains two parts:
    * Private portion: $\phi_{priv}$
    ```flow
    input=>start: Party's Input Data
    output=>end: Payout Distributions amongst Parties
    private=>operation: Private Part

    input->private->output
    ```
    * Public portion: $\phi_{pub}$ does not touch private data or money
* **Hawk** compiler will compile the **Hawk** program into several pieces:
    * The blockchain's program (like Bitcoin script)
    * A program to be executed by users
    * A program to be executed by **manager**

#### Security of Hawk 
* On-chain privacy
    * privacy is provided against the public
    * privacy is implemented by encryption of data and zero-knowledge proof of encrypted data
* Contractual security
    * confidentiality and authenticity
    * financial faireness in the presence of cheating and aborting behavior
* Minimally trusted manager
    * Manager can see but not disclose users' private data
    * Manager is not trusted
    * Manager aborted from the protocol will be penalized
    * Manager can be instantiated with trusted computing hardware

### Example of usage of Hawk
* **Hawk** can be used to implement second price sealed auction
* The program contains preprocessing instructions, and the definition of two functions
    ```
    HawkDeclareParties(Seller,/* N parties */);
    HawkDeclareTimeouts(/* hardcoded timeouts */);
    
    // Private portion
    private contract auction(Inp &in, Outp &out) {
    }
    
    // Public portion
    public contract deposit {
    }
    ```
* **Hawk** compiles this program into cryptographic protocol

### Contributions
* First to provide privacy and programmability at the same time
* Present a **Universal Composability (UC)** model for the blockchain model of cryptography
* Present a new cryptography suite
* Implemented the Hawk prototype and evaluated its performance

> The framework of universal composability (UC) is a general-purpose model for the analysis of cryptographic protocols. It guarantees very strong security properties. Protocols remain secure even if arbitrarily composed with other instances of the same or other protocols. 
<https://en.wikipedia.org/wiki/Universal_composability>

### Related work
* Financial fairness in protocol design
* Smart contracts
* Programming frameworks for cryptography

## The Blockchain Model of Cryptography

### The Blockchain Model

* The blockchain here is regarded as a **trusted third party**, and is trusted for **correctness** and **availability**, but not for **privacy**.
* The blockchain maintains a global ledger, storing all the transactions between pseudonyms.
* The blockchain executes user-defined programs[^footnote].
* The blockchain is aware of a **discrete clock** incrementing in *rounds/epochs*.
* Messages sent to the blockchain will arrive at the **beginning** of the next round.
* Within the same round, the order of messages could be **arbitrarily changed** by the adversary.
* All parties have **reliable channel** to the blockchain, not blocked by adversary.
* Users can make up an **unbounded polynomial number of pseudonyms**.
[^footnote]: How does blockchain executes user defined program?
  
### Formal Model of the Blockchain

* Ideal specifications will be denoted `Ideal`, the blockchain program denoted `B`, the user/manager program `UserP`.
* **Compile** procedures are modeled as wrappers.
    * The **ideal wrapper** $\mathcal{F}$ transforms an `IdealP` into a UC ideal functionality
    * The **blockchain wrapper** $\mathcal{G}$ transforms a blockchain program `B` to a blockchain functionality
    * The **protocol wrapper** $\Pi$ transforms a `UserP` into a user-side or manager-side protocol
    
### Conventions (to be filled)

## Cryptography Abstractions

* Overview: **Hawk** realizes the following specifications:
    * Private ledger and currency transfer (which **ZeroCash** is meant to implement).
    * **Hawk** specific primitives. Including **freeze, compute and finalize** that are essential for enabling transaction privacy and programmability simultaneously.
* Note that from now on, everything is divided into these two parts: **cash** and **hawk**.

### Private Cash Specification

* The **mint** and **pour** terminology is adopted from **ZeroCash**.
* For the **pour** operation, the adversary could learn the output pseudonyms $\mathcal{P_1}$ and $\mathcal{P_2}$, which can be generated on the fly, and nothing else is learned.
* Some other sutlties (to be filled).

### Hawk Specification

* **Freeze**. A party tell the `IdealP` to remove a coin from private coin pool `Coins` and add it to `FrozenCoins`.
* **Compute**. When a party calls `Compute`, its private input and value of its frozen coin is disclosed to the manager.
* **Finalize**. The manager submits a public input to `IdealP`, and the `IdealP` redistributes all the frozen coins.

## Cryptographic Protocols

* Like before, the protocols consists of two parts, the **private cash** parts and **Hawk** parts.
* The **private cash** part is borrowed from **Zerocash**, managing direct money transfer between users. The definition is based on **simulation** version of the definition of the **DAP Scheme**. The details can be found in **Zerocash: Decentralized Anonymous Payments from Bitcoin** and **Accountable Privacy for Decentralized Anonymous Payments**.

### Binding Privacy and Programmable Logic

* The **Zerocash** is obviously nonprogrammable. The **Hawk** part of this protocol is to make it so. This part consists of three operations: *freeze*, *compute* and *finalize*. These three operations allows smart contract to be executed.
* **Freeze** is similar to **pour**, which costs the user coins, only it does not pay any other user, but put into a set **FrozenCoins**.
* **Compute** is for the manager to execute **smart contract**. Users send their private input openings to manager, encrypted by manager's public key. The manager then apply the contract on the inputs, to generate the outcome. The manager also generate a zero-knowledge proof for the correctness of the result.
* **Finalize** manager sends the output to Blockchain for verification. The blockchain then redistributes the frozen money according to the outcome.
