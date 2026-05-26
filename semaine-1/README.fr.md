> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Semaine 1 — Fondations

**Objectif :** Comprendre les briques de base : encodage, cryptographie, clés et adresses.
**Période :** 25 - 29 mai 2026

**Prérequis Rust :**
```toml
# Cargo.toml
[dependencies]
sha2 = "0.10"
hex = "0.4"
rand = "0.8"
```

---

## Lundi 25 mai — Encodage de base

### 1. Hexadecimal

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/hexadecimal/)

#### Théorie

Hexadecimal est un système de numération en **base 16** utilisant 16 symboles : `0-9` et `A-F` (valeurs 0 à 15). La casse ne compte pas — `1337af` = `1337AF`.

**Pourquoi Bitcoin l'utilise :** Un demi-octet (nibble) a 16 combinaisons (2⁴ = 16), ce qui correspond parfaitement à un caractère hex. Un octet complet = 2 caractères hex. Toutes les données Bitcoin (clés, hash, transactions) sont affichées en hex.

| Hex | Décimal | Binaire |
|-----|---------|---------|
| 0 | 0 | 0000 |
| 1 | 1 | 0001 |
| 2 | 2 | 0010 |
| 3 | 3 | 0011 |
| 4 | 4 | 0100 |
| 5 | 5 | 0101 |
| 6 | 6 | 0110 |
| 7 | 7 | 0111 |
| 8 | 8 | 1000 |
| 9 | 9 | 1001 |
| A | 10 | 1010 |
| B | 11 | 1011 |
| C | 12 | 1100 |
| D | 13 | 1101 |
| E | 14 | 1110 |
| F | 15 | 1111 |

**Préfixes :** `0x` = hex, `0d` = décimal, `0b` = binaire. Ex: `0x100` = 256.

#### Conversion hex → décimal

Multiplier chaque caractère par des puissances croissantes de 16 (de droite à gauche).

Exemple avec `02A13B` :
```
B(11) × 16⁰ =          11
3      × 16¹ =          48
1      × 16² =         256
A(10)  × 16³ =      40 960
2      × 16⁴ =     131 072
0      × 16⁵ =           0
                  --------
Total               172 347
```

#### Conversion décimal → hex

Diviser par 16, noter les restes de bas en haut.

Exemple avec 6735 :
```
6735 ÷ 16 = 420  reste 15 → F
 420 ÷ 16 =  26  reste  4 → 4
  26 ÷ 16 =   1  reste 10 → A
   1 ÷ 16 =   0  reste  1 → 1
                          → 1A4F
```

#### Code Rust

```rust
fn hex_to_decimal(hex: &str) -> u64 {
    let hex = hex.trim_start_matches("0x").to_uppercase();
    let mut result: u64 = 0;
    for c in hex.chars() {
        let digit = c.to_digit(16).expect("Invalid hex character");
        result = result * 16 + digit as u64;
    }
    result
}

fn decimal_to_hex(mut n: u64) -> String {
    if n == 0 { return "0".to_string(); }
    let mut hex = String::new();
    while n > 0 {
        let remainder = n % 16;
        let c = if remainder < 10 {
            (b'0' + remainder as u8) as char
        } else {
            (b'A' + (remainder - 10) as u8) as char
        };
        hex.push(c);
        n /= 16;
    }
    hex.chars().rev().collect()
}

fn main() {
    // Hex → Decimal
    println!("0xFF = {}", hex_to_decimal("FF"));       // 255
    println!("0x02A13B = {}", hex_to_decimal("02A13B")); // 172347

    // Decimal → Hex
    println!("255 = 0x{}", decimal_to_hex(255));       // FF
    println!("6735 = 0x{}", decimal_to_hex(6735));     // 1A4F

    // Avec la crate hex (pour les octets)
    let hex_str = "deadbeef";
    let bytes = hex::decode(hex_str).expect("Invalid hex");
    println!("{:?} → {:?}", hex_str, bytes);
    // "deadbeef" → [222, 173, 190, 239]

    let back = hex::encode(&bytes);
    println!("{:?} → {:?}", bytes, back);
    // [222, 173, 190, 239] → "deadbeef"
}
```

#### Exercice rapide
Écrivez un programme qui convertit `0xCAFEBABE` en décimal. Résultat attendu : 3405691582.

---

### 2. Bytes (Octets)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/bytes/)

#### Théorie

Un **bit** = une valeur binaire (0 ou 1). Un **octet (byte)** = 8 bits groupés. C'est l'unité de mesure par défaut des données Bitcoin.

- 2 caractères hex = 1 octet = 8 bits
- Une clé privée de 32 octets = 256 bits

**Représentation :** Un octet peut contenir différents types de données selon le contexte :

| Type | Exemple Bitcoin | Taille |
|------|----------------|--------|
| Nombre (entier) | Clé privée, montant, nonce | 4-32 octets |
| Texte (ASCII) | OP_RETURN, coinbase message | Variable |
| Champ de bits | Services du nœud, version du bloc | 4-8 octets |
| Identifiant unique | TXID, Block Hash, Public Key Hash | 32 octets |
| Données arbitraires | Magic bytes (`f9beb4d9`) | 4 octets |

**Important :** Le même octet peut signifier des choses différentes — `01100001` = la lettre "g" (ASCII) ou le nombre 103 (entier). Tout dépend du contexte.

#### Les bits en détail

```
Octet: 1 0 1 1 0 1 0 1
Bit:   7 6 5 4 3 2 1 0  (position, poids décroissant)

Valeur = 1×2⁷ + 0×2⁶ + 1×2⁵ + 1×2⁴ + 0×2³ + 1×2² + 0×2¹ + 1×2⁰
       = 128  +  0   +  32  +  16  +  0   +  4   +  0   +  1
       = 181
```

#### Code Rust

```rust
fn byte_to_bits(byte: u8) -> String {
    format!("{:08b}", byte)
}

fn bits_to_decimal(bits: &str) -> u8 {
    let mut result: u8 = 0;
    for (i, c) in bits.chars().rev().enumerate() {
        if c == '1' {
            result |= 1 << i;
        }
    }
    result
}

fn main() {
    // Un octet = 8 bits
    let byte: u8 = 0b10110101;
    println!("0b10110101 = {} (décimal) = 0x{:02X} (hex)", byte, byte);
    // 0b10110101 = 181 (décimal) = 0xB5 (hex)

    // Afficher les bits d'un octet
    println!("Bits de 181 : {}", byte_to_bits(181));
    // Bits de 181 : 10110101

    // ASCII : même octet, contexte différent
    let byte_ascii: u8 = 0b01100001; // 97
    println!("97 = '{}'", byte_ascii as char); // 97 = 'a'

    // Conversion entre types
    let n: u32 = 1000;
    let bytes = n.to_le_bytes(); // little-endian
    println!("1000 en LE : {:02x?}", bytes); // [e8, 03, 00, 00]

    let bytes_be = n.to_be_bytes(); // big-endian
    println!("1000 en BE : {:02x?}", bytes_be); // [00, 00, 03, e8]
}
```

#### Exercice rapide
Écrivez un programme qui affiche les bits de chaque octet de la chaîne `"Bitcoin"`.
Indice : `"Bitcoin".as_bytes()` retourne `&[u8]`.

---

### 3. Little Endian

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/little-endian/)

#### Théorie

Little-endian est un ordre d'octets où l'**octet de poids faible vient en premier** — les octets apparaissent "à l'envers" par rapport à la lecture humaine. C'est l'ordre standard pour presque tous les entiers dans les données Bitcoin brutes.

