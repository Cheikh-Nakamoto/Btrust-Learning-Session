> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Semaine 5 — Scripts + Upgrades

**Objectif :** Maîtriser tous les types de scripts et les upgrades SegWit/Taproot.
**Période :** 22 - 26 juin 2026

---

## Lundi 22 juin — Scripts originaux

### P2PK (Pay to Public Key)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2pk/)

> Contenu à fetch

### P2PKH (Pay to Public Key Hash)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2pkh/)

> Contenu à fetch

**Complément :** [P2PKH Script Validation](https://developer.bitcoin.org/devguide/transactions.html#p2pkh-script-validation) — exécution pas-à-pas du script.

### P2MS (Pay to Multisig)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2ms/)

> Contenu à fetch

**Complément :** [Standard Transactions — Multisig](https://developer.bitcoin.org/devguide/transactions.html#multisig) — format des scripts multisig standard.

---

## Mardi 23 juin — Scripts P2SH

### P2SH (Pay to Script Hash)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh/)

> Contenu à fetch

**Complément :** [P2SH Scripts](https://developer.bitcoin.org/devguide/transactions.html#pay-to-script-hash-p2sh) — le mécanisme de hash de script.

### P2SH-P2WPKH

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh-p2wpkh/)

> Contenu à fetch

### P2SH-P2WSH

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2sh-p2wsh/)

> Contenu à fetch

---

## Mercredi 24 juin — Scripts SegWit natifs et Taproot

### P2WPKH (Pay to Witness Public Key Hash)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2wpkh/)

> Contenu à fetch

### P2WSH (Pay to Witness Script Hash)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2wsh/)

> Contenu à fetch

### P2TR (Pay to Taproot)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/p2tr/)

> Contenu à fetch

---

## Jeudi 25 juin — Upgrades

### OP_RETURN

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/script/return/)

> Contenu à fetch

**Complément :** [Null Data (OP_RETURN)](https://developer.bitcoin.org/devguide/transactions.html#null-data) — transaction standard pour données arbitraires.

### Segregated Witness

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/upgrades/segregated-witness/)

> Contenu à fetch

**Complément :** [Standard Transactions](https://developer.bitcoin.org/devguide/transactions.html#standard-transactions) — liste complète des types de transactions standard.

---

## Vendredi 26 juin — Taproot

### Taproot

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/upgrades/taproot/)

> Contenu à fetch

---

## OpCodes (référence)

**Source :** [developer.bitcoin.org — OpCodes](https://developer.bitcoin.org/reference/transactions.html#opcodes)

Référence complète de tous les opcodes Bitcoin. Les plus importants à retenir :

| Opcode | Description |
|--------|-------------|
| `OP_DUP` | Duplique le premier élément de la pile |
| `OP_HASH160` | SHA256 + RIPEMD160 |
| `OP_EQUALVERIFY` | Vérifie l'égalité des deux éléments |
| `OP_CHECKSIG` | Vérifie une signature ECDSA |
| `OP_CHECKMULTISIG` | Vérifie N signatures sur M |
| `OP_RETURN` | Marque la sortie comme non dépensable (données) |
| `OP_CHECKSIGADD` | Taproot — vérification de signature aggregée |

---

## Résumé de la semaine

| Concept clé | Description |
|-------------|-------------|
| **P2PKH** | Le script classique — hash de clé publique + signature |
| **P2SH** | Hash de script — permet des conditions complexes sans les révéler |
| **P2WPKH** | P2PKH en SegWit — données witness séparées |
| **P2TR** | Taproot — Schnorr + MAST pour l'efficacité et la confidentialité |
| **SegWit** | Séparation des signatures → résout la malléabilité, augmente la capacité |
| **Taproot** | Schnorr + Merklized Abstract Script Trees → scripts plus efficaces |

---

## Liens de référence rapides

| Ressource | URL |
|-----------|-----|
| Developer Guides | https://developer.bitcoin.org/devguide/ |
| Reference | https://developer.bitcoin.org/reference/ |
| RPC API | https://developer.bitcoin.org/reference/rpc/ |
| P2P Messages | https://developer.bitcoin.org/reference/p2p_network.html |
| Glossary | https://developer.bitcoin.org/glossary.html |


<a href="#top">⬆ Retour au menu / Back to top</a>

