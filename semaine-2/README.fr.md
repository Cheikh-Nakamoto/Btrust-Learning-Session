> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Semaine 2 — Wallets HD + Transactions (partie 1)

**Objectif :** Maîtriser les HD wallets et la structure des inputs/outputs.
**Période :** 1 - 5 juin 2026

---

## Lundi 1 juin — Clés encodées et signatures

### WIF Private Key

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/private-key/wif/)

> Contenu à fetch

**Complément :** [Wallet Import Format (WIF)](https://developer.bitcoin.org/devguide/wallets.html#wallet-import-format-wif) — encodage officiel pour importer/exporter des clés privées.

### Signature

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/signature/)

> Contenu à fetch

**Complément :** [Signature Hash Types](https://developer.bitcoin.org/devguide/transactions.html#signature-hash-types) — SIGHASH_ALL, SIGHASH_NONE, SIGHASH_SINGLE, SIGHASH_ANYONECANPAY.

---

## Mardi 2 juin — HD Wallets

### HD Wallets

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/)

> Contenu à fetch

**Complément :** [HD Key Creation](https://developer.bitcoin.org/devguide/wallets.html#hd-key-creation) — hardened keys, root seeds, processus de dérivation.

### Mnemonic Seed

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/mnemonic-seed/)

> Contenu à fetch

**Complément :** [Deterministic Wallet Formats (Type 2: HD Wallets)](https://developer.bitcoin.org/reference/wallets.html#type-2-hierarchical-deterministic-hd-wallets) — spécification BIP32.

---

## Mercredi 3 juin — Dérivation

### Extended Keys

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/extended-keys/)

> Contenu à fetch

### Derivation Paths

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/hd-wallets/derivation-paths/)

> Contenu à fetch

---

## Jeudi 4 juin — Structure des inputs

### Input

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/)

> Contenu à fetch

**Complément :** [TxIn (Transaction Input)](https://developer.bitcoin.org/reference/transactions.html#txin) — format sérialisé officiel.

### TXID

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/txid/)

> Contenu à fetch

### VOUT

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/vout/)

> Contenu à fetch

### ScriptSig

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/scriptsig/)

> Contenu à fetch

**Complément :** [P2PKH Script Validation](https://developer.bitcoin.org/devguide/transactions.html#p2pkh-script-validation) — exécution pas-à-pas du script de vérification.

---

## Vendredi 5 juin — Outputs et UTXO

### Sequence

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/input/sequence/)

> Contenu à fetch

**Complément :** [Locktime And Sequence Number](https://developer.bitcoin.org/devguide/transactions.html#locktime-and-sequence-number) — usage officiel du champ sequence.

### Output

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/output/)

> Contenu à fetch

**Complément :** [TxOut (Transaction Output)](https://developer.bitcoin.org/reference/transactions.html#txout) — format sérialisé officiel.

### ScriptPubKey

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/output/scriptpubkey/)

> Contenu à fetch

### UTXO

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/transaction/utxo/)

> Contenu à fetch

**Complément :** [Avoiding Key Reuse](https://developer.bitcoin.org/devguide/transactions.html#avoiding-key-reuse) — pourquoi chaque transaction devrait utiliser de nouveaux UTXO.

---

## Résumé de la semaine

| Concept clé | Description |
|-------------|-------------|
| **WIF** | Format d'import/export de clé privée (Base58Check) |
| **BIP32** | HD wallets — dérivation hiérarchique de clés |
| **BIP39** | Mnemonic seed — 12/24 mots → seed → clés |
| **TxIn** | Référence à un UTXO existant + script de preuve |
| **TxOut** | Montant + script de verrouillage (conditions de dépense) |
| **UTXO** | Unspent Transaction Output — l'unité de base du modèle Bitcoin |


<a href="#top">⬆ Retour au menu / Back to top</a>

