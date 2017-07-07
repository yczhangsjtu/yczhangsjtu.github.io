# Cryptography

## Symmetric Encryption

### What are the advantages of CBC?

### How to attack CBC with IV incrementing by 1?

1. The attacker send $m$ to challenger.
2. Challenger encrypt $m$ with $IV_1$ and get $C_1$, send $C_1$ to attacker.
3. Attacker send $m_0$ and $m_1$ to challenger, where $m_0=(IV_1)\oplus(IV_1+1)\oplus m\| m'$ and $m_1$ is randomly generated.
4. Challenger encrypt $m_0$ using $IV_1+1$ and $m_1$ with $IV_1+2$, and send one of them to attacker randomly.
5. The ciphertext for $m_0$ would be exactly the same to $C_1$. Attacker could tell which ciphertext is from the challenger.

### What is CFB mode?

1. Put $IV$ in the register.
2. Encrypt the register, and take the first $s$ bits to xor $s$ bits of plaintext to get $s$ bits of ciphertext.
3. Put the $s$ bits of ciphertext in the end of register, shifting the first $s$ bits out. Repeat 2.

### What are the advantages of CFB?

### What is OFB mode?

Very similar to CFB, but put the first $s$ bits of the encrypted register in the end of next register instead of the $s$ bits of plaintext.