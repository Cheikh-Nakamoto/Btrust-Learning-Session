> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Week 2 — HD Wallets + Transactions (Part 1)

**Objective:** Master HD wallets and the structure of inputs/outputs.
**Period:** June 1 - 5, 2026

---

## Monday June 1 — Encoded Keys and Signatures

### WIF Private Key

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/private-key/wif/)

> Content to fetch

**Complement:** [Wallet Import Format (WIF)](https://developer.bitcoin.org/devguide/wallets.html#wallet-import-format-wif) — official encoding for importing/exporting private keys.

### Signature

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/signature/)

> Content to fetch

**Complement:** [Signature Hash Types](https://developer.bitcoin.org/devguide/transactions.html#signature-hash-types) — SIGHASH_ALL, SIGHASH_NONE, SIGHASH_SINGLE, SIGHASH_ANYONECANPAY.

---

## Tuesday June 2 — HD Wallets

### HD Wallets

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/)

> Content to fetch

**Complement:** [HD Key Creation](https://developer.bitcoin.org/devguide/wallets.html#hd-key-creation) — hardened keys, root seeds, derivation process.

### Mnemonic Seed

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/mnemonic-seed/)

> Content to fetch

**Complement:** [Deterministic Wallet Formats (Type 2: HD Wallets)](https://developer.bitcoin.org/reference/wallets.html#type-2-hierarchical-deterministic-hd-wallets) — BIP32 specification.

---

## Wednesday June 3 — Derivation

### Extended Keys

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/extended-keys/)

> Content to fetch

### Derivation Paths

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/)

> Content to fetch

---

## Thursday June 4 — Input Structure

### Input

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/)

> Content to fetch

**Complement:** [TxIn (Transaction Input)](https://developer.bitcoin.org/reference/transactions.html#txin) — official serialized format.

### TXID

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/txid/)

> Content to fetch

### VOUT

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/vout/)

> Content to fetch

### ScriptSig

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/scriptsig/)

> Content to fetch

**Complement:** [P2PKH Script Validation](https://developer.bitcoin.org/devguide/transactions.html#p2pkh-script-validation) — step-by-step script execution.

---

## Friday June 5 — Outputs and UTXO

### Sequence

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/sequence/)

> Content to fetch

**Complement:** [Locktime And Sequence Number](https://developer.bitcoin.org/devguide/transactions.html#locktime-and-sequence-number) — official usage of the sequence field.

### Output

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/output/)

> Content to fetch

**Complement:** [TxOut (Transaction Output)](https://developer.bitcoin.org/reference/transactions.html#txout) — official serialized format.

### ScriptPubKey

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/output/scriptpubkey/)

> Content to fetch

### UTXO

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/utxo/)

> Content to fetch

**Complement:** [Avoiding Key Reuse](https://developer.bitcoin.org/devguide/transactions.html#avoiding-key-reuse) — why each transaction should use new UTXOs.

---

## Week Summary

| Key Concept | Description |
|-------------|-------------|
| **WIF** | Import/export format for private keys (Base58Check) |
| **BIP32** | HD wallets — hierarchical key derivation |
| **BIP39** | Mnemonic seed — 12/24 words -> seed -> keys |
| **TxIn** | Reference to an existing UTXO + proof script |
| **TxOut** | Amount + locking script (spending conditions) |
| **UTXO** | Unspent Transaction Output — the basic unit of Bitcoin's model |

---

## Next Week

[Week 3 — Transactions (Part 2) + Blocks](../semaine-3/README.en.md) — Witness, PSBT, block structure, mining fields.