**Big-Endian** (lisible par l'humain) : `00 00 00 00 00 bc 61 4e`
**Little-Endian** (utilisé par Bitcoin) : `4e 61 bc 00 00 00 00 00`

**Origine du terme :** Jonathan Swift, *Les Voyages de Gulliver* (1726) — deux factions se disputent quel côté d'un œuf casser.

**Où on le trouve dans Bitcoin :**
- `version` d'une transaction (ex: `02000000`)
- `vout` d'un input (ex: `01000000` = output #1)
- `amount` d'un output (ex: `2a5f020000000000`)
- `time`, `bits`, `nonce` dans l'en-tête de bloc

**Pourquoi Bitcoin l'utilise :** Satoshi développait sur un processeur little-endian. La plupart des CPU modernes fonctionnent nativement en little-endian.

**Conversion :** Inverser l'ordre des octets (pas des caractères hex). `6fe2` inversé = `e26f`, pas `2ef6`.

#### Visualisation

```
Nombre : 305 419 896  (0x12345678)

Big-Endian (BE) :    12 34 56 78   ← lecture naturelle
Little-Endian (LE) : 78 56 34 12   ← ordre inversé
                     ↑
              octet de poids faible en premier
```

#### Code Rust

```rust
fn swap_endian(hex: &str) -> String {
    hex.as_bytes()
        .chunks(2)
        .rev()
        .map(|c| std::str::from_utf8(c).unwrap())
        .collect::<Vec<_>>()
        .join("")
}

fn main() {
    // Conversion LE ↔ BE
    let be = "0000000000bc614e";
    let le = swap_endian(be);
    println!("BE → LE : {} → {}", be, le);
    // BE → LE : 0000000000bc614e → 4e61bc0000000000

    // Utilisation native de Rust
    let n: u32 = 0x12345678;
    println!("Nombre   : 0x{:08X}", n);

    let be_bytes = n.to_be_bytes();
    let le_bytes = n.to_le_bytes();
    println!("BE bytes : {:02x?}", be_bytes); // [12, 34, 56, 78]
    println!("LE bytes : {:02x?}", le_bytes); // [78, 56, 34, 12]

    // Cas Bitcoin : le champ "version" d'une transaction
    let version_le: [u8; 4] = [0x02, 0x00, 0x00, 0x00];
    let version = u32::from_le_bytes(version_le);
    println!("Transaction version : {}", version); // 2

    // Cas Bitcoin : le champ "vout" d'un input
    let vout_le: [u8; 4] = [0x01, 0x00, 0x00, 0x00];
    let vout = u32::from_le_bytes(vout_le);
    println!("Vout : {}", vout); // 1 (output #1)

    // Cas Bitcoin : montant (en satoshis)
    let amount_le: [u8; 8] = [0x2a, 0x5f, 0x02, 0x00, 0x00, 0x00, 0x00, 0x00];
    let amount = u64::from_le_bytes(amount_le);
    println!("Amount : {} satoshis", amount); // 155434 satoshis
    println!("Amount : {} BTC", amount as f64 / 100_000_000.0); // 0.00155434 BTC
}
```

#### Exercice rapide
Écrivez un programme qui lit le champ `nonce` d'un en-tête de bloc en little-endian.
Le nonce brut (LE) est : `[0x3a, 0x1f, 0x01, 0x00]`. Quelle est la valeur décimale ?

---

## Résumé Lundi

| Concept | Point clé | Code Rust |
|---------|-----------|-----------|
| **Hex** | 2 car. hex = 1 octet, base 16 | `hex::decode()`, `hex::encode()` |
| **Bytes** | 8 bits, unité fondamentale | `u8`, `.to_le_bytes()`, `.to_be_bytes()` |
| **Little-Endian** | Octet faible en premier (Bitcoin) | `u32::from_le_bytes()`, `u64::from_le_bytes()` |

---

## Mardi 26 mai — Encodage avancé

### 4. Byte Order (ordre des octets pour les hash)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/byte-order/)

#### Théorie

Bitcoin utilise **deux ordres d'octets** distincts pour les hash de transactions (TXID) et les hash de blocs :

| Contexte | Ordre | Usage |
|----------|-------|-------|
| Données brutes (protocole) | Naturel (sortie du hash) | Références internes : previous block, TXID dans les inputs |
| RPC, explorateurs, affichage | Inversé (octets retournés) | Recherche et affichage des hash |

**Exemple — hash du genesis block :**
- Naturel : `6fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000`
- Inversé : `000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f`

**Pourquoi inverser ?** Satoshi interprétait les hash de blocs comme des **entiers** pour les comparer au target lors du minage. En format inversé, les zéros sont à gauche, ce qui rend la comparaison visuellement intuitive.

**Attention :** Les TXID et les hash de blocs sont inversés à l'affichage, mais les hash dans les données brutes ne le sont pas. Il faut inverser l'ordre des octets pour rechercher un hash sur un explorateur.

#### Code Rust

```rust
use sha2::{Sha256, Digest};

fn hash256(data: &[u8]) -> Vec<u8> {
    let hash1 = Sha256::digest(data);
    let hash2 = Sha256::digest(&hash1);
    hash2.to_vec()
}

fn reverse_hex(hex_str: &str) -> String {
    hex_str.as_bytes()
        .chunks(2)
        .rev()
        .map(|c| std::str::from_utf8(c).unwrap())
        .collect::<Vec<_>>()
        .join("")
}

fn main() {
    // Simuler un hash de bloc (genesis block hash)
    let genesis_hash_natural = "6fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000";
    let genesis_hash_display = reverse_hex(genesis_hash_natural);

    println!("Naturel  : {}", genesis_hash_natural);
    println!("Inversé  : {}", genesis_hash_display);
    // Inversé : 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f

    // Vérifier : le hash inversé commence par des zéros (preuve de travail)
    assert!(genesis_hash_display.starts_with("0000000000"));

    // Hasher des données et inverser pour l'affichage
    let data = b"Hello Bitcoin";
    let hash = hash256(data);
    let hash_hex = hex::encode(&hash);
    let hash_display = reverse_hex(&hash_hex);

    println!("\nDonnées  : {:?}", std::str::from_utf8(data).unwrap());
    println!("Hash     : {}", hash_hex);
    println!("Affiché  : {}", hash_display);
}
```

#### Cas pratique : chercher un TXID sur un explorateur

```rust
fn main() {
    // TXID dans les données brutes d'une transaction
    let txid_raw = "c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704";

    // Pour chercher sur un explorateur, inverser les octets
    let txid_display = reverse_hex(txid_raw);
    println!("TXID (explorateur) : {}", txid_display);
    // 0437cd7f8525ceed2324359c2d0ba26006d92d856a9c20fa0241106ee5a597c9
}
```

---

### 5. CompactSize (entiers de taille variable)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/compact-size/)

#### Théorie

Un champ CompactSize encode des entiers de taille variable. Le premier octet (préfixe) détermine la structure :

| Préfixe | Signification | Plage de valeurs | Taille totale |
|---------|---------------|------------------|---------------|
| `00`–`FC` | L'octet lui-même est la valeur | 0 – 252 | 1 octet |
| `FD` | 2 octets suivants (little-endian) | 253 – 65 535 | 3 octets |
| `FE` | 4 octets suivants (little-endian) | 65 536 – 4 294 967 295 | 5 octets |
| `FF` | 8 octets suivants (little-endian) | 4 294 967 296 – 2⁶⁴-1 | 9 octets |

**Exemples :**

| Décimal | CompactSize Hex |
|---------|-----------------|
| 0 | `00` |
| 100 | `64` |
| 252 | `fc` |
| 253 | `fdfd00` |
| 1 000 | `fde803` |
| 65 536 | `fe00000100` |

**Où on le trouve :**
- Nombre d'inputs/outputs dans une transaction
- Taille du ScriptSig / ScriptPubKey
- Nombre d'éléments dans le witness stack
- Nombre de transactions dans un bloc

**Avantage :** Économie d'espace — 1 octet au lieu de 2 fixed pour les petits nombres courants.

**Différence avec VarInt :** CompactSize est utilisé dans les messages réseau Bitcoin. VarInt est utilisé dans la base LevelDB du Chainstate. BIP 141 appelle CompactSize `var_int`, ce qui crée de la confusion.

#### Code Rust : Encoder et Décoder

```rust
fn encode_compact_size(value: u64) -> Vec<u8> {
    if value < 0xfd {
        vec![value as u8]
    } else if value <= 0xffff {
        let mut result = vec![0xfd];
        result.extend_from_slice(&(value as u16).to_le_bytes());
        result
    } else if value <= 0xffffffff {
        let mut result = vec![0xfe];
        result.extend_from_slice(&(value as u32).to_le_bytes());
        result
    } else {
        let mut result = vec![0xff];
        result.extend_from_slice(&value.to_le_bytes());
        result
    }
}

fn decode_compact_size(bytes: &[u8]) -> (u64, usize) {
    match bytes[0] {
        0x00..=0xfc => (bytes[0] as u64, 1),
        0xfd => {
            let value = u16::from_le_bytes([bytes[1], bytes[2]]) as u64;
            (value, 3)
        }
        0xfe => {
            let value = u32::from_le_bytes([bytes[1], bytes[2], bytes[3], bytes[4]]) as u64;
            (value, 5)
        }
        0xff => {
            let value = u64::from_le_bytes([
                bytes[1], bytes[2], bytes[3], bytes[4],
                bytes[5], bytes[6], bytes[7], bytes[8],
            ]);
            (value, 9)
        }
    }
}

fn main() {
    // Encoder
    let values = [0u64, 100, 252, 253, 1000, 65536];
    for &v in &values {
        let encoded = encode_compact_size(v);
        println!("{:>6} → {}", v, hex::encode(&encoded));
    }
    //      0 → 00
    //    100 → 64
    //    252 → fc
    //    253 → fdfd00
    //   1000 → fde803
    //  65536 → fe00000100

    // Décoder
    let test_bytes = hex::decode("fdfd00").unwrap();
    let (value, size) = decode_compact_size(&test_bytes);
    println!("\nfdfd00 → valeur={}, taille={} octets", value, size);
    // fdfd00 → valeur=253, taille=3 octets

    // Cas pratique : nombre d'inputs dans une transaction
    let tx_data: &[u8] = &[0x02]; // 2 inputs
    let (num_inputs, _) = decode_compact_size(tx_data);
    println!("\nNombre d'inputs : {}", num_inputs); // 2
}
```

---

### 6. Base58

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/base58/)

