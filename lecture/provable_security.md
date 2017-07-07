# Provable Security

## One-way functions and permutations

### What is a one way function?

A function `f` is one way function if it can be computed by some algorithm in polynomial time, but no PPT adversary `A` can compute `x` from `f(x)` with non-negligible probability.

### What is a hard-core predicates for one-way function?

A function `h(x)` which maps `x` to a bit `b`, is hard-core of `f(x)`, if for every PPT adversary `A`, given `f(x)`, `A` cannot compute `h(x)` with probability far from `1/2` by more than a negligible amount.

### How to construct a pseudorandom generator from a one-way permutation?

If `f(x)` is a one-way permutation, with hard-core function `h(x)`, then `g(x)=(f(x),h(x))` is a pseudorandom generator from `n`-bit input to `n+1`-bit output.

### What does Goldreich-Levin Theorem say?

Let `f(x)` be a one-way function, define `g(x,r)=(f(x),r)`, where `x` and `r` are `n`-bit. Then `gl(x,r)` which is defined as the **inner product** of `x` and `r` is a hard-core predicate of `g(x,r)`.

### How to prove Goldreich-Levin Theorem?

1. If `gl(x,r)` is not hard-core predicate of `g(x,r)`, then there is a PPT adversary `A(g(x),r) -> gl(x,r)` computes `gl(x,r)` with probability better than `1/2`. The randomness is from both `x` and `r`.
2. The existence of `A` ensures the existence of a non-negligible subset `S` of all `x` of `n` bits, such that for any `x` in `S`, `A` can compute `gl(x,r)` with probability better than `1/2`. The randomness is from `r`.
3. From adversary `A` we can construct another adversary `A'` which subverts `f(x)`. We only need to assure that `A'` performs better than `1/2` for `x` in `S`, for those outside `S` just guess randomly. Since the size of `S` is non-neligible, such `A'` is sufficient to subvert `f(x)`.
4. We want to construct `A'` in the following way: for each `i=1,2,...,n`, `A'` tries to guess `x[i]`. If `x` is in `S`, this guess has non-negligible probability to guess all `x[i]` correctly.
5. For each `i=1,2,...,n`, let `r=000...00100...00`, where the `1` is at the `i`'th position. Let `A` given `f(x)` and `r` guess `gl(x,r)`, which as defined is exactly `x[i]`. We know that `A` can guess it right with probability better than `1/2`. But this is not enough, since we have to iterate this for all `i`. An idea to improve is to let `A` guess each `x[i]` for more than one time, and vote for the result.
6. To allow `A` to guess for multiple times, we randomly generate `l` bit-strings of `n`-bit, `s_1, s_2, ..., s_l`, and `l` bits `sigma_1, sigma_2, ... , sigma_l` such that `sigma_i = gl(x,s_i)`. Well, we cannot assure that `sigma_i` are right, but if `l` is only logarithmic w.r.t. `n`, then the probability that they are right is non-negligible, that is enough.
7. For each `i`, instead of letting `A` guess from `r=000...00100...000`, we xor `r` with a subset of `{s_i}`, and xor the result with the corresponding subset of `{sigma_i}`. In this way, `A` can guess `2^l-1` times.
8. Note that the choice of `{s_i}` and `{sigma_i}` is once for all bits of `x` to guess. So the discount of `2^l` in probability of success is taken only once.
9. We can prove that `2^l-1` times are enough to make the `n` guesses together non-negligible.

## Pseudorandom Functions and CPA Security

### What is pseudorandom function?

A true random function is a function randomly selected from a large field of functions. A true random function cannot be described by polynomial size.
A pseudorandom function is a function that can be described by short key, but no distinguisher can distinguish it from a true random function. In another word, a pseudorandom function is drawn randomly from a much much smaller subset of the entire function field, but there is no algorithm to distinguish this small subset efficiently.

### How to use pseudorandom function to construct CPU secure private-key encryption scheme?

Generate the key randomly. To encrypt a message, generate $r$ randomly, compute pseudorandom function keyed by $k$ on $r$ and xor the result by the message. Output $r$ and the final result as the ciphertext.

### How to construct pseudorandom function?

The Goldreich-Goldwasser-Micali Construction, construct pseudorandom function from pseudorandom generator.

The pseudorandom generator maps $n$ bits to $2n$ bits.
Apply the pseudorandom generator on $k$ by $n$ times, each time take half of the result. Which half is decided by the corresponding bit in the input $x$.

