# Day one

Day one is the genesis block of `timecoin-testnet`, chain id `timecoin-testnet-1`. The genesis domain is `timecoin-testnet.genesis.v1`. A block from this chain is not a valid block for a different domain.

## No classical fallback

The verifier calls ML-DSA-44 for every signature it accepts. A note spend, a contract capability, and a block proposer signature all use that algorithm. A different signature length or a different algorithm fails closed.

Note payloads are encapsulated with ML-KEM-768. The long-term view key used for that encapsulation is ML-KEM-768. The testnet wallet generates that key with the same algorithm. There is no second view-key type.

## What a quantum adversary still has to break

To forge a spend, the adversary needs an ML-DSA-44 forgery on the sighash, or the one-time private key. That private key is inside the ML-KEM ciphertext until the owner spends.

To forge a block, the adversary needs an ML-DSA-44 forgery under the proposer key on the block signing bytes. The signing bytes cover the parent, the height, the slot, the transactions, the uncle hashes, the coinbase, the proposer key, and the state root. The block id is SHAKE-256 over those bytes and the signature.

To read a memo, the adversary needs the recipient view key, or an ML-KEM-768 break that also passes the HMAC. Decapsulation with the wrong key returns a shared secret that fails the MAC.

## Supply does not depend on a hidden witness

Each block mints 50 TIC. Fees are moved, not minted. The coinbase amount has to equal the reward plus the fees. After execution, unspent notes plus contract balances equal supply. Those checks use the public integers. They do not ask a prover to assert them.
