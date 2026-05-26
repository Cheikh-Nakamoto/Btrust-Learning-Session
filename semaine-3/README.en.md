> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Week 3 — Transactions (Part 2) + Blocks

**Objective:** Finish transactions, then understand block structure.
**Period:** June 8 - 12, 2026

---

## Monday June 8 — Witness and Fees

### Witness

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/witness/)

> Content to fetch

**Complement:** [Segregated Witness](https://developer.bitcoin.org/devguide/transactions.html) — witness data is separated from the transaction body.

### wTXID

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/wtxid/)

> Content to fetch

### Fee

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/fee/)

> Content to fetch

**Complement:** [Transaction Fees And Change](https://developer.bitcoin.org/devguide/transactions.html#transaction-fees-and-change) — fee calculation and change output.

### Size

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/size/)

> Content to fetch

**Complement:** [Transaction Malleability](https://developer.bitcoin.org/devguide/transactions.html#transaction-malleability) — why wTXID solves the malleability problem.

---

## Tuesday June 9 — Transaction Finalization

### Locktime

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/locktime/)

> Content to fetch

**Complement:** [Locktime And Sequence Number](https://developer.bitcoin.org/devguide/transactions.html#locktime-and-sequence-number) — time-locked transactions (nLockTime, nSequence).

### PSBT

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/psbt/)

> Content to fetch

**Complement:** [RPC PSBT](https://developer.bitcoin.org/reference/rpc/rawtransactions.html) — createpsbt, decodepsbt, finalizepsbt, walletprocesspsbt.

---

## Wednesday June 10 — Block Structure

### Version

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/version/)

> Content to fetch

**Complement:** [Block Versions](https://developer.bitcoin.org/reference/block_chain.html#block-versions) — block version history (V1->V4, BIP9).

### Previous Block

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/previous-block/)

> Content to fetch

### Merkle Root

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/merkle-root/)

> Content to fetch

**Complement:** [Merkle Trees](https://developer.bitcoin.org/reference/block_chain.html#merkle-trees) — official Merkle tree construction.

---

## Thursday June 11 — Block Mining

### Time

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/time/)

> Content to fetch

### Bits

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/bits/)

> Content to fetch

**Complement:** [Target nBits](https://developer.bitcoin.org/reference/block_chain.html#target-nbits) — compact encoding of difficulty.

### Nonce

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/nonce/)

> Content to fetch

**Complement:** [Proof Of Work](https://developer.bitcoin.org/devguide/block_chain.html#proof-of-work) — the proof-of-work consensus mechanism.

### Block Hash

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/hash/)

> Content to fetch

---

## Friday June 12 — Storage

### blk.dat

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/blkdat/)

> Content to fetch

**Complement:** [Serialized Blocks](https://developer.bitcoin.org/reference/block_chain.html#serialized-blocks) — official block serialization format.

---

## Week Summary

| Key Concept | Description |
|-------------|-------------|
| **Witness** | Signature data separated from transaction body (SegWit) |
| **wTXID** | Transaction ID including witness data (solves malleability) |
| **PSBT** | Format for collaboratively signed transactions |
| **Merkle Root** | Hash of all TXIDs in the block — proves integrity |
| **nBits** | Compact encoding of mining difficulty |
| **Nonce** | Number incremented by miner to find valid hash |

---

## Next Week

[Week 4 — Blockchain + Mining + Network](../semaine-4/README.en.md) — Consensus, mining process, P2P protocol.