#### Théorie

Base58 est un système en **base 58** créé par Satoshi pour représenter les données Bitcoin de manière lisible.

**Jeu de caractères :**
```
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz
```
58 caractères : 10 chiffres + 25 majuscules + 23 minuscules. Les caractères `0`, `O`, `I`, `l` sont exclus car visuellement ambigus.

**Encodage :** Division modulaire répétée par 58.

**Zéros initiaux :** Chaque octet `0x00` en entrée est converti en `1` en sortie Base58 (pour préserver la longueur).

**Préfixes de version :**

| Préfixe Hex | Car. initial | Usage | Exemple |
|-------------|-------------|-------|---------|
| `00` | 1 | Adresse P2PKH | `1AKDDsfTh8uY4X3ppy1m7jw1fVMBSMkzjP` |
| `05` | 3 | Adresse P2SH | `34nSkinWC9rDDJiUY438qQN1JHmGqBHGW7` |
| `80` | K, L, ou 5 | Clé privée WIF | `L4mee2GrpBSckB9SgC9WhHxvtEgKUvgvTiy...` |
| `0488ADE4` | xprv | Clé étendue privée | `xprv9tuogRdb5YTgcL3P8Waj7REqDuQx4sXc...` |
| `0488B21E` | xpub | Clé étendue publique | `xpub67uA5wAUuv1ypp7rEY7jUZBZmwFSULFU...` |

**Base58Check** = données + checksum (4 premiers octets de SHA256(SHA256(données))) + encodage Base58. Le checksum permet de détecter les erreurs de saisie (1 chance sur 4,3 milliards de faux positifs).

#### Code Rust

```rust
const BASE58_ALPHABET: &[u8; 58] = b"123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz";

fn base58_encode(data: &[u8]) -> String {
    // Compter les zéros initiaux
    let leading_zeros = data.iter().take_while(|&&b| b == 0).count();

    // Conversion en base58 par division modulaire
    let mut num = num_bigint::BigUint::from_bytes_be(data);
    let base = num_bigint::BigUint::from(58u32);
    let mut result = Vec::new();

    while num > 0 {
        let remainder = (&num % &base).to_u32_digits().first().copied().unwrap_or(0) as usize;
        num /= &base;
        result.push(BASE58_ALPHABET[remainder]);
    }

    // Ajouter les '1' pour les zéros initiaux
    for _ in 0..leading_zeros {
        result.push(b'1');
    }

    result.reverse();
    String::from_utf8(result).unwrap()
}

fn base58_decode(s: &str) -> Vec<u8> {
    let leading_ones = s.bytes().take_while(|&b| b == b'1').count();

    let mut num = num_bigint::BigUint::from(0u32);
    let base = num_bigint::BigUint::from(58u32);

    for c in s.bytes() {
        let idx = BASE58_ALPHABET.iter().position(|&b| b == c).unwrap();
        num = num * &base + num_bigint::BigUint::from(idx);
    }

    let mut result = vec![0u8; leading_ones];
    result.extend_from_slice(&num.to_bytes_be());
    result
}

fn main() {
    // Encoder des données en Base58
    let data = hex::decode("000102030405").unwrap();
    let encoded = base58_encode(&data);
    println!("Base58 : {}", encoded); // 16Ho7Hs

    // Décoder
    let decoded = base58_decode("16Ho7Hs");
    println!("Hex    : {}", hex::encode(&decoded));

    // Zéros initiaux
    let data_with_zeros = hex::decode("000000abcdef").unwrap();
    let encoded = base58_encode(&data_with_zeros);
    println!("Avec zéros : {}", encoded); // 111Rmg...
    // Note : 3 zéros initiaux → 3 '1' en début
}
```

---

### 7. Bech32

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/bech32/)

#### Théorie

Bech32 est le format d'adresse pour les scripts SegWit, conçu par Pieter Wuille. Il utilise un jeu de **32 caractères minuscules** (insensible à la casse) avec un checksum amélioré.

**Structure :** `bc1q` + données encodées

| Type | Préfixe | Longueur | Encodage | Exemple |
|------|---------|----------|----------|---------|
| P2WPKH | `bc1q` | 42 car. | Bech32 | `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4` |
| P2WSH | `bc1q` | 62 car. | Bech32 | `bc1qrp33g0q5c5txsp9arysrx4k6zdkfs4nce4xj0gdcccefvpysxf3qccfmv3` |
| P2TR | `bc1p` | 62 car. | Bech32m | `bc1p5d7rjq7g6rdk2yhzksdssmvp9afg6hv6l2j235...` |

**Jeu de caractères Bech32 :**
```
qpzry9x8gf2tvdw0s3jn54khce6mua7l
```

**Avantages sur Base58 :**
- Insensible à la casse (tout en minuscules)
- Checksum amélioré (détection d'erreurs meilleure)
- Encodage du ScriptPubKey complet (pas seulement du hash)

**Différence Bech32 vs Bech32m :** Le calcul du checksum diffère. Bech32 est utilisé pour SegWit v0 (P2WPKH, P2WSH). Bech32m est utilisé pour SegWit v1+ (P2TR/Taproot). Le changement corrige une faiblesse : insérer/supprimer des `q` avant un `p` final n'était pas détecté.

#### Code Rust (utilisant la crate bech32)

```rust
use bech32::{self, FromBase32, ToBase32, Variant};

fn main() {
    // Décoder une adresse Bech32
    let addr = "bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4";
    let (hrp, data, variant) = bech32::decode(addr).expect("Invalid bech32");
    let payload = Vec::<u8>::from_base32(&data).expect("Invalid base32");

    println!("HRP      : {}", hrp);                    // bc
    println!("Variant  : {:?}", variant);              // Bech32
    println!("Payload  : {} octets", payload.len());    // 20 octets (P2WPKH)
    println!("Payload  : {}", hex::encode(&payload));

    // Encoder une adresse Bech32
    let witness_program = hex::decode("751e76e8199196d454941c45d1b3a323f1433bd6").unwrap();
    let encoded = bech32::encode("bc", witness_program.to_base32(), Variant::Bech32).unwrap();
    println!("\nAdresse : {}", encoded);
    // bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4

    // Identifier le type d'adresse
    let addresses = [
        "bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4",  // P2WPKH
        "bc1p5d7rjq7g6rdk2yhzksdssmvp9afg6hv6l2j235",   // P2TR
    ];
    for addr in &addresses {
        let (_, data, variant) = bech32::decode(addr).unwrap();
        let payload = Vec::<u8>::from_base32(&data).unwrap();
        let type_name = match (variant, payload.len()) {
            (Variant::Bech32, 20) => "P2WPKH",
            (Variant::Bech32, 32) => "P2WSH",
            (Variant::Bech32m, 32) => "P2TR",
            _ => "Inconnu",
        };
        println!("{} → {} ({} octets)", addr, type_name, payload.len());
    }
}
```

---

### 8. Checksum

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/checksum/)

