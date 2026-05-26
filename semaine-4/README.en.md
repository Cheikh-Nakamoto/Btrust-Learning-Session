> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Week 4 — Blockchain + Mining + Network

**Objective:** Understand consensus, mining, and the P2P network.
**Period:** June 15 - 19, 2026

---

## Monday June 15 — The Blockchain

### Height

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/height/)

> Content to fetch

**Complement:** [Block Height And Forking](https://developer.bitcoin.org/devguide/block_chain.html#block-height-and-forking) — height as a position identifier in the chain.

### Longest Chain

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/longest-chain/)

> Content to fetch

### Chain Reorganization

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/chain-reorganization/)

> Content to fetch

**Complement:** [Detecting Forks](https://developer.bitcoin.org/devguide/block_chain.html#detecting-forks) — how nodes detect and handle forks.

---

## Tuesday June 16 — Forks and Attacks

### 51% Attack

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/51-attack/)

> Content to fetch

### Hard Fork

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/hard-fork/)

> Content to fetch

**Complement:** [Consensus Rule Changes](https://developer.bitcoin.org/devguide/block_chain.html#consensus-rule-changes) — how consensus changes are deployed.

### Soft Fork

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/soft-fork/)

> Content to fetch

---

## Wednesday June 17 — Mining Process

### Memory Pool

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/memory-pool/)

> Content to fetch

**Complement:** [Transaction Broadcasting — Memory Pool](https://developer.bitcoin.org/devguide/p2p_network.html#transaction-broadcasting) — how transactions enter the mempool.

### Candidate Block

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/candidate-block/)

> Content to fetch

**Complement:** [Block Prototypes — getblocktemplate](https://developer.bitcoin.org/devguide/mining.html#block-prototypes) — building a candidate block via RPC.

### Target

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/target/)

> Content to fetch

---

## Thursday June 18 — Rewards

### Coinbase Transaction

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/coinbase-transaction/)

> Content to fetch

**Complement:** [Coinbase Input](https://developer.bitcoin.org/reference/transactions.html#coinbase-input) — special input format of a coinbase.

### Block Reward

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/block-reward/)

> Content to fetch

---

## Friday June 19 — P2P Network

### Node

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/networking/node/)

> Content to fetch

**Complement:** Developer P2P Network guides:
- [Introduction](https://developer.bitcoin.org/devguide/p2p_network.html)
- [Peer Discovery](https://developer.bitcoin.org/devguide/p2p_network.html#peer-discovery)
- [Connecting To Peers](https://developer.bitcoin.org/devguide/p2p_network.html#connecting-to-peers)
- [Initial Block Download](https://developer.bitcoin.org/devguide/p2p_network.html#initial-block-download)

### Magic Bytes

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/networking/magic-bytes/)

> Content to fetch

**Complement:** [Message Headers](https://developer.bitcoin.org/reference/p2p_network.html#message-headers) — P2P message header format (magic + command + length + checksum).

---

## Operating Modes (bonus developer.bitcoin.org)

| Mode | Description | Guide |
|------|-------------|-------|
| **Full Node** | Downloads and verifies the entire chain | [Operating Modes](https://developer.bitcoin.org/devguide/operating_modes.html) |
| **SPV** | Simplified verification (headers only) | [SPV](https://developer.bitcoin.org/devguide/operating_modes.html#simplified-payment-verification-spv) |

---

## Week Summary

| Key Concept | Description |
|-------------|-------------|
| **Height** | Block number from genesis (0) |
| **Longest Chain** | The chain with the most accumulated work wins |
| **Chain Reorg** | Replacing part of the chain with a longer one |
| **51% Attack** | A dominant miner can rewrite history |
| **Mempool** | Queue of unconfirmed transactions |
| **Coinbase** | First transaction in a block — miner's reward |
| **P2P Protocol** | Node communication (magic bytes, messages, inv) |

---

## Next Week

[Week 5 — Scripts + Upgrades](../semaine-5/README.en.md) — Script types from P2PK to P2TR, SegWit, Taproot.

