---
title: Schnorr Signature
layout: math
---

# Schnorr Signature

This is a brief description of the Schnorr signature, serving only as a reference.

**Standard version**:

1. Generate random $k$
2. Set $r = g^k$ mod $p$
3. Set $c=$ Hash($m,r$)
4. Set $s=k+cx$
5. Output signature $(c,s)$.
6. Verify the signature by computing $r=g^s y^{-c}$, then check the hash.

**Threshold Version**:

Generate $r=g^k$ by JF-DKG, nobody knows $k$, but each knows some $k\_i$.
Each party publishes $s\_i = k\_i + cx\_i$. Summation of $s\_i$ is $s$.