#### Théorie

Un checksum est un petit morceau de données permettant de vérifier l'**intégrité** d'autres données. Il détecte les erreurs mais ne les corrige pas.

**Création :** Les 4 premiers octets de `SHA256(SHA256(données))`.

```
données     = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
hash256     = 05c4de7c1069e9de703efd172e58c1919f48ae03910277a49c9afd7ded58bbeb
checksum    = 05c4de7c
```

**Où on le trouve :**
- Adresses Base58 (P2PKH, P2SH)
- Clés WIF
- Clés étendues (xpub, xprv)
- Messages réseau (chaque message entre nœuds)

**Fiabilité :** 4 octets = 2³² = 4 294 967 296 valeurs possibles. Probabilité de faux positif ≈ 1 sur 4,3 milliards.

#### Code Rust

```rust
use sha2::{Sha256, Digest};

fn hash256(data: &[u8]) -> Vec<u8> {
    let hash1 = Sha256::digest(data);
    let hash2 = Sha256::digest(&hash1);
    hash2.to_vec()
}

fn checksum(data: &[u8]) -> [u8; 4] {
    let hash = hash256(data);
    [hash[0], hash[1], hash[2], hash[3]]
}

fn verify_checksum(data: &[u8], expected_checksum: &[u8; 4]) -> bool {
    checksum(data) == *expected_checksum
}

fn main() {
    // Calculer un checksum
    let data = hex::decode("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa").unwrap();
    let cs = checksum(&data);
    println!("Données : {}", hex::encode(&data));
    println!("Checksum : {}", hex::encode(&cs));
    // Checksum : 05c4de7c

    // Vérifier un checksum
    let data = hex::decode("deadbeef12345678").unwrap();
    let cs = checksum(&data);
    println!("\nDonnées : {}", hex::encode(&data));
    println!("Checksum calculé : {}", hex::encode(&cs));

    // Construire une adresse Base58Check complète
    let version: u8 = 0x00; // P2PKH mainnet
    let pubkey_hash = hex::decode("010966776006953d5567439e5e39f86a0d273bee").unwrap();

    let mut payload = vec![version];
    payload.extend_from_slice(&pubkey_hash);
    let cs = checksum(&payload);

    let mut full = payload.clone();
    full.extend_from_slice(&cs);

    println!("\nVersion + Hash : {}", hex::encode(&payload));
    println!("Checksum       : {}", hex::encode(&cs));
    println!("Complet        : {}", hex::encode(&full));
    // Base58Check de cela donne : 16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM

    // Vérification
    let received = &full[..full.len()-4];
    let received_cs: [u8; 4] = full[full.len()-4..].try_into().unwrap();
    println!("\nVérification : {}", verify_checksum(received, &received_cs)); // true
}
```

---

## Résumé Mardi

| Concept | Point clé | Code Rust |
|---------|-----------|-----------|
| **Byte Order** | Hash inversés pour affichage | `reverse_hex()`, `from_le_bytes()` |
| **CompactSize** | 1/3/5/9 octets selon la valeur | `encode_compact_size()`, `decode_compact_size()` |
| **Base58** | 58 car., checksum intégré | `base58_encode()`, `base58_decode()` |
| **Bech32** | SegWit, minuscules, Bech32m pour Taproot | `bech32::decode()`, `bech32::encode()` |
| **Checksum** | 4 premiers octets de HASH256 | `checksum()`, `verify_checksum()` |

---

## Mercredi 27 mai — Cryptographie

### 9. Hash Function

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/hash-function/)

#### Théorie

Une fonction de hash crée des **empreintes numériques** des données. Elle prend une entrée de taille quelconque et produit une sortie de taille fixe.

**Propriétés fondamentales :**

| Propriété | Description | Exemple |
|-----------|-------------|---------|
| **Déterministe** | Même entrée → même sortie | Toujours le même hash |
| **Sortie à taille fixe** | SHA-256 = toujours 32 octets | Peu importe la taille d'entrée |
| **Effet avalanche** | Un bit changé → hash complètement différent | `"abc"` vs `"abd"` → hashes totalement différents |
| **Unidirectionnel** | Impossible de retrouver l'entrée à partir du hash | Pas d'algorithme inverse connu |
| **Résistance aux collisions** | Impossible de trouver deux entrées avec le même hash | ~2¹²⁸ opérations pour SHA-256 |

**Fonctions utilisées dans Bitcoin :**

| Fonction | Formule | Usage | Taille de sortie |
|----------|---------|-------|------------------|
| **HASH256** | SHA256(SHA256(data)) | Blocs, TXID, Merkle root, checksums | 32 octets |
| **HASH160** | RIPEMD160(SHA256(data)) | Public key hash, adresses legacy | 20 octets |
| **SHA256** | SHA256(data) | P2WSH script hash (SegWit) | 32 octets |
| **HMAC-SHA512** | HMAC(SHA512, key, data) | Dérivation clés étendues HD | 64 octets |
| **PBKDF2** | 2048 itérations HMAC-SHA512 | Mnemonic → seed HD | 64 octets |

**Usage dans Bitcoin :**
- **Minage :** Hash du bloc < target → bloc valide
- **Intégrité blockchain :** Chaque bloc référence le hash du précédent
- **TXID :** Identifiant unique de transaction
- **Merkle Root :** Empreinte de toutes les TXID du bloc
- **Checksums :** 4 premiers octets d'un hash pour détecter les erreurs

**Erreur courante :** Hasher les codes ASCII d'une chaîne hex au lieu des octets réels. En Rust, il faut `hex::decode(hex)` avant de hasher.

#### Code Rust : HASH256 et HASH160

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};

fn hash256(data: &[u8]) -> Vec<u8> {
    let hash1 = Sha256::digest(data);
    let hash2 = Sha256::digest(&hash1);
    hash2.to_vec()
}

fn hash160(data: &[u8]) -> Vec<u8> {
    let sha = Sha256::digest(data);
    let ripemd = Ripemd160::digest(&sha);
    ripemd.to_vec()
}

fn main() {
    // HASH256 : double SHA-256
    let data = b"Hello Bitcoin";
    let h = hash256(data);
    println!("HASH256 : {}", hex::encode(&h));
    // 32 octets = 64 caractères hex

    // HASH160 : SHA-256 puis RIPEMD-160
    let h = hash160(data);
    println!("HASH160 : {}", hex::encode(&h));
    // 20 octets = 40 caractères hex

    // ERREUR COURANTE : hasher les codes ASCII au lieu des octets
    let hex_str = "ab";
    // FAUX : hasher les bytes du string "ab" (= [0x61, 0x62])
    let wrong = hash256(hex_str.as_bytes());
    println!("\nFAUX   : {}", hex::encode(&wrong));

    // CORRECT : décoder le hex puis hasher
    let correct = hash256(&hex::decode(hex_str).unwrap());
    println!("CORRECT : {}", hex::encode(&correct));
    // Résultats complètement différents !

    // Démontrer l'effet avalanche
    let a = hash256(b"abc");
    let b = hash256(b"abd");
    println!("\nabc → {}", hex::encode(&a));
    println!("abd → {}", hex::encode(&b));
    // Un seul bit changé → hash totalement différent
}
```

#### Rust : dépendances pour ripemd160

```toml
# Cargo.toml
[dependencies]
sha2 = "0.10"
hex = "0.4"
ripemd = "0.1"
```

---

### 10. Courbe Elliptique (secp256k1)

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/)

#### Théorie

Une courbe elliptique est un ensemble de points satisfaisant l'équation **y² = x³ + ax + b**. Elle fournit une **fonction à sens unique** : on peut avancer (multiplication) mais pas reculer (logarithme discret).

**Bitcoin utilise la courbe secp256k1 :**

| Paramètre | Valeur | Description |
|-----------|--------|-------------|
| **a** | 0 | Coefficient |
| **b** | 7 | Coefficient |
| **p** | 2²⁵⁶ - 2³² - 2⁹ - 2⁸ - 2⁷ - 2⁶ - 2⁴ - 1 | Module premier (champ fini) |
| **n** | 115792089237316195423570985008687907852837564279074904382605163141518161494337 | Ordre (nombre de points) |

**Point générateur (G) :**
```
x = 55066263022277343669578718895168534326250603453777594175500187360389116729240
y = 32670510020758816978083085130507043184471273380659243275938904335757337482424
```

**Champ fini :** Les opérations se font modulo p (un nombre premier), ce qui transforme la courbe lisse en un nuage de points discrets. Les mathématiques restent identiques.

#### Opérations fondamentales

**1. Addition de points :**
```
Soient P₁(x₁, y₁) et P₂(x₂, y₂) sur la courbe.

