> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Semaine 3 — Transactions (partie 2) + Blocs

**Objectif :** Terminer les transactions, puis comprendre la structure des blocs.
**Période :** 8 - 12 juin 2026

---

## Lundi 8 juin — Witness et frais

### Witness

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/witness/)

> Contenu à fetch

**Complément :** [Segregated Witness](https://developer.bitcoin.org/devguide/transactions.html) — les données witness sont séparées du corps de la transaction.

### wTXID

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/wtxid/)

> Contenu à fetch

### Fee

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/fee/)

> Contenu à fetch

**Complément :** [Transaction Fees And Change](https://developer.bitcoin.org/devguide/transactions.html#transaction-fees-and-change) — calcul des frais et de la monnaie rendue.

### Size

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/size/)

> Contenu à fetch

**Complément :** [Transaction Malleability](https://developer.bitcoin.org/devguide/transactions.html#transaction-malleability) — pourquoi le wTXID résout le problème de malléabilité.

---

## Mardi 9 juin — Finalisation transactions

### Locktime

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/locktime/)

> Contenu à fetch

**Complément :** [Locktime And Sequence Number](https://developer.bitcoin.org/devguide/transactions.html#locktime-and-sequence-number) — transactions à temps verrouillé (nLockTime, nSequence).

### PSBT

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/psbt/)

> Contenu à fetch

**Complément :** [RPC PSBT](https://developer.bitcoin.org/reference/rpc/rawtransactions.html) — createpsbt, decodepsbt, finalizepsbt, walletprocesspsbt.

---

## Mercredi 10 juin — Structure d'un bloc

### Version

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/version/)

> Contenu à fetch

**Complément :** [Block Versions](https://developer.bitcoin.org/reference/block_chain.html#block-versions) — historique des versions de blocs (V1→V4, BIP9).

### Previous Block

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/previous-block/)

> Contenu à fetch

### Merkle Root

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/merkle-root/)

> Contenu à fetch

**Complément :** [Merkle Trees](https://developer.bitcoin.org/reference/block_chain.html#merkle-trees) — construction officielle de l'arbre de Merkle.

---

## Jeudi 11 juin — Minage d'un bloc

### Time

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/time/)

> Contenu à fetch

### Bits

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/bits/)

> Contenu à fetch

**Complément :** [Target nBits](https://developer.bitcoin.org/reference/block_chain.html#target-nbits) — encodage compact de la difficulté.

### Nonce

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/nonce/)

> Contenu à fetch

**Complément :** [Proof Of Work](https://developer.bitcoin.org/devguide/block_chain.html#proof-of-work) — le mécanisme de consensus par preuve de travail.

### Block Hash

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/hash/)

> Contenu à fetch

---

## Vendredi 12 juin — Stockage

### blk.dat

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/block/blkdat/)

> Contenu à fetch

**Complément :** [Serialized Blocks](https://developer.bitcoin.org/reference/block_chain.html#serialized-blocks) — format de sérialisation officiel des blocs.

---

## Résumé de la semaine

| Concept clé | Description |
|-------------|-------------|
| **Witness** | Données de signature séparées du corps de la transaction (SegWit) |
| **wTXID** | ID de transaction incluant les données witness (résout la malléabilité) |
| **PSBT** | Format pour transactions signées de manière collaborative |
| **Merkle Root** | Hash de tous les TXID du bloc — prouve l'intégrité |
| **nBits** | Encodage compact de la difficulté de minage |
| **Nonce** | Nombre incrémenté par le mineur pour trouver un hash valide |


<a href="#top">⬆ Retour au menu / Back to top</a>

