# Distributed Key Generation

## What is Shamir's secret sharing protocol?

The dealer generates a random polynomial $f$ of degree $t$, such that $f(0)$ is the secret to share. He transmits $f(i)$ to party $i$, for $i=1,...,n$.

## What is Feldman's VSS?

To prevent problems occurred in Shamir's protocol, in presence of dishonest parties. Tolerates $n/2$ malicious parties **including the dealer**. The dealer additionally broadcasts $F_k = g^{f_k}$ mod $p$. Everyone checks the result in the exponential space in $\mathbb{Z}_p$.

## What is Pedersen's VSS?

Extend Feldman's VSS by adding a polynomial, and use two generators.

## What is JF-DKG?

Joint Feldman Distributed Key Generation. For $n$ parties, each runs Feldman's VSS as the dealer. Each party take the summation of all the secret shares as the final secret share. The secret $x$ is the summation of all the secrets. The public key is the product of all the $F_0 = g^{f_0}$, which equals $g^x$.

JF-DKG is **inseure**, as are many variants and extensions of JF-DKG.

## What is New-DKG?

Use Pedersen's VSS instead of Feldman's VSS. The problem now is the public key cannot be computed directly from the public verifications. The solution is to run the Feldman's VSS.

## What is Schnorr's Signature Scheme?

1. Generate random $k$
2. Set $r$ to $g^k$ mod $p$
3. Set $c$ to Hash($m,r$)
4. Set $s$ to $k+cx$
5. Set the signature to $(c,s)$.
6. Verify the signature by computing $r$ by $g^s y^{-c}$, then check the hash.

## How to Threshold Schnorr's Signature Scheme using JF-DKG?

Generate $r=g^k$ by JF-DKG, nobody knows $k$, but each knows some $k_i$.
Each party publish $s_i = k_i + cx_i$. Summation of $s_i$ is $s$.