Si P₁ ≠ P₂ :
    s = (y₂ - y₁) × (x₂ - x₁)⁻¹ mod p    (pente)
    x₃ = s² - x₁ - x₂ mod p
    y₃ = s(x₁ - x₃) - y₁ mod p

Si P₁ = P₂ (doublement) :
    s = (3x₁² + a) × (2y₁)⁻¹ mod p
    x₃ = s² - 2x₁ mod p
    y₃ = s(x₁ - x₃) - y₁ mod p
```

**2. Multiplication scalaire (double-and-add) :**
```
k × P = P + P + ... + P (k fois)

Optimisation : double-and-add
Exemple : 21 × P
21 en binaire = 10101

Bit 1 : R = P
Bit 0 : R = 2R          (double)
Bit 1 : R = 2R + P      (double + add)
Bit 0 : R = 2R          (double)
Bit 1 : R = 2R + P      (double + add)

4 opérations au lieu de 20 additions
```

**3. Dérivation clé publique :**
```
Clé publique Q = k × G
où k = clé privée (nombre), G = point générateur

Sens inverse (retrouver k à partir de Q) :
→ Problème du logarithme discret sur courbes elliptiques (ECDLP)
→ Computationnellement infaisable (~2²⁵⁶ opérations)
```

#### Code Rust : opérations sur courbe elliptique (simplifié)

```rust
use num_bigint::BigUint;
use num_traits::{Zero, One};

// Paramètres secp256k1
struct Secp256k1 {
    p: BigUint,  // module premier
    a: BigUint,  // coefficient (0)
    b: BigUint,  // coefficient (7)
    n: BigUint,  // ordre
    g: (BigUint, BigUint), // point générateur
}

impl Secp256k1 {
    fn new() -> Self {
        Secp256k1 {
            p: BigUint::parse_bytes(b"FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F", 16).unwrap(),
            a: BigUint::zero(),
            b: BigUint::from(7u32),
            n: BigUint::parse_bytes(b"FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141", 16).unwrap(),
            g: (
                BigUint::parse_bytes(b"79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798", 16).unwrap(),
                BigUint::parse_bytes(b"483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8", 16).unwrap(),
            ),
        }
    }

    // Addition modulaire : a^(-1) mod p
    fn mod_inverse(a: &BigUint, p: &BigUint) -> BigUint {
        // Utilise le petit théorème de Fermat : a^(p-2) mod p
        a.modpow(&(p - BigUint::from(2u32)), p)
    }

    // Addition de deux points
    fn point_add(&self, p1: &(BigUint, BigUint), p2: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let (x1, y1) = p1;
        let (x2, y2) = p2;

        if x1 == x2 && y1 == y2 {
            return self.point_double(p1);
        }

        // Pente : s = (y2 - y1) * (x2 - x1)^(-1) mod p
        let dy = if y2 >= y1 { y2 - y1 } else { &self.p - (y1 - y2) };
        let dx = if x2 >= x1 { x2 - x1 } else { &self.p - (x1 - x2) };
        let dx_inv = Self::mod_inverse(&dx, &self.p);
        let s = (&dy * &dx_inv) % &self.p;

        // x3 = s² - x1 - x2 mod p
        let x3 = (&s * &s + &self.p - x1 + &self.p - x2) % &self.p;
        // y3 = s(x1 - x3) - y1 mod p
        let y3 = (&s * (if x1 >= &x3 { x1 - &x3 } else { &self.p - (&x3 - x1) }) + &self.p - y1) % &self.p;

        (x3, y3)
    }

    // Doublement d'un point
    fn point_double(&self, p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let (x1, y1) = p1;

        // Pente : s = (3x₁² + a) * (2y₁)^(-1) mod p
        let three_x2 = (BigUint::from(3u32) * x1 * x1) % &self.p;
        let two_y = (BigUint::from(2u32) * y1) % &self.p;
        let two_y_inv = Self::mod_inverse(&two_y, &self.p);
        let s = (&three_x2 * &two_y_inv) % &self.p;

        // x3 = s² - 2x1 mod p
        let x3 = (&s * &s + &self.p - (BigUint::from(2u32) * x1)) % &self.p;
        // y3 = s(x1 - x3) - y1 mod p
        let y3 = (&s * (if x1 >= &x3 { x1 - &x3 } else { &self.p - (&x3 - x1) }) + &self.p - y1) % &self.p;

        (x3, y3)
    }

    // Multiplication scalaire : k × P (double-and-add)
    fn scalar_mul(&self, k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let mut result = (BigUint::zero(), BigUint::zero()); // point à l'infini
        let mut addend = point.clone();
        let mut k = k.clone();

        while k > BigUint::zero() {
            if &k % 2u32 == BigUint::one() {
                result = if result.0.is_zero() && result.1.is_zero() {
                    addend.clone()
                } else {
                    self.point_add(&result, &addend)
                };
            }
            addend = self.point_double(&addend);
            k >>= 1;
        }
        result
    }
}

