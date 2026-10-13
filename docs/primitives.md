# Primitives

The testnet uses two lattice algorithms and one hash for domains, commitments, and the block id.

## ML-DSA-44

ML-DSA-44 signs notes, contract capabilities, and blocks. A measured OpenSSL public key for this parameter set is 1334 bytes. A measured signature is 2420 bytes. The testnet treats those encodings as opaque byte strings and verifies them through OpenSSL.

The sighash is SHAKE-256 of the domain `timecoin.sighash.v1` and the canonical transaction with signatures blanked. Blanking the signatures keeps the signed message independent of the signature bytes.

## ML-KEM-768

ML-KEM-768 encapsulates the note payload. A measured ciphertext is 1088 bytes. The shared secret is 32 bytes. The testnet uses that secret twice:

- SHAKE-256 with domain `timecoin.stream.v1` produces the keystream.
- HMAC-SHA256 with domain `timecoin.mac.v1` authenticates the ciphertext.

The MAC check rejects a decapsulation that used the wrong key.

## SHAKE-256

SHAKE-256 also builds note commitments (`timecoin.note.v1`), nullifiers (`timecoin.null.v1`), the Merkle state root, and the block id (`timecoin.blockid.v1`). Hash commitments bind the public amount. They are not an encryption of the amount. The amount remains a separate public integer.

## What the code refuses to do

The repository that runs these checks does not include its own lattice arithmetic. Replacing OpenSSL's ML-DSA or ML-KEM with a private implementation is outside the testnet. A node that cannot call `ML-DSA-44` and `ML-KEM-768` cannot verify or build a block.
