> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Semaine 4 — Blockchain + Mining + Réseau

**Objectif :** Comprendre le consensus, le minage et le réseau P2P.
**Période :** 15 - 19 juin 2026

---

## Lundi 15 juin — La chaîne de blocs

### Height

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/height/)

> Contenu à fetch

**Complément :** [Block Height And Forking](https://developer.bitcoin.org/devguide/block_chain.html#block-height-and-forking) — la hauteur comme identifiant de position dans la chaîne.

### Longest Chain

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/longest-chain/)

> Contenu à fetch

### Chain Reorganization

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/chain-reorganization/)

> Contenu à fetch

**Complément :** [Detecting Forks](https://developer.bitcoin.org/devguide/block_chain.html#detecting-forks) — comment les nœuds détectent et gèrent les forks.

---

## Mardi 16 juin — Forks et attaques

### 51% Attack

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/51-attack/)

> Contenu à fetch

### Hard Fork

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/hard-fork/)

> Contenu à fetch

**Complément :** [Consensus Rule Changes](https://developer.bitcoin.org/devguide/block_chain.html#consensus-rule-changes) — comment les changements de consensus sont déployés.

### Soft Fork

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/blockchain/soft-fork/)

> Contenu à fetch

---

## Mercredi 17 juin — Processus de minage

### Memory Pool

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/memory-pool/)

> Contenu à fetch

**Complément :** [Transaction Broadcasting — Memory Pool](https://developer.bitcoin.org/devguide/p2p_network.html#transaction-broadcasting) — comment les transactions arrivent dans le mempool.

### Candidate Block

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/candidate-block/)

> Contenu à fetch

**Complément :** [Block Prototypes — getblocktemplate](https://developer.bitcoin.org/devguide/mining.html#block-prototypes) — construction d'un bloc candidat via RPC.

### Target

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/target/)

> Contenu à fetch

---

## Jeudi 18 juin — Récompenses

### Coinbase Transaction

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/coinbase-transaction/)

> Contenu à fetch

**Complément :** [Coinbase Input](https://developer.bitcoin.org/reference/transactions.html#coinbase-input) — format spécial de l'input d'une coinbase.

### Block Reward

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/mining/block-reward/)

> Contenu à fetch

---

## Vendredi 19 juin — Réseau P2P

### Node

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/networking/node/)

> Contenu à fetch

**Complément :** Guides développeur P2P Network :
- [Introduction](https://developer.bitcoin.org/devguide/p2p_network.html)
- [Peer Discovery](https://developer.bitcoin.org/devguide/p2p_network.html#peer-discovery)
- [Connecting To Peers](https://developer.bitcoin.org/devguide/p2p_network.html#connecting-to-peers)
- [Initial Block Download](https://developer.bitcoin.org/devguide/p2p_network.html#initial-block-download)

### Magic Bytes

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/networking/magic-bytes/)

> Contenu à fetch

**Complément :** [Message Headers](https://developer.bitcoin.org/reference/p2p_network.html#message-headers) — format des en-têtes de messages P2P (magic + command + length + checksum).

---

## Modes de fonctionnement (bonus developer.bitcoin.org)

| Mode | Description | Guide |
|------|-------------|-------|
| **Full Node** | Télécharge et vérifie toute la chaîne | [Operating Modes](https://developer.bitcoin.org/devguide/operating_modes.html) |
| **SPV** | Vérification simplifiée (headers seulement) | [SPV](https://developer.bitcoin.org/devguide/operating_modes.html#simplified-payment-verification-spv) |

---

## Résumé de la semaine

| Concept clé | Description |
|-------------|-------------|
| **Height** | Numéro d'un bloc depuis le genesis (0) |
| **Longest Chain** | La chaîne avec le plus de travail accumulé gagne |
| **Chain Reorg** | Remplacement d'une partie de la chaîne par une plus longue |
| **51% Attack** | Un mineur dominant peut réécrire l'historique |
| **Mempool** | File d'attente des transactions non confirmées |
| **Coinbase** | Première transaction d'un bloc — récompense du mineur |
| **P2P Protocol** | Communication entre nœuds (magic bytes, messages, inv) |


<a href="#top">⬆ Retour au menu / Back to top</a>