fn main() {
    let curve = Secp256k1::new();

    // Dérivation clé publique : Q = k × G
    let private_key = BigUint::from(7u32); // clé privée exemple
    let public_key = curve.scalar_mul(&private_key, &curve.g);

    println!("Clé privée : {}", private_key);
    println!("Clé publique :");
    println!("  x = {}", public_key.0);
    println!("  y = {}", public_key.1);

    // Vérifier que le point est sur la courbe : y² = x³ + 7 mod p
    let y2 = (&public_key.1 * &public_key.1) % &curve.p;
    let x3_7 = (&public_key.0 * &public_key.0 * &public_key.0 + &curve.b) % &curve.p;
    println!("\nSur la courbe ? {}", y2 == x3_7); // true

    // Démontrer la fonction à sens unique
    println!("\n--- Fonction à sens unique ---");
    println!("k → Q = k×G : FACILE (multiplication scalaire)");
    println!("Q → k       : INFAISABLE (logarithme discret)");
    println!("Avec ~2²⁵⁶ clés possibles, la force brute prendrait");
    println!("plus de temps que l'âge de l'univers.");
}
```

#### Dépendances Cargo.toml

```toml
[dependencies]
num-bigint = "0.4"
num-traits = "0.2"
```

---

## Résumé Mercredi

| Concept | Point clé | Code Rust |
|---------|-----------|-----------|
| **HASH256** | Double SHA-256 (blocs, TXID) | `Sha256::digest` deux fois |
| **HASH160** | SHA-256 puis RIPEMD-160 (adresses) | `Sha256` puis `Ripemd160` |
| **Effet avalanche** | 1 bit changé → hash totalement différent | Comparer `hash256(b"abc")` vs `hash256(b"abd")` |
| **secp256k1** | y² = x³ + 7, a=0, b=7, module premier p | Opérations modulaires sur les points |
| **Double-and-add** | O(log k) au lieu de O(k) | `scalar_mul()` avec bits de k |
| **ECDLP** | k×G facile, Q→k infaisable | La sécurité de Bitcoin repose sur ça |

---

## Jeudi 28 mai — Signatures numériques

### 11. ECDSA

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/ecdsa/)

#### Théorie

ECDSA (Elliptic Curve Digital Signature Algorithm) est le système de signature utilisé dans Bitcoin pour prouver la propriété des bitcoins.

**Génération de clés :**
- **Clé privée (d) :** un grand nombre aléatoire dans [0..n-1]
- **Clé publique (Q) :** `Q = d × G`

**Signature :** Nécessite 3 entrées :
1. **Nonce aléatoire (k) :** assure l'unicité de chaque signature
2. **Hash du message (z) :** SHA256 des données à signer
3. **Clé privée (d)**

La signature = **[r, s]** où :
- `r` = coordonnée x du point R (R = k × G), réduit mod n
- `s` = k⁻¹ × (z + r × d) mod n

**Sécurité du nonce :** Si le même nonce k est utilisé pour deux signatures différentes, la clé privée est récupérable :
```
k = (z₁ - z₂) × (s₁ - s₂)⁻¹ mod n
d = (k × s₁ - z₁) × r⁻¹ mod n
```
Exploité en 2011 contre la PS3 (Sony avait réutilisé k).

**Vérification :** Calculer deux points et les additionner :
- Point 1 : G × (s⁻¹ × z)
- Point 2 : Q × (s⁻¹ × r)
- Si le x du résultat mod n = r → signature valide

**Encodage DER :** Bitcoin exige les signatures en format DER :
```
30 [longueur] 02 [longueur_r] [r] 02 [longueur_s] [s] [sighash_type]
```
Typiquement 70-72 octets + 1 byte sighash (`01` pour SIGHASH_ALL).

**Règle Low-S (BIP 62) :** Si s > n/2, remplacer par n - s. Empêche la malléabilité des transactions.

#### Code Rust : signature et vérification ECDSA

```rust
use k256::ecdsa::{SigningKey, VerifyingKey, signature::{Signer, Verifier}};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Générer une paire de clés
    let signing_key = SigningKey::random(&mut OsRng);
    let verifying_key = VerifyingKey::from(&signing_key);

    println!("Clé privée : {}", hex::encode(signing_key.to_bytes()));
    println!("Clé publique : {}", hex::encode(verifying_key.to_encoded_point(false).as_bytes()));

    // Signer un message
    let message = b"Transaction: Alice -> Bob: 0.5 BTC";
    let signature: k256::ecdsa::Signature = signing_key.sign(message);

    println!("\nMessage : {:?}", std::str::from_utf8(message).unwrap());
    println!("Signature : {}", hex::encode(signature.to_bytes()));

    // Vérifier la signature
    let is_valid = verifying_key.verify(message, &signature).is_ok();
    println!("Signature valide ? {}", is_valid); // true

    // Vérifier avec un message modifié
    let tampered = b"Transaction: Alice -> Bob: 50 BTC";
    let is_valid = verifying_key.verify(tampered, &signature).is_ok();
    println!("Message modifié valide ? {}", is_valid); // false
}
```

#### Code Rust : détails de la signature ECDSA

```rust
use k256::ecdsa::{SigningKey, signature::Signer};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Créer une clé privée à partir d'octets
    let private_key_bytes = hex::decode(
        "0000000000000000000000000000000000000000000000000000000000000001"
    ).unwrap();
    let signing_key = SigningKey::from_bytes(&private_key_bytes.into()).unwrap();

    // Signer
    let msg = b"test";
    let sig: k256::ecdsa::Signature = signing_key.sign(msg);

    // Le format DER de la signature
    let der_bytes = sig.to_bytes();
    println!("Signature ({} octets) : {}", der_bytes.len(), hex::encode(&der_bytes));

    // Nonce déterministe (RFC 6979)
    // k est dérivé de la clé privée et du message → même message donne même k
    // Cela évite le problème de Sony (nonce réutilisé)
    println!("\nSécurité du nonce :");
    println!("- RFC 6979 : nonce déterministe basé sur (clé, message)");
    println!("- Même message → même nonce → même signature");
    println!("- Messages différents → nonces différents");
    println!("- Jamais de réutilisation de nonce");
}
```

#### Encodage DER en détail

```rust
fn parse_der_signature(der: &[u8]) -> Option<(&[u8], &[u8])> {
    if der.len() < 8 || der[0] != 0x30 {
        return None; // Pas une SEQUENCE
    }
    let _total_len = der[1] as usize;
    let mut pos = 2;

    // r
    if der[pos] != 0x02 { return None; }
    let r_len = der[pos + 1] as usize;
    let r = &der[pos+2..pos+2+r_len];
    pos += 2 + r_len;

    // s
    if der[pos] != 0x02 { return None; }
    let s_len = der[pos + 1] as usize;
    let s = &der[pos+2..pos+2+s_len];

    Some((r, s))
}

fn main() {
    // Exemple DER réel
    let der = hex::decode(
        "3044022047ac8e878352d3ebbde1c94ce3a10d057c24175747116f8288e5d794d12d482f0220217f36a485cae903c713331d877c1f64677e3622ad4010726870540656fe9dcb"
    ).unwrap();

    match parse_der_signature(&der) {
        Some((r, s)) => {
            println!("DER signature parsée :");
            println!("r = {} ({} octets)", hex::encode(r), r.len());
            println!("s = {} ({} octets)", hex::encode(s), s.len());
        }
        None => println!("Signature DER invalide"),
    }
}
```

#### Dépendances

```toml
[dependencies]
k256 = { version = "0.13", features = ["ecdsa"] }
hex = "0.4"
rand_core = "0.6"
```

---

### 12. Schnorr Signatures

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/schnorr/)

#### Théorie

Schnorr est un système de signature ajouté par le upgrade Taproot en 2021 (BIP 340). Même courbe secp256k1 qu'ECDSA, mais avec des avantages significatifs.

**Différences clés avec ECDSA :**

| Aspect | ECDSA | Schnorr |
|--------|-------|---------|
| Équation de signature | `s = k⁻¹(z + r·d)` (non-linéaire) | `s = (k + e·d)` (linéaire) |
| Taille de signature | 70-72 octets (DER) | 64 octets (fixe) |
| Clé publique | 33 octets (compressée) | 32 octets (x-only) |
| Agrégation de signatures | Non | Oui |
| Vérification par lot | Non | Oui |
| Preuve de sécurité | Absente | Prouvée (modèle oracle aléatoire) |
| Malléabilité | Oui (avant SegWit) | Non |

**Processus de signature (BIP 340) :**
1. Calculer P = d'·G
2. Négaliser d si P[y] est impair → d
3. Générer le nonce privé k' via tagged hash
4. Calculer R = k'·G, négaliser k si R[y] est impair
5. Calculer le challenge e = tagged_hash("BIP0340/challenge", R[x] || P[x] || m)
6. Signature = r(R[x]) || s((k + e·d) % n)

**Tagged hashes :** Tous les hashes utilisent un préfixe de tag (`BIP0340/aux`, `BIP0340/nonce`, `BIP0340/challenge`) pour éviter les collisions entre contextes.

```
tag_hash = SHA256(tag)
tagged_hash(data) = SHA256(tag_hash || tag_hash || data)
```
Le tag hash est doublé (64 octets = un bloc SHA-256) pour permettre la pré-calcul du premier round.

**Vérification par lot :** Plusieurs signatures peuvent être vérifiées simultanément, plus rapide que la vérification individuelle. Utilise des multiplicateurs aléatoires pour empêcher les signatures invalides de s'annuler.

**Lien avec Taproot :** La linéarité de Schnorr permet l'agrégation de clés (MuSig) et les key-path / script-path spending de Taproot.

#### Code Rust : signatures Schnorr (BIP 340)

```rust
use k256::schnorr::{SigningKey, VerifyingKey, signature::{Signer, Verifier}};
use k256::elliptic_curve::rand_core::OsRng;
use sha2::{Sha256, Digest};

fn tagged_hash(tag: &[u8], data: &[u8]) -> [u8; 32] {
    let tag_hash = Sha256::digest(tag);
    let mut hasher = Sha256::new();
    hasher.update(&tag_hash);
    hasher.update(&tag_hash);
    hasher.update(data);
    hasher.finalize().into()
}

