> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Week 5 — Scripts + Upgrades

**Objective:** Master all script types and the SegWit/Taproot upgrades.
**Period:** June 22 - 26, 2026

---

## Monday June 22 — Original Scripts

### P2PK (Pay to Public Key)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2pk/)

> Content to fetch

### P2PKH (Pay to Public Key Hash)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2pkh/)

> Content to fetch

**Complement:** [P2PKH Script Validation](https://developer.bitcoin.org/devguide/transactions.html#p2pkh-script-validation) — step-by-step script execution.

### P2MS (Pay to Multisig)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2ms/)

> Content to fetch

**Complement:** [Standard Transactions — Multisig](https://developer.bitcoin.org/devguide/transactions.html#multisig) — multisig script format.

---

## Tuesday June 23 — P2SH Scripts

### P2SH (Pay to Script Hash)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh/)

> Content to fetch

**Complement:** [P2SH Scripts](https://developer.bitcoin.org/devguide/transactions.html#pay-to-script-hash-p2sh) — the script hash mechanism.

### P2SH-P2WPKH

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh-p2wpkh/)

> Content to fetch

### P2SH-P2WSH

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh-p2wsh/)

> Content to fetch

---

## Wednesday June 24 — Native SegWit Scripts and Taproot

### P2WPKH (Pay to Witness Public Key Hash)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2wpkh/)

> Content to fetch

### P2WSH (Pay to Witness Script Hash)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2wsh/)

> Content to fetch

### P2TR (Pay to Taproot)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2tr/)

> Content to fetch

---

## Thursday June 25 — Upgrades

### OP_RETURN

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/return/)

> Content to fetch

**Complement:** [Null Data (OP_RETURN)](https://developer.bitcoin.org/devguide/transactions.html#null-data) — standard transaction for arbitrary data.

### Segregated Witness

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/upgrades/segregated-witness/)

> Content to fetch

**Complement:** [Standard Transactions](https://developer.bitcoin.org/devguide/transactions.html#standard-transactions) — complete list of standard transaction types.

---

## Friday June 26 — Taproot

### Taproot

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/upgrades/taproot/)

> Content to fetch

---

## OpCodes (Reference)

**Source:** [developer.bitcoin.org — OpCodes](https://developer.bitcoin.org/reference/transactions.html#opcodes)

Complete reference of all Bitcoin opcodes. The most important ones to remember:

| Opcode | Description |
|--------|-------------|
| `OP_DUP` | Duplicates the top stack item |
| `OP_HASH160` | SHA256 + RIPEMD160 |
| `OP_EQUALVERIFY` | Checks equality of two items |
| `OP_CHECKSIG` | Verifies an ECDSA signature |
| `OP_CHECKMULTISIG` | Verifies N of M signatures |
| `OP_RETURN` | Marks output as unspendable (data) |
| `OP_CHECKSIGADD` | Taproot — aggregated signature verification |

---

## Week Summary

| Key Concept | Description |
|-------------|-------------|
| **P2PKH** | Classic script — public key hash + signature |
| **P2SH** | Script hash — enables complex conditions without revealing them |
| **P2WPKH** | P2PKH in SegWit — witness data separated |
| **P2TR** | Taproot — Schnorr + MAST for efficiency and privacy |
| **SegWit** | Signature separation -> solves malleability, increases capacity |
| **Taproot** | Schnorr + Merklized Abstract Script Trees -> more efficient scripts |

---

## Quick Reference Links

| Resource | URL |
|----------|-----|
| Developer Guides | https://developer.bitcoin.org/devguide/ |
| Reference | https://developer.bitcoin.org/reference/ |
| RPC API | https://developer.bitcoin.org/reference/rpc/ |
| P2P Messages | https://developer.bitcoin.org/reference/p2p_network.html |
| Glossary | https://developer.bitcoin.org/glossary.html |