fn main() {
    // Générer une paire de clés Schnorr
    let signing_key = SigningKey::random(&mut OsRng);
    let verifying_key = VerifyingKey::from(&signing_key);

    // Clé publique x-only (32 octets)
    let pubkey_bytes = verifying_key.to_bytes();
    println!("Clé publique x-only : {} ({} octets)",
        hex::encode(pubkey_bytes), pubkey_bytes.len());

    // Signer
    let message = b"Taproot transaction";
    let signature = signing_key.sign(message);

    println!("Signature : {} (64 octets fixe)",
        hex::encode(signature.to_bytes()));

    // Vérifier
    let is_valid = verifying_key.verify(message, &signature).is_ok();
    println!("Valide ? {}", is_valid); // true

    // Tagged hash (BIP 340)
    let challenge = tagged_hash(b"BIP0340/challenge", b"test data");
    println!("\nTagged hash (challenge) : {}", hex::encode(challenge));

    // Comparaison taille
    println!("\n--- Comparaison ---");
    println!("ECDSA (DER)  : ~70-72 octets + sighash byte");
    println!("Schnorr      : 64 octets fixes (r[32] || s[32])");
    println!("Économie     : ~10% d'espace, encodage fixe");
}
```

#### Dépendances

```toml
[dependencies]
k256 = { version = "0.13", features = ["schnorr"] }
sha2 = "0.10"
hex = "0.4"
```

---

## Résumé Jeudi

| Concept | Point clé | Code Rust |
|---------|-----------|-----------|
| **ECDSA** | `s = k⁻¹(z + r·d) mod n`, DER 70-72 octets | `k256::ecdsa`, `SigningKey`, `VerifyingKey` |
| **Nonce** | Jamais réutiliser → clé privée récupérable | RFC 6979 (déterministe) |
| **Low-S** | Si s > n/2 → n - s (BIP 62) | Géré automatiquement par `k256` |
| **Schnorr** | `s = (k + e·d) mod n`, 64 octets fixes | `k256::schnorr`, x-only keys |
| **Tagged hash** | `SHA256(tag_hash||tag_hash||data)` | `tagged_hash()` |
| **Taproot** | Linéarité → agrégation clés/signatures | MuSig, key-path / script-path |

---

## Vendredi 29 mai — Clés et adresses

### 13. Private Key

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/private-key/)

#### Théorie

Une clé privée est un très grand **nombre aléatoire** utilisé pour dériver une clé publique. Elle ne doit jamais être exposée publiquement.

**Génération :** 32 octets aléatoires (256 bits) depuis une source cryptographiquement sécurisée (`/dev/urandom` sur Linux).

**Plage valide :**
- Min : 1
- Max : n - 1 (où n = ordre de secp256k1)

**Sécurité :** Les fonctions `rand()` classiques ne sont PAS suffisantes. Utiliser des générateurs cryptographiquement sécurisés. Incident Milk Sad (2023) : la bibliothèque libbitcoin a utilisé une source de randomness faible → +900 000 $ de pertes.

**Espace des clés :** ~2²⁵⁶ ≈ 10⁷⁷ clés possibles. Pour comparaison, il y a ~10⁷⁸ atomes dans l'univers observable. Une collision accidentelle est effectivement impossible.

#### Code Rust : génération sécurisée

```rust
use k256::SecretKey;
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Génération sécurisée avec OsRng (source OS cryptographique)
    let secret_key = SecretKey::random(&mut OsRng);
    let key_bytes = secret_key.to_bytes();

    println!("Clé privée (32 octets) :");
    println!("Hex : {}", hex::encode(&key_bytes));
    println!("Taille : {} octets = {} bits", key_bytes.len(), key_bytes.len() * 8);

    // Vérifier que la clé est dans la plage valide [1, n-1]
    // k256 le fait automatiquement, mais vérifions manuellement
    let n = num_bigint::BigUint::parse_bytes(
        b"FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141", 16
    ).unwrap();
    let key_num = num_bigint::BigUint::from_bytes_be(&key_bytes);
    println!("\nClé < n ? {}", key_num < n);
    println!("Clé > 0 ? {}", key_num > num_bigint::BigUint::from(0u32));
}
```

#### Erreur courante : randomness faible

```rust
// NE PAS FAIRE CECI :
// let bad_key = rand::random::<[u8; 32]>(); // rand par défaut n'est pas crypto-sécurisé

// FAIRE CECI :
use k256::elliptic_curve::rand_core::OsRng;
let good_key = SecretKey::random(&mut OsRng); // OsRng utilise /dev/urandom
```

---

### 14. Public Key

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/public-key/)

#### Théorie

Une clé publique est un **point sur la courbe secp256k1** (coordonnées x, y). Elle est dérivée de la clé privée par multiplication de courbe elliptique : **Q = k × G**.

**Formats d'encodage :**

| Format | Taille | Préfixe | Contenu |
|--------|--------|---------|---------|
| Non compressée | 65 octets | `04` | x (32 octets) + y (32 octets) |
| Compressée | 33 octets | `02` (y pair) ou `03` (y impair) | x (32 octets) |
| x-only | 32 octets | Aucun | x (32 octets), y supposé pair |

**Propriétés :**
- **Fonction unidirectionnelle :** Private → Public (facile), Public → Private (infaisable)
- **Capacité de signature :** Prouver la possession de la clé privée sans la révéler

**Décompression :** Résoudre y² = x³ + 7 (mod p) via y = y_sq^((p+1)/4) mod p, puis choisir la bonne racine selon le préfixe.

#### Code Rust : formats de clé publique

```rust
use k256::{SecretKey, PublicKey, EncodedPoint};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    let secret_key = SecretKey::random(&mut OsRng);
    let public_key = secret_key.public_key();

    // Format non compressé (65 octets, préfixe 04)
    let uncompressed = public_key.to_encoded_point(false);
    println!("Non compressé ({} octets) : {}...",
        uncompressed.as_bytes().len(),
        hex::encode(&uncompressed.as_bytes()[..10]));

    // Format compressé (33 octets, préfixe 02 ou 03)
    let compressed = public_key.to_encoded_point(true);
    println!("Compressé ({} octets) : {}...",
        compressed.as_bytes().len(),
        hex::encode(&compressed.as_bytes()[..10]));

    // Identifier le préfixe
    let prefix = compressed.as_bytes()[0];
    let y_parity = if prefix == 0x02 { "pair" } else { "impair" };
    println!("Préfixe : 0x{:02x} (y {})", prefix, y_parity);

    // Format x-only (32 octets, utilisé par Schnorr/Taproot)
    let x_only = &compressed.as_bytes()[1..]; // enlever le préfixe
    println!("x-only ({} octets) : {}", x_only.len(), hex::encode(x_only));
}
```

---

### 15. Public Key Hash

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/public-key/hash/)

#### Théorie

Le public key hash est le résultat du hash d'une clé publique via **HASH160** : `RIPEMD-160(SHA-256(clé_publique))`.

- **Entrée :** clé publique (33 ou 65 octets)
- **Sortie :** 20 octets (160 bits)

**Usage :**
- **P2PKH :** Le ScriptPubKey contient le hash → `76a914<20-byte-hash>88ac`
- **P2WPKH :** Le ScriptPubKey contient le hash → `0014<20-byte-hash>`

**Pourquoi hasher ?** Satoshi : "To make Bitcoin Addresses short, they are a hash of the public key, not the public key itself." Le hash raccourcit les adresses et ajoute une couche de sécurité supplémentaire (la clé publique n'est révélée qu'au moment de dépenser).

#### Code Rust

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};
use k256::{SecretKey, PublicKey};
use k256::elliptic_curve::rand_core::OsRng;

fn hash160(data: &[u8]) -> [u8; 20] {
    let sha = Sha256::digest(data);
    let ripemd = Ripemd160::digest(&sha);
    ripemd.into()
}

fn main() {
    let secret_key = SecretKey::random(&mut OsRng);
    let public_key = secret_key.public_key();

    // Clé publique compressée (33 octets)
    let compressed = public_key.to_encoded_point(true);
    let pubkey_bytes = compressed.as_bytes();

    println!("Clé publique compressée :");
    println!("  {} ({} octets)", hex::encode(pubkey_bytes), pubkey_bytes.len());

    // HASH160 = RIPEMD160(SHA256(pubkey))
    let pkh = hash160(pubkey_bytes);
    println!("\nPublic Key Hash :");
    println!("  {} (20 octets)", hex::encode(pkh));

    // ScriptPubKey P2PKH
    let mut script = vec![0x76, 0xa9, 0x14]; // OP_DUP OP_HASH160 PUSH20
    script.extend_from_slice(&pkh);
    script.extend_from_slice(&[0x88, 0xac]); // OP_EQUALVERIFY OP_CHECKSIG
    println!("\nScriptPubKey P2PKH :");
    println!("  {}", hex::encode(&script));

    // ScriptPubKey P2WPKH (SegWit)
    let mut script_wit = vec![0x00, 0x14]; // OP_0 PUSH20
    script_wit.extend_from_slice(&pkh);
    println!("\nScriptPubKey P2WPKH :");
    println!("  {}", hex::encode(&script_wit));
}
```

---

### 16. Address

**Source :** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/address/)

#### Théorie

Une adresse Bitcoin est un **encodage lisible** d'un public key hash ou script hash. Elle n'existe pas sur la blockchain — c'est une convention externe pour que les wallets sachent quel ScriptPubKey construire.

**Types d'adresses :**

| Type | Préfixe | Longueur | Encodage | Contenu | Script |
|------|---------|----------|----------|---------|--------|
| P2PKH | `1` | 34 car. | Base58Check | 20-byte pubkey hash | OP_DUP OP_HASH160 <hash> OP_EQUALVERIFY OP_CHECKSIG |
| P2SH | `3` | 34 car. | Base58Check | 20-byte script hash | OP_HASH160 <hash> OP_EQUAL |
| P2WPKH | `bc1q` | 42 car. | Bech32 | 20-byte pubkey hash | OP_0 <hash> |
| P2WSH | `bc1q` | 62 car. | Bech32 | 32-byte script hash | OP_0 <hash> |
| P2TR | `bc1p` | 62 car. | Bech32m | 32-byte tweaked pubkey | OP_1 <clé> |

**Progression historique :**
- 2009+ : Base58Check (P2PKH `1...`, P2SH `3...`)
- 2016+ : Bech32 (P2WPKH `bc1q...42`, P2WSH `bc1q...62`) — SegWit
- 2021+ : Bech32m (P2TR `bc1p...62`) — Taproot

**Avantages des adresses SegWit :** Le champ witness consomme moins d'espace bloc → frais de transaction plus bas.

#### Code Rust : créer une adresse P2PKH complète

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};
use k256::{SecretKey, PublicKey};
use k256::elliptic_curve::rand_core::OsRng;

const BASE58_ALPHABET: &[u8; 58] = b"123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz";

fn hash160(data: &[u8]) -> [u8; 20] {
    let sha = Sha256::digest(data);
    let ripemd = Ripemd160::digest(&sha);
    ripemd.into()
}

fn hash256(data: &[u8]) -> [u8; 32] {
    let h1 = Sha256::digest(data);
    let h2 = Sha256::digest(&h1);
    h2.into()
}

fn base58_encode(data: &[u8]) -> String {
    let leading_zeros = data.iter().take_while(|&&b| b == 0).count();
    let mut num = num_bigint::BigUint::from_bytes_be(data);
    let base = num_bigint::BigUint::from(58u32);
    let mut result = Vec::new();
    while num > 0 {
        let remainder = (&num % &base).to_u32_digits().first().copied().unwrap_or(0) as usize;
        num /= &base;
        result.push(BASE58_ALPHABET[remainder]);
    }
    for _ in 0..leading_zeros {
        result.push(b'1');
    }
    result.reverse();
    String::from_utf8(result).unwrap()
}

fn pubkey_to_address_p2pkh(pubkey: &[u8]) -> String {
    // 1. HASH160 de la clé publique
    let pkh = hash160(pubkey);

    // 2. Préfixer avec le byte de version (0x00 pour P2PKH mainnet)
    let mut payload = vec![0x00];
    payload.extend_from_slice(&pkh);

    // 3. Calculer le checksum (4 premiers octets de HASH256)
    let checksum = hash256(&payload);
    payload.extend_from_slice(&checksum[..4]);

    // 4. Encoder en Base58
    base58_encode(&payload)
}

fn pubkey_to_address_bech32(pubkey: &[u8]) -> String {
    let pkh = hash160(pubkey);
    bech32::encode("bc", pkh.to_base32(), bech32::Variant::Bech32).unwrap()
}

use bech32::ToBase32;

fn main() {
    let secret_key = SecretKey::random(&mut OsRng);
    let public_key = secret_key.public_key();
    let compressed = public_key.to_encoded_point(true);

    println!("Clé privée : {}", hex::encode(secret_key.to_bytes()));
    println!("Clé publique : {}", hex::encode(compressed.as_bytes()));

    // Adresse P2PKH (legacy)
    let addr_p2pkh = pubkey_to_address_p2pkh(compressed.as_bytes());
    println!("\nP2PKH : {}", addr_p2pkh);
    // Commence par '1'

    // Adresse P2WPKH (SegWit)
    let addr_bech32 = pubkey_to_address_bech32(compressed.as_bytes());
    println!("P2WPKH : {}", addr_bech32);
    // Commence par 'bc1q'

    // Résumé de la chaîne de dérivation
    println!("\n--- Chaîne de dérivation ---");
    println!("Clé privée (32 octets)");
    println!("  → k × G (multiplication courbe elliptique)");
    println!("Clé publique (33 octets, compressée)");
    println!("  → HASH160 (SHA256 puis RIPEMD160)");
    println!("Public Key Hash (20 octets)");
    println!("  → Base58Check / Bech32");
    println!("Adresse (34 car. P2PKH / 42 car. P2WPKH)");
}
```

---

## Résumé Vendredi

| Concept | Point clé | Code Rust |
|---------|-----------|-----------|
| **Private Key** | 32 octets aléatoires, plage [1, n-1] | `SecretKey::random(&mut OsRng)` |
| **Public Key** | Point sur courbe, 3 formats (65/33/32 octets) | `.to_encoded_point(true/false)` |
| **Public Key Hash** | HASH160 = RIPEMD160(SHA256(pubkey)), 20 octets | `hash160()` |
| **P2PKH** | `1...`, Base58Check, version byte `00` | `base58_encode(version+hash+checksum)` |
| **P2WPKH** | `bc1q...`, Bech32, 42 caractères | `bech32::encode("bc", ...)` |

---

## Résumé de la semaine

| Concept clé | Description | Code Rust |
|-------------|-------------|-----------|
| **Hex** | Base de tous les affichages dans Bitcoin (2 car. hex = 1 octet) | `hex::decode()`, `hex::encode()` |
| **Endian** | Little-endian pour les entiers dans le protocole ; hash inversés à l'affichage | `u32::from_le_bytes()` |
| **CompactSize** | Entiers de taille variable (1/3/5/9 octets selon la valeur) | `encode_compact_size()`, `decode_compact_size()` |
| **Base58** | Encodage lisible avec checksum (adresses legacy, WIF, clés étendues) | `base58_encode()`, `base58_decode()` |
| **Bech32** | Encodage SegWit, insensible à la casse, checksum amélioré | `bech32::decode()`, `bech32::encode()` |
| **SHA256** | Hash utilisé partout (blocs, transactions, clés) | `Sha256::digest()` |
| **secp256k1** | Courbe elliptique spécifique à Bitcoin (y² = x³ + 7) | Opérations modulaires sur les points |
| **ECDSA** | Signatures classiques (DER, 70-72 octets) | `k256::ecdsa` |
| **Schnorr** | Signatures Taproot (64 octets, agrégation, batch verification) | `k256::schnorr` |
| **Private → Public → Hash → Address** | La chaîne de dérivation fondamentale | Toutes les fonctions combinées |

---

## Prochaine semaine

[Semaine 2 — Wallets HD + Transactions](../semaine-2/README.md) : WIF, signatures, HD wallets (BIP32/BIP39), inputs, outputs, UTXO.


<a href="#top">⬆ Retour au menu / Back to top</a>
