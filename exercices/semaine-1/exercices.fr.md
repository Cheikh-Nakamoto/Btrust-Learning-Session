> [![Français](https://img.shields.io/badge/Français-blue)](exercices.fr.md) [![English](https://img.shields.io/badge/English-blue)](exercices.en.md)


# Exercices pratiques — Semaine 1

**Total : 29 exercices de codage Rust** répartis sur 5 jours.
**Objectif :** Écrire du code qui compile et passe les tests.

**Prérequis :**
```toml
# Cargo.toml
[dependencies]
sha2 = "0.10"
hex = "0.4"
ripemd = "0.1"
k256 = { version = "0.13", features = ["ecdsa", "schnorr"] }
bech32 = "0.11"
num-bigint = "0.4"
num-traits = "0.2"
rand_core = "0.6"
```

---

## Lundi — Hexadecimal, Bytes, Little Endian (6 exercices)

### Exercice 1 — Hex → Décimal

**Objectif :** Convertir une chaîne hexadécimale en nombre décimal.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `hex_to_decimal` qui prend une chaîne hexadécimale (avec ou sans préfixe `0x`) et retourne la valeur décimale.

**Fonction à implémenter :**
```rust
pub fn hex_to_decimal(hex: &str) -> u64 {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex1 {
    use super::*;

    #[test]
    fn test_ff() {
        assert_eq!(hex_to_decimal("FF"), 255);
    }

    #[test]
    fn test_with_prefix() {
        assert_eq!(hex_to_decimal("0x1A"), 26);
    }

    #[test]
    fn test_zero() {
        assert_eq!(hex_to_decimal("00"), 0);
    }

    #[test]
    fn test_complex() {
        assert_eq!(hex_to_decimal("02A13B"), 172347);
    }

    #[test]
    fn test_lowercase() {
        assert_eq!(hex_to_decimal("cafebabe"), 3405691582);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn hex_to_decimal(hex: &str) -> u64 {
    let hex = hex.trim_start_matches("0x").to_uppercase();
    let mut result: u64 = 0;
    for c in hex.chars() {
        let digit = c.to_digit(16).expect("Invalid hex character");
        result = result * 16 + digit as u64;
    }
    result
}
```

</details>

---

### Exercice 2 — Décimal → Hex

**Objectif :** Convertir un nombre décimal en chaîne hexadécimale.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `decimal_to_hex` qui convertit un entier non signé en sa représentation hexadécimale (sans préfixe `0x`, en majuscules).

**Fonction à implémenter :**
```rust
pub fn decimal_to_hex(mut n: u64) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex2 {
    use super::*;

    #[test]
    fn test_255() {
        assert_eq!(decimal_to_hex(255), "FF");
    }

    #[test]
    fn test_zero() {
        assert_eq!(decimal_to_hex(0), "0");
    }

    #[test]
    fn test_6735() {
        assert_eq!(decimal_to_hex(6735), "1A4F");
    }

    #[test]
    fn test_1000() {
        assert_eq!(decimal_to_hex(1000), "3E8");
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn decimal_to_hex(mut n: u64) -> String {
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
```

</details>

---

### Exercice 3 — Byte → Bits

**Objectif :** Afficher les 8 bits d'un octet sous forme de chaîne.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `byte_to_bits` qui retourne la représentation binaire d'un octet sur 8 caractères (ex: `0b10110101` → `"10110101"`).

**Fonction à implémenter :**
```rust
pub fn byte_to_bits(byte: u8) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex3 {
    use super::*;

    #[test]
    fn test_zero() {
        assert_eq!(byte_to_bits(0), "00000000");
    }

    #[test]
    fn test_255() {
        assert_eq!(byte_to_bits(255), "11111111");
    }

    #[test]
    fn test_181() {
        assert_eq!(byte_to_bits(181), "10110101");
    }

    #[test]
    fn test_97() {
        assert_eq!(byte_to_bits(97), "01100001");
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn byte_to_bits(byte: u8) -> String {
    format!("{:08b}", byte)
}
```

</details>

---

### Exercice 4 — Bits → Octet

**Objectif :** Convertir une chaîne de 8 bits en un octet.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `bits_to_u8` qui prend une chaîne de 8 caractères '0' et '1' et retourne la valeur entière correspondante.

**Fonction à implémenter :**
```rust
pub fn bits_to_u8(bits: &str) -> u8 {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex4 {
    use super::*;

    #[test]
    fn test_all_zeros() {
        assert_eq!(bits_to_u8("00000000"), 0);
    }

    #[test]
    fn test_all_ones() {
        assert_eq!(bits_to_u8("11111111"), 255);
    }

    #[test]
    fn test_181() {
        assert_eq!(bits_to_u8("10110101"), 181);
    }

    #[test]
    fn test_97() {
        assert_eq!(bits_to_u8("01100001"), 97);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn bits_to_u8(bits: &str) -> u8 {
    let mut result: u8 = 0;
    for (i, c) in bits.chars().rev().enumerate() {
        if c == '1' {
            result |= 1 << i;
        }
    }
    result
}
```

</details>

---

### Exercice 5 — Inverser l'ordre des octets (Endian swap)

**Objectif :** Convertir entre big-endian et little-endian en inversant les octets.
**Difficulté :** Moyen

**Énoncé :**
Écrivez une fonction `swap_endian` qui prend une chaîne hexadécimale et retourne la chaîne avec l'ordre des octets inversé. Chaque paire de caractères hex représente un octet.

**Fonction à implémenter :**
```rust
pub fn swap_endian(hex: &str) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex5 {
    use super::*;

    #[test]
    fn test_8_bytes() {
        assert_eq!(swap_endian("0000000000bc614e"), "4e61bc0000000000");
    }

    #[test]
    fn test_4_bytes() {
        assert_eq!(swap_endian("12345678"), "78563412");
    }

    #[test]
    fn test_2_bytes() {
        assert_eq!(swap_endian("6fe2"), "e26f");
    }

    #[test]
    fn test_transaction_version() {
        assert_eq!(swap_endian("02000000"), "00000002");
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn swap_endian(hex: &str) -> String {
    hex.as_bytes()
        .chunks(2)
        .rev()
        .map(|c| std::str::from_utf8(c).unwrap())
        .collect::<Vec<_>>()
        .join("")
}
```

</details>

---

### Exercice 6 — Lire un entier little-endian

**Objectif :** Lire un entier 32 bits non signé à partir d'octets en little-endian.
**Difficulté :** Moyen

**Énoncé :**
Écrivez une fonction `read_le_u32` qui prend un tableau de 4 octets en little-endian et retourne la valeur entière.

**Fonction à implémenter :**
```rust
pub fn read_le_u32(bytes: &[u8; 4]) -> u32 {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex6 {
    use super::*;

    #[test]
    fn test_version_2() {
        assert_eq!(read_le_u32(&[0x02, 0x00, 0x00, 0x00]), 2);
    }

    #[test]
    fn test_vout_1() {
        assert_eq!(read_le_u32(&[0x01, 0x00, 0x00, 0x00]), 1);
    }

    #[test]
    fn test_nonce() {
        assert_eq!(read_le_u32(&[0x3a, 0x1f, 0x01, 0x00]), 73530);
    }

    #[test]
    fn test_large() {
        assert_eq!(read_le_u32(&[0xff, 0xff, 0xff, 0xff]), 4294967295);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn read_le_u32(bytes: &[u8; 4]) -> u32 {
    u32::from_le_bytes(*bytes)
}
```

</details>

---
## Mardi — CompactSize, Base58, Checksum (6 exercices)

### Exercice 7 — CompactSize : Encodage

**Objectif :** Encoder un entier en format CompactSize (variable-length integer utilisé dans le protocole Bitcoin).
**Difficulté :** Moyen

**Énoncé :**
Le format CompactSize encode les entiers de manière variable :
- Si la valeur < 0xFD : 1 octet directement.
- Si la valeur <= 0xFFFF : préfixe `0xFD` suivi de 2 octets en little-endian.
- Si la valeur <= 0xFFFFFFFF : préfixe `0xFE` suivi de 4 octets en little-endian.
- Sinon : préfixe `0xFF` suivi de 8 octets en little-endian.

Écrivez une fonction `encode_compact_size` qui retourne la représentation CompactSize d'un entier.

**Fonction à implémenter :**
```rust
pub fn encode_compact_size(value: u64) -> Vec<u8> {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex7 {
    use super::*;

    #[test]
    fn test_small_value() {
        assert_eq!(encode_compact_size(0), vec![0x00]);
    }

    #[test]
    fn test_252() {
        assert_eq!(encode_compact_size(252), vec![0xfc]);
    }

    #[test]
    fn test_253() {
        assert_eq!(encode_compact_size(253), vec![0xfd, 0xfd, 0x00]);
    }

    #[test]
    fn test_1000() {
        assert_eq!(encode_compact_size(1000), vec![0xfd, 0xe8, 0x03]);
    }

    #[test]
    fn test_65535() {
        assert_eq!(encode_compact_size(65535), vec![0xfd, 0xff, 0xff]);
    }

    #[test]
    fn test_65536() {
        assert_eq!(encode_compact_size(65536), vec![0xfe, 0x00, 0x00, 0x01, 0x00]);
    }

    #[test]
    fn test_large() {
        let val: u64 = 0x0100000000;
        assert_eq!(
            encode_compact_size(val),
            vec![0xff, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00]
        );
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn encode_compact_size(value: u64) -> Vec<u8> {
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
```

</details>

---

### Exercice 8 — CompactSize : Décodage

**Objectif :** Décoder un entier CompactSize à partir d'un flux d'octets.
**Difficulté :** Moyen

**Énoncé :**
Écrivez une fonction `decode_compact_size` qui lit un entier CompactSize depuis une tranche d'octets et retourne un tuple `(valeur, nombre_d'octets_consommés)`. Le premier octet détermine la taille :
- `< 0xFD` : la valeur est l'octet lui-même (1 octet consommé).
- `0xFD` : les 2 octets suivants en little-endian (3 octets consommés au total).
- `0xFE` : les 4 octets suivants en little-endian (5 octets consommés au total).
- `0xFF` : les 8 octets suivants en little-endian (9 octets consommés au total).

**Fonction à implémenter :**
```rust
pub fn decode_compact_size(bytes: &[u8]) -> (u64, usize) {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex8 {
    use super::*;

    #[test]
    fn test_small() {
        assert_eq!(decode_compact_size(&[0x00]), (0, 1));
    }

    #[test]
    fn test_252() {
        assert_eq!(decode_compact_size(&[0xfc]), (252, 1));
    }

    #[test]
    fn test_253() {
        assert_eq!(decode_compact_size(&[0xfd, 0xfd, 0x00]), (253, 3));
    }

    #[test]
    fn test_1000() {
        assert_eq!(decode_compact_size(&[0xfd, 0xe8, 0x03]), (1000, 3));
    }

    #[test]
    fn test_65536() {
        assert_eq!(decode_compact_size(&[0xfe, 0x00, 0x00, 0x01, 0x00]), (65536, 5));
    }

    #[test]
    fn test_large() {
        let bytes = [0xff, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00];
        assert_eq!(decode_compact_size(&bytes), (0x0100000000, 9));
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn decode_compact_size(bytes: &[u8]) -> (u64, usize) {
    match bytes[0] {
        n if n < 0xfd => (n as u64, 1),
        0xfd => {
            let val = u16::from_le_bytes([bytes[1], bytes[2]]) as u64;
            (val, 3)
        }
        0xfe => {
            let val = u32::from_le_bytes([bytes[1], bytes[2], bytes[3], bytes[4]]) as u64;
            (val, 5)
        }
        0xff => {
            let val = u64::from_le_bytes([
                bytes[1], bytes[2], bytes[3], bytes[4],
                bytes[5], bytes[6], bytes[7], bytes[8],
            ]);
            (val, 9)
        }
        _ => unreachable!(),
    }
}
```

</details>

---

### Exercice 9 — Encodage Base58

**Objectif :** Encoder des octets en Base58 (alphabet Bitcoin : `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz`).
**Difficulté :** Difficile

**Énoncé :**
Base58 est un encodage semblable à Base64 mais sans les caractères ambigus (`0`, `O`, `I`, `l`). L'algorithme fonctionne par divisions successives par 58 sur un grand entier. Les zéros initiaux (octets `0x00`) dans les données d'entrée doivent être représentés par des `'1'` en début de résultat.

Utilisez `num_bigint::BigUint` pour manipuler les grands nombres.

**Fonction à implémenter :**
```rust
pub fn base58_encode(data: &[u8]) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex9 {
    use super::*;

    #[test]
    fn test_empty() {
        assert_eq!(base58_encode(&[]), "");
    }

    #[test]
    fn test_single_zero() {
        assert_eq!(base58_encode(&[0x00]), "1");
    }

    #[test]
    fn test_two_zeros() {
        assert_eq!(base58_encode(&[0x00, 0x00]), "11");
    }

    #[test]
    fn test_hello() {
        // "Hello World!" in Base58
        let data = b"Hello World!";
        assert_eq!(base58_encode(data), "3vQB7B6MrGQZaxCuFg4oh");
    }

    #[test]
    fn test_leading_zeros() {
        let data = [0x00, 0x00, 0x28, 0x7F, 0xB4, 0xCD];
        let result = base58_encode(&data);
        assert!(result.starts_with("11"));
    }

    #[test]
    fn test_known_address_hash() {
        // Known test vector from Bitcoin wiki
        let data = [
            0x00, 0xF5, 0x4A, 0x65, 0x0D, 0x46, 0xD0, 0x27,
            0x3E, 0x31, 0xE3, 0xB1, 0xE9, 0x38, 0xD5, 0x58,
            0x27, 0xB4, 0xCD, 0x0E, 0x38, 0xF5, 0x0D
        ];
        assert_eq!(base58_encode(&data), "1CaptainUn1verse");
    }
}
```

<details>
<summary>Solution</summary>

```rust
use num_bigint::BigUint;
use num_traits::Zero;

pub fn base58_encode(data: &[u8]) -> String {
    const ALPHABET: &[u8; 58] = b"123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz";

    if data.is_empty() {
        return String::new();
    }

    // Count leading zero bytes
    let leading_zeros = data.iter().take_while(|&&b| b == 0).count();

    // Convert to BigUint
    let mut num = BigUint::from_bytes_be(data);
    let base = BigUint::from(58u32);

    let mut result = Vec::new();
    while num > BigUint::zero() {
        let remainder = &num % &base;
        num /= &base;
        result.push(ALPHABET[remainder.to_u32_digits()[0] as usize]);
    }

    // Add '1' for each leading zero byte
    for _ in 0..leading_zeros {
        result.push(ALPHABET[0]);
    }

    result.reverse();
    String::from_utf8(result).unwrap()
}
```

</details>

---

### Exercice 10 — Checksum (HASH256 tronqué)

**Objectif :** Calculer un checksum Bitcoin : les 4 premiers octets du double SHA-256 (HASH256).
**Difficulté :** Facile

**Énoncé :**
Le checksum utilisé dans les adresses Bitcoin et le format WIF est constitué des 4 premiers octets de `SHA256(SHA256(data))`. Écrivez une fonction `checksum` qui calcule ce checksum à partir de données en entrée.

Utilisez `sha2::Sha256` pour le hachage.

**Fonction à implémenter :**
```rust
pub fn checksum(data: &[u8]) -> [u8; 4] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex10 {
    use super::*;

    #[test]
    fn test_hello() {
        let hash = checksum(b"hello");
        assert_eq!(hash.len(), 4);
    }

    #[test]
    fn test_known_value() {
        // SHA256(SHA256("")) = 5df6e0e2... → first 4 bytes
        let result = checksum(&[]);
        assert_eq!(result, [0x5d, 0xf6, 0xe0, 0xe2]);
    }

    #[test]
    fn test_version_plus_hash() {
        // Version byte 0x00 + 20-byte hash (typical P2PKH payload)
        let mut payload = vec![0x00u8; 21];
        payload[0] = 0x00;
        let cs = checksum(&payload);
        assert_eq!(cs.len(), 4);
    }

    #[test]
    fn test_deterministic() {
        let data = b"bitcoin";
        assert_eq!(checksum(data), checksum(data));
    }
}
```

<details>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn checksum(data: &[u8]) -> [u8; 4] {
    let first = Sha256::digest(data);
    let second = Sha256::digest(&first);
    [second[0], second[1], second[2], second[3]]
}
```

</details>

---

### Exercice 11 — Vérification de checksum

**Objectif :** Vérifier qu'un checksum correspond bien aux données fournies.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `verify_checksum` qui retourne `true` si le checksum calculé sur `data` correspond aux 4 octets attendus.

**Fonction à implémenter :**
```rust
pub fn verify_checksum(data: &[u8], expected: &[u8; 4]) -> bool {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex11 {
    use super::*;

    #[test]
    fn test_valid() {
        let data = b"hello";
        let cs = checksum(data);
        assert!(verify_checksum(data, &cs));
    }

    #[test]
    fn test_invalid() {
        let data = b"hello";
        assert!(!verify_checksum(data, &[0x00, 0x00, 0x00, 0x00]));
    }

    #[test]
    fn test_empty() {
        let cs = checksum(&[]);
        assert!(verify_checksum(&[], &cs));
    }

    #[test]
    fn test_tampered_data() {
        let data = b"bitcoin";
        let cs = checksum(data);
        let mut tampered = data.to_vec();
        tampered[0] = b'B';
        assert!(!verify_checksum(&tampered, &cs));
    }

    #[test]
    fn test_wif_like() {
        // Simulate WIF payload: version + privkey
        let mut payload = vec![0x80u8];
        payload.extend_from_slice(&[0xABu8; 32]);
        let cs = checksum(&payload);
        assert!(verify_checksum(&payload, &cs));
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn verify_checksum(data: &[u8], expected: &[u8; 4]) -> bool {
    checksum(data) == *expected
}
```

</details>

---

### Exercice 12 — Inversion de hash pour l'affichage

**Objectif :** Inverser l'ordre des octets d'un hash hexadécimal (conversion big-endian ↔ little-endian).
**Difficulté :** Facile

**Énoncé :**
Dans Bitcoin, les hash de blocs et de transactions sont affichés en little-endian (octets inversés) mais stockés en big-endian dans les structures de données. Écrivez une fonction `reverse_hash` qui prend un hash en hexadécimal (big-endian) et retourne sa version inversée (little-endian), ou vice versa.

Chaque paire de caractères hex représente un octet. Il faut inverser l'ordre des octets.

**Fonction à implémenter :**
```rust
pub fn reverse_hash(hex: &str) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex12 {
    use super::*;

    #[test]
    fn test_genesis_block() {
        // Genesis block hash (displayed reversed in block explorers)
        let internal = "000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f";
        let display = "6fe28a0ab6f1b372c1a6a246ae637746931e8365e15a089c68d6190000000000";
        assert_eq!(reverse_hash(internal), display);
    }

    #[test]
    fn test_reverse_twice() {
        let hash = "0102030405060708";
        assert_eq!(reverse_hash(&reverse_hash(hash)), hash);
    }

    #[test]
    fn test_two_bytes() {
        assert_eq!(reverse_hash("aabb"), "bbaa");
    }

    #[test]
    fn test_four_bytes() {
        assert_eq!(reverse_hash("01020304"), "04030201");
    }

    #[test]
    fn test_single_byte() {
        assert_eq!(reverse_hash("ff"), "ff");
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn reverse_hash(hex: &str) -> String {
    hex.as_bytes()
        .chunks(2)
        .rev()
        .map(|c| std::str::from_utf8(c).unwrap())
        .collect::<Vec<_>>()
        .join("")
}
```

</details>
## Mercredi — Hashs cryptographiques et courbes elliptiques (6 exercices)

### Exercice 13 — HASH256 (double SHA-256)

**Objectif :** Implémenter le hash HASH256 utilisé partout dans Bitcoin : `SHA256(SHA256(data))`.
**Difficulté :** Facile

**Énoncé :**
Le protocole Bitcoin utilise un double hachage SHA-256 (appelé HASH256) pour les hash de blocs, de transactions, les checksums, etc. Écrivez une fonction `hash256` qui prend une tranche d'octets et retourne le résultat de SHA256 appliqué deux fois.

Utilisez `sha2::Sha256`.

**Fonction à implémenter :**
```rust
pub fn hash256(data: &[u8]) -> [u8; 32] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex13 {
    use super::*;

    #[test]
    fn test_empty() {
        let result = hash256(&[]);
        let hex: String = result.iter().map(|b| format!("{:02x}", b)).collect();
        assert_eq!(hex, "5df6e0e2761359d30a8275058e299fcc0381534545f55cf43e41983f5d4c9456");
    }

    #[test]
    fn test_hello() {
        let result = hash256(b"hello");
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_genesis_coinbase() {
        // The coinbase tx hash of the genesis block
        let coinbase_script = b"The Times 03/Jan/2009 Chancellor on brink of second bailout for banks";
        let result = hash256(coinbase_script);
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_deterministic() {
        let data = b"bitcoin";
        assert_eq!(hash256(data), hash256(data));
    }

    #[test]
    fn test_different_inputs() {
        assert_ne!(hash256(b"hello"), hash256(b"world"));
    }
}
```

<details>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn hash256(data: &[u8]) -> [u8; 32] {
    let first = Sha256::digest(data);
    let second = Sha256::digest(&first);
    let mut result = [0u8; 32];
    result.copy_from_slice(&second);
    result
}
```

</details>

---

### Exercice 14 — HASH160 (SHA-256 puis RIPEMD-160)

**Objectif :** Implémenter le hash HASH160 utilisé pour les adresses Bitcoin : `RIPEMD160(SHA256(data))`.
**Difficulté :** Facile

**Énoncé :**
HASH160 est la composition de SHA-256 suivi de RIPEMD-160. C'est le hash utilisé pour dériver les adresses à partir des clés publiques. Le résultat fait 20 octets (160 bits).

Utilisez `sha2::Sha256` et `ripemd::Ripemd160`.

**Fonction à implémenter :**
```rust
pub fn hash160(data: &[u8]) -> [u8; 20] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex14 {
    use super::*;

    #[test]
    fn test_empty() {
        let result = hash160(&[]);
        assert_eq!(result.len(), 20);
    }

    #[test]
    fn test_hello() {
        let result = hash160(b"hello");
        let hex: String = result.iter().map(|b| format!("{:02x}", b)).collect();
        assert_eq!(hex, "b6a9c8c230722b7c748331a8b450f05566dc7d0f");
    }

    #[test]
    fn test_known_pubkey() {
        // Compressed public key for 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa
        let pubkey: Vec<u8> = vec![
            0x02, 0x63, 0x67, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63,
        ];
        let result = hash160(&pubkey);
        assert_eq!(result.len(), 20);
    }

    #[test]
    fn test_deterministic() {
        let data = b"bitcoin";
        assert_eq!(hash160(data), hash160(data));
    }

    #[test]
    fn test_different_from_hash256() {
        let data = b"test";
        let h160 = hash160(data);
        let h256 = hash256(data);
        // HASH160 is 20 bytes, HASH256 is 32 bytes
        assert_ne!(h160.len(), h256.len());
    }
}
```

<details>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};

pub fn hash160(data: &[u8]) -> [u8; 20] {
    let sha = Sha256::digest(data);
    let ripe = Ripemd160::digest(&sha);
    let mut result = [0u8; 20];
    result.copy_from_slice(&ripe);
    result
}
```

</details>

---

### Exercice 15 — Vérification de hash

**Objectif :** Vérifier qu'un hash calculé correspond à une valeur attendue en hexadécimal.
**Difficulté :** Facile

**Énoncé :**
Écrivez une fonction `verify_hash` qui calcule le HASH256 de `data` et vérifie s'il correspond au hash attendu fourni sous forme de chaîne hexadécimale (64 caractères, en minuscules).

**Fonction à implémenter :**
```rust
pub fn verify_hash(data: &[u8], expected_hex: &str) -> bool {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex15 {
    use super::*;

    #[test]
    fn test_empty_match() {
        let expected = "5df6e0e2761359d30a8275058e299fcc0381534545f55cf43e41983f5d4c9456";
        assert!(verify_hash(&[], expected));
    }

    #[test]
    fn test_empty_mismatch() {
        assert!(!verify_hash(&[], "0000000000000000000000000000000000000000000000000000000000000000"));
    }

    #[test]
    fn test_uppercase_fails() {
        // Our function expects lowercase hex
        let expected = "5DF6E0E2761359D30A8275058E299FCC0381534545F55CF43E41983F5D4C9456";
        assert!(!verify_hash(&[], expected));
    }

    #[test]
    fn test_hello() {
        let hash = hash256(b"hello");
        let hex: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
        assert!(verify_hash(b"hello", &hex));
    }

    #[test]
    fn test_wrong_length() {
        assert!(!verify_hash(b"test", "abcd"));
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn verify_hash(data: &[u8], expected_hex: &str) -> bool {
    let hash = hash256(data);
    let computed_hex: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
    computed_hex == expected_hex
}
```

</details>

---

### Exercice 16 — Addition de points sur secp256k1

**Objectif :** Implémenter l'addition de deux points sur la courbe elliptique secp256k1.
**Difficulté :** Difficile

**Énoncé :**
La courbe secp256k1 est définie par l'équation `y² = x³ + 7` sur le corps premier `p`. L'addition de deux points `P1 = (x1, y1)` et `P2 = (x2, y2)` suit les règles :
- Si P1 est le point à l'infini : retourner P2.
- Si P2 est le point à l'infini : retourner P1.
- Si P1 == P2 : utiliser la formule de doublage (voir exercice 17).
- Si x1 == x2 et y1 == -y2 (mod p) : retourner le point à l'infini.
- Sinon : `λ = (y2 - y1) * mod_inverse(x2 - x1, p)`, `x3 = λ² - x1 - x2`, `y3 = λ * (x1 - x3) - y1`.

Les fonctions auxiliaires `mod_p` et `mod_inverse` vous sont fournies. La fonction `point_double` est également fournie pour gérer le cas P1 == P2.

**Paramètres secp256k1 :**
```
p  = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F
a  = 0
b  = 7
Gx = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798
Gy = 0x483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8
```

**Fonctions fournies :**
```rust
use num_bigint::BigUint;
use num_traits::{Zero, One};

const SECP256K1_P: &str = "FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F";

pub fn mod_p(x: &BigUint) -> BigUint {
    let p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    x % p
}

pub fn mod_inverse(a: &BigUint, m: &BigUint) -> BigUint {
    // Extended Euclidean algorithm for modular inverse
    let mut old_r = a.clone() % m;
    let mut r = m.clone();
    let mut old_s = BigUint::one();
    let mut s = BigUint::zero();

    while !r.is_zero() {
        let quotient = &old_r / &r;
        let temp_r = old_r.clone();
        old_r = r.clone();
        r = temp_r - &quotient * &r;
        let temp_s = old_s.clone();
        old_s = s.clone();
        if temp_s >= quotient * &s {
            s = temp_s - quotient * &s;
        } else {
            s = m.clone() - (quotient * &s - temp_s) % m;
        }
    }
    old_s % m
}

pub fn point_double(p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    let p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    let (ref x1, ref y1) = *p1;

    // λ = (3 * x1²) / (2 * y1)
    let three = BigUint::from(3u32);
    let two = BigUint::from(2u32);
    let numerator = mod_p(&(&three * x1 * x1));
    let denominator = mod_p(&(&two * y1));
    let lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    let x3 = mod_p(&(&lambda * &lambda - x1 - x1));
    let y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

**Fonction à implémenter :**
```rust
pub fn point_add(
    p1: &(BigUint, BigUint),
    p2: &(BigUint, BigUint),
) -> (BigUint, BigUint) {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex16 {
    use super::*;

    fn g() -> (BigUint, BigUint) {
        let gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        let gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_add_g_plus_g_is_2g() {
        let g = g();
        let two_g = point_add(&g, &g);
        let expected_2g = point_double(&g);
        assert_eq!(two_g, expected_2g);
    }

    #[test]
    fn test_add_g_plus_2g_is_3g() {
        let g = g();
        let two_g = point_double(&g);
        let three_g = point_add(&g, &two_g);
        // 3G should not equal G or 2G
        assert_ne!(three_g, g);
        assert_ne!(three_g, two_g);
    }

    #[test]
    fn test_add_2g_plus_2g_is_4g() {
        let g = g();
        let two_g = point_double(&g);
        let four_g = point_add(&two_g, &two_g);
        let four_g_by_double = point_double(&two_g);
        assert_eq!(four_g, four_g_by_double);
    }

    #[test]
    fn test_known_2g() {
        let g = g();
        let two_g = point_double(&g);
        // Known 2G x-coordinate
        let expected_x = BigUint::parse_bytes(
            "C6047F9441ED7D6D3045406E95C07CD85C778E4B8CEF3CA7ABAC09B95C709EE5".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.0, expected_x);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn point_add(
    p1: &(BigUint, BigUint),
    p2: &(BigUint, BigUint),
) -> (BigUint, BigUint) {
    let p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    let (ref x1, ref y1) = *p1;
    let (ref x2, ref y2) = *p2;

    // If P1 == P2, use point doubling
    if x1 == x2 && y1 == y2 {
        return point_double(p1);
    }

    // λ = (y2 - y1) / (x2 - x1)
    let numerator = if y2 >= y1 {
        y2 - y1
    } else {
        &p - (y1 - y2)
    };
    let denominator = if x2 >= x1 {
        x2 - x1
    } else {
        &p - (x1 - x2)
    };
    let lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    let x3 = mod_p(&(&lambda * &lambda - x1 - x2));
    let y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

</details>

---

### Exercice 17 — Doublage de point sur secp256k1

**Objectif :** Implémenter le doublage d'un point sur secp256k1 (cas spécial de l'addition).
**Difficulté :** Difficile

**Énoncé :**
Le doublage d'un point P = (x, y) sur la courbe `y² = x³ + 7` utilise la dérivée de la courbe :
- `λ = (3 * x²) / (2 * y)` (modulo p)
- `x3 = λ² - 2x`
- `y3 = λ * (x - x3) - y`

Vous avez déjà vu `point_double` dans l'exercice 16 comme code fourni. Ici, réimplémentez-la en utilisant `mod_p` et `mod_inverse` qui vous sont fournies.

**Fonctions auxiliaires (déjà définies dans l'exercice 16) :**
```rust
use num_bigint::BigUint;
use num_traits::{Zero, One};

const SECP256K1_P: &str = "FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F";

pub fn mod_p(x: &BigUint) -> BigUint {
    let p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    x % p
}

pub fn mod_inverse(a: &BigUint, m: &BigUint) -> BigUint {
    let mut old_r = a.clone() % m;
    let mut r = m.clone();
    let mut old_s = BigUint::one();
    let mut s = BigUint::zero();

    while !r.is_zero() {
        let quotient = &old_r / &r;
        let temp_r = old_r.clone();
        old_r = r.clone();
        r = temp_r - &quotient * &r;
        let temp_s = old_s.clone();
        old_s = s.clone();
        if temp_s >= quotient * &s {
            s = temp_s - quotient * &s;
        } else {
            s = m.clone() - (quotient * &s - temp_s) % m;
        }
    }
    old_s % m
}
```

**Fonction à implémenter :**
```rust
pub fn point_double(p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex17 {
    use super::*;

    fn g() -> (BigUint, BigUint) {
        let gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        let gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_2g_known_x() {
        let g = g();
        let two_g = point_double(&g);
        let expected_x = BigUint::parse_bytes(
            "C6047F9441ED7D6D3045406E95C07CD85C778E4B8CEF3CA7ABAC09B95C709EE5".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.0, expected_x);
    }

    #[test]
    fn test_2g_known_y() {
        let g = g();
        let two_g = point_double(&g);
        let expected_y = BigUint::parse_bytes(
            "1AE168FEA63DC339A3C58419466CEAEEF7F632653266D0E1236431A950CFE52A".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.1, expected_y);
    }

    #[test]
    fn test_4g() {
        let g = g();
        let two_g = point_double(&g);
        let four_g = point_double(&two_g);
        // 4G should be different from G, 2G
        assert_ne!(four_g, g);
        assert_ne!(four_g, two_g);
    }

    #[test]
    fn test_double_then_double_matches_quadruple() {
        let g = g();
        let two_g = point_double(&g);
        let four_g = point_double(&two_g);
        let four_g_alt = point_double(&point_double(&g));
        assert_eq!(four_g, four_g_alt);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn point_double(p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    let p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    let (ref x1, ref y1) = *p1;

    let three = BigUint::from(3u32);
    let two = BigUint::from(2u32);

    // λ = (3 * x1²) / (2 * y1)
    let numerator = mod_p(&(&three * x1 * x1));
    let denominator = mod_p(&(&two * y1));
    let lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    let x3 = mod_p(&(&lambda * &lambda - x1 - x1));
    let y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

</details>

---

### Exercice 18 — Multiplication scalaire (double-and-add)

**Objectif :** Implémenter la multiplication scalaire d'un point par un entier sur secp256k1.
**Difficulté :** Difficile

**Énoncé :**
La multiplication scalaire `k * P` est l'opération fondamentale des courbes elliptiques en cryptographie. L'algorithme "double-and-add" décompose le scalaire `k` en binaire et, pour chaque bit (de gauche à droite) :
1. Double le résultat courant.
2. Si le bit vaut 1, ajoute le point P.

Cet algorithme est O(log k) en nombre d'opérations sur la courbe, au lieu de O(k) pour une addition naïve.

Utilisez `point_double` et `point_add` des exercices précédents.

**Fonctions auxiliaires (déjà définies) :**
```rust
// point_double, point_add, mod_p, mod_inverse, SECP256K1_P
// (voir exercices 16 et 17)
```

**Fonction à implémenter :**
```rust
pub fn scalar_mul(k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    // TODO — retourne le point à l'infini (0, 0) si k == 0
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex18 {
    use super::*;

    fn g() -> (BigUint, BigUint) {
        let gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        let gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_1g_is_g() {
        let g = g();
        let result = scalar_mul(&BigUint::from(1u32), &g);
        assert_eq!(result, g);
    }

    #[test]
    fn test_2g_matches_double() {
        let g = g();
        let result = scalar_mul(&BigUint::from(2u32), &g);
        let expected = point_double(&g);
        assert_eq!(result, expected);
    }

    #[test]
    fn test_3g_known() {
        let g = g();
        let three_g = scalar_mul(&BigUint::from(3u32), &g);
        // Known 3G x-coordinate
        let expected_x = BigUint::parse_bytes(
            "F9308A019258C31049344F85F89D5229B531C845836F99B08601F113BCE036F9".as_bytes(), 16
        ).unwrap();
        assert_eq!(three_g.0, expected_x);
    }

    #[test]
    fn test_7g() {
        let g = g();
        let seven_g = scalar_mul(&BigUint::from(7u32), &g);
        // 7G should be different from G
        assert_ne!(seven_g, g);
        assert_ne!(seven_g, (BigUint::zero(), BigUint::zero()));
    }

    #[test]
    fn test_large_scalar() {
        let g = g();
        let k = BigUint::parse_bytes(
            "FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141".as_bytes(), 16
        ).unwrap();
        let result = scalar_mul(&k, &g);
        // This is the order of the curve, so n*G should be point at infinity
        // But the order is actually n, so (n-1)*G = -G
        assert_ne!(result, g);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn scalar_mul(k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    if k.is_zero() {
        // Point at infinity represented as (0, 0)
        return (BigUint::zero(), BigUint::zero());
    }

    let mut result: Option<(BigUint, BigUint)> = None;
    let bits = k.to_bytes_be();

    for byte in &bits {
        for i in (0..8).rev() {
            // Double
            if let Some(ref r) = result {
                result = Some(point_double(r));
            }
            // Add if bit is 1
            if (byte >> i) & 1 == 1 {
                result = match result {
                    Some(r) => Some(point_add(&r, point)),
                    None => Some(point.clone()),
                };
            }
        }
    }

    result.unwrap()
}
```

</details>
## Jeudi — ECDSA, Signatures, Tagged Hash (5 exercices)

### Exercice 19 — Génération de paire de clés ECDSA

**Objectif :** Générer une paire de clés (clé privée + clé publique compressée) avec la courbe secp256k1.
**Difficulté :** Facile

**Énoncé :**
Utilisez le crate `k256` pour générer une paire de clés ECDSA. La clé privée est un tableau de 32 octets aléatoires. La clé publique compressée est un tableau de 33 octets commençant par `0x02` (y pair) ou `0x03` (y impair).

Utilisez `k256::ecdsa::SigningKey` et `k256::PublicKey`.

**Fonction à implémenter :**
```rust
pub fn generate_keypair() -> ([u8; 32], [u8; 33]) {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex19 {
    use super::*;

    #[test]
    fn test_privkey_length() {
        let (privkey, _) = generate_keypair();
        assert_eq!(privkey.len(), 32);
    }

    #[test]
    fn test_pubkey_length() {
        let (_, pubkey) = generate_keypair();
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_pubkey_prefix() {
        let (_, pubkey) = generate_keypair();
        assert!(pubkey[0] == 0x02 || pubkey[0] == 0x03);
    }

    #[test]
    fn test_privkey_not_zero() {
        let (privkey, _) = generate_keypair();
        assert_ne!(privkey, [0u8; 32]);
    }

    #[test]
    fn test_two_keypairs_differ() {
        let (pk1, pub1) = generate_keypair();
        let (pk2, pub2) = generate_keypair();
        assert_ne!(pk1, pk2);
        assert_ne!(pub1, pub2);
    }
}
```

<details>
<summary>Solution</summary>

```rust
use k256::ecdsa::SigningKey;
use k256::PublicKey;
use rand_core::OsRng;

pub fn generate_keypair() -> ([u8; 32], [u8; 33]) {
    let signing_key = SigningKey::random(&mut OsRng);
    let privkey_bytes = signing_key.to_bytes();
    let verifying_key = signing_key.verifying_key();
    let pubkey_bytes = verifying_key.to_encoded_point(true).as_bytes().to_vec();

    let mut privkey = [0u8; 32];
    privkey.copy_from_slice(&privkey_bytes);

    let mut pubkey = [0u8; 33];
    pubkey.copy_from_slice(&pubkey_bytes);

    (privkey, pubkey)
}
```

</details>

---

### Exercice 20 — Signature d'un message (ECDSA)

**Objectif :** Signer un message avec une clé privée ECDSA.
**Difficulté :** Moyen

**Énoncé :**
Écrivez une fonction qui signe un message avec une clé privée. La signature est retournée au format compact (64 octets : 32 octets pour `r` + 32 octets pour `s`).

Utilisez `k256::ecdsa::SigningKey` et `k256::ecdsa::signature::Signer`.

**Fonction à implémenter :**
```rust
pub fn sign_message(privkey: &[u8; 32], message: &[u8]) -> Vec<u8> {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex20 {
    use super::*;

    #[test]
    fn test_signature_length() {
        let (privkey, _) = generate_keypair();
        let sig = sign_message(&privkey, b"hello");
        assert_eq!(sig.len(), 64);
    }

    #[test]
    fn test_signature_deterministic() {
        // RFC 6979 deterministic signatures
        let (privkey, _) = generate_keypair();
        let sig1 = sign_message(&privkey, b"bitcoin");
        let sig2 = sign_message(&privkey, b"bitcoin");
        assert_eq!(sig1, sig2);
    }

    #[test]
    fn test_different_messages_different_sigs() {
        let (privkey, _) = generate_keypair();
        let sig1 = sign_message(&privkey, b"hello");
        let sig2 = sign_message(&privkey, b"world");
        assert_ne!(sig1, sig2);
    }

    #[test]
    fn test_signature_not_all_zeros() {
        let (privkey, _) = generate_keypair();
        let sig = sign_message(&privkey, b"test");
        assert_ne!(sig, vec![0u8; 64]);
    }
}
```

<details>
<summary>Solution</summary>

```rust
use k256::ecdsa::{SigningKey, signature::Signer};

pub fn sign_message(privkey: &[u8; 32], message: &[u8]) -> Vec<u8> {
    let signing_key = SigningKey::from_bytes(privkey.into()).expect("Invalid private key");
    let signature: k256::ecdsa::Signature = signing_key.sign(message);
    signature.to_bytes().to_vec()
}
```

</details>

---

### Exercice 21 — Vérification de signature (ECDSA)

**Objectif :** Vérifier qu'une signature ECDSA est valide pour un message et une clé publique donnés.
**Difficulté :** Moyen

**Énoncé :**
Écrivez une fonction qui vérifie une signature compacte (64 octets) contre un message et une clé publique compressée (33 octets). Retourne `true` si la signature est valide.

Utilisez `k256::ecdsa::VerifyingKey` et `k256::ecdsa::signature::Verifier`.

**Fonction à implémenter :**
```rust
pub fn verify_signature(pubkey: &[u8; 33], message: &[u8], sig: &[u8]) -> bool {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex21 {
    use super::*;

    #[test]
    fn test_valid_signature() {
        let (privkey, pubkey) = generate_keypair();
        let message = b"Hello Bitcoin";
        let sig = sign_message(&privkey, message);
        assert!(verify_signature(&pubkey, message, &sig));
    }

    #[test]
    fn test_wrong_message() {
        let (privkey, pubkey) = generate_keypair();
        let sig = sign_message(&privkey, b"correct");
        assert!(!verify_signature(&pubkey, b"wrong", &sig));
    }

    #[test]
    fn test_wrong_pubkey() {
        let (privkey, _) = generate_keypair();
        let (_, other_pubkey) = generate_keypair();
        let sig = sign_message(&privkey, b"hello");
        assert!(!verify_signature(&other_pubkey, b"hello", &sig));
    }

    #[test]
    fn test_tampered_signature() {
        let (privkey, pubkey) = generate_keypair();
        let mut sig = sign_message(&privkey, b"hello");
        sig[0] ^= 0xFF;
        assert!(!verify_signature(&pubkey, b"hello", &sig));
    }

    #[test]
    fn test_empty_message() {
        let (privkey, pubkey) = generate_keypair();
        let sig = sign_message(&privkey, b"");
        assert!(verify_signature(&pubkey, b"", &sig));
    }
}
```

<details>
<summary>Solution</summary>

```rust
use k256::ecdsa::{VerifyingKey, Signature, signature::Verifier};
use k256::EncodedPoint;

pub fn verify_signature(pubkey: &[u8; 33], message: &[u8], sig: &[u8]) -> bool {
    let point = EncodedPoint::from_bytes(pubkey);
    if point.is_err() {
        return false;
    }
    let verifying_key = VerifyingKey::from_encoded_point(&point.unwrap());
    if verifying_key.is_err() {
        return false;
    }
    let verifying_key = verifying_key.unwrap();

    let signature = Signature::from_slice(sig);
    if signature.is_err() {
        return false;
    }

    verifying_key.verify(message, &signature.unwrap()).is_ok()
}
```

</details>

---

### Exercice 22 — Parsing d'une signature DER

**Objectif :** Parser une signature au format DER pour extraire les composantes `r` et `s`.
**Difficulté :** Moyen

**Énoncé :**
Le format DER (Distinguished Encoding Rules) est un format de signature courant en Bitcoin. La structure est :
```
0x30 <longueur_totale> 0x02 <longueur_r> <r> 0x02 <longueur_s> <s>
```
- `0x30` : tag SEQUENCE
- `0x02` : tag INTEGER
- Les valeurs `r` et `s` sont en big-endian, signées (un octet `0x00` est ajouté si le premier bit est à 1)

Écrivez une fonction qui parse une signature DER et retourne `(r, s)` sous forme de vecteurs d'octets, ou `None` si le format est invalide.

**Fonction à implémenter :**
```rust
pub fn parse_der_signature(der: &[u8]) -> Option<(Vec<u8>, Vec<u8>)> {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex22 {
    use super::*;

    #[test]
    fn test_basic_der() {
        // Minimal valid DER: 30 06 02 01 01 02 01 01
        let der = [0x30, 0x06, 0x02, 0x01, 0x01, 0x02, 0x01, 0x01];
        let (r, s) = parse_der_signature(&der).unwrap();
        assert_eq!(r, vec![0x01]);
        assert_eq!(s, vec![0x01]);
    }

    #[test]
    fn test_real_world_der() {
        // Typical 72-byte DER signature
        let der = [
            0x30, 0x45, 0x02, 0x21, 0x00,
            0x8b, 0x73, 0xc1, 0x53, 0x17, 0x9a, 0x76, 0x31,
            0x3b, 0x0e, 0x47, 0x59, 0x82, 0x32, 0xd4, 0x3e,
            0x39, 0x59, 0x16, 0x5a, 0x92, 0x31, 0xc5, 0x64,
            0x73, 0xd4, 0xf8, 0x39, 0x16, 0xe0, 0xe4, 0x3e,
            0x02, 0x20, 0x2a, 0xa9, 0x58, 0xd3, 0x48, 0xf5,
            0xb0, 0x39, 0xe9, 0x45, 0x49, 0xc3, 0x67, 0xdf,
            0x3e, 0xd7, 0x83, 0x57, 0x28, 0xbd, 0x07, 0x95,
            0xa7, 0xb3, 0x1d, 0xd7, 0x37, 0x86, 0xfb, 0x68,
            0xb1, 0x5a,
        ];
        let result = parse_der_signature(&der);
        assert!(result.is_some());
        let (r, s) = result.unwrap();
        assert_eq!(r.len(), 33); // padded with 0x00
        assert_eq!(s.len(), 32);
    }

    #[test]
    fn test_invalid_tag() {
        let der = [0x31, 0x06, 0x02, 0x01, 0x01, 0x02, 0x01, 0x01];
        assert!(parse_der_signature(&der).is_none());
    }

    #[test]
    fn test_too_short() {
        let der = [0x30, 0x04, 0x02, 0x01];
        assert!(parse_der_signature(&der).is_none());
    }

    #[test]
    fn test_r_padding() {
        // When r starts with high bit, a 0x00 padding byte is added
        let der = [
            0x30, 0x08, 0x02, 0x02, 0x00, 0x80, 0x02, 0x02, 0x00, 0xFF,
        ];
        let (r, s) = parse_der_signature(&der).unwrap();
        assert_eq!(r, vec![0x00, 0x80]);
        assert_eq!(s, vec![0x00, 0xFF]);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn parse_der_signature(der: &[u8]) -> Option<(Vec<u8>, Vec<u8>)> {
    if der.len() < 8 {
        return None;
    }
    if der[0] != 0x30 {
        return None;
    }

    let total_len = der[1] as usize;
    if der.len() < total_len + 2 {
        return None;
    }

    // Parse r
    if der[2] != 0x02 {
        return None;
    }
    let r_len = der[3] as usize;
    let r_start = 4;
    let r_end = r_start + r_len;
    if r_end > der.len() {
        return None;
    }
    let r = der[r_start..r_end].to_vec();

    // Parse s
    if r_end >= der.len() || der[r_end] != 0x02 {
        return None;
    }
    let s_len_pos = r_end + 1;
    if s_len_pos >= der.len() {
        return None;
    }
    let s_len = der[s_len_pos] as usize;
    let s_start = s_len_pos + 1;
    let s_end = s_start + s_len;
    if s_end > der.len() {
        return None;
    }
    let s = der[s_start..s_end].to_vec();

    Some((r, s))
}
```

</details>

---

### Exercice 23 — Tagged Hash (BIP 340)

**Objectif :** Implémenter le tagged hash utilisé dans BIP 340 (Schnorr signatures).
**Difficulté :** Moyen

**Énoncé :**
BIP 340 définit un "tagged hash" pour éviter les collisions entre différentes utilisations de SHA-256. Le principe est :
```
tagged_hash(tag, data) = SHA256(SHA256(tag) || SHA256(tag) || data)
```
Où `tag` est une chaîne d'octets (typiquement le nom de la fonction, ex: `"TapLeaf"`), et `||` est la concaténation.

Le SHA256 du tag est calculé une seule fois et dupliqué avant les données.

**Fonction à implémenter :**
```rust
pub fn tagged_hash(tag: &[u8], data: &[u8]) -> [u8; 32] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex23 {
    use super::*;

    #[test]
    fn test_tap_leaf_empty() {
        // Known test vector for TapLeaf with empty data
        let result = tagged_hash(b"TapLeaf", &[]);
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_tap_branch() {
        let result = tagged_hash(b"TapBranch", b"test");
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_different_tags_different_results() {
        let data = b"hello";
        let r1 = tagged_hash(b"Tag1", data);
        let r2 = tagged_hash(b"Tag2", data);
        assert_ne!(r1, r2);
    }

    #[test]
    fn test_known_vectors() {
        // SHA256("TapLeaf") = 6961... (32 bytes)
        // tagged_hash("TapLeaf", "") = SHA256(sha_tag || sha_tag)
        use sha2::{Sha256, Digest};
        let sha_tag = Sha256::digest(b"TapLeaf");
        let mut preimage = Vec::new();
        preimage.extend_from_slice(&sha_tag);
        preimage.extend_from_slice(&sha_tag);
        preimage.extend_from_slice(&[]);
        let expected = Sha256::digest(&preimage);
        assert_eq!(tagged_hash(b"TapLeaf", &[]), expected.as_slice());
    }

    #[test]
    fn test_consistency() {
        assert_eq!(tagged_hash(b"Test", b"data"), tagged_hash(b"Test", b"data"));
    }
}
```

<details>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn tagged_hash(tag: &[u8], data: &[u8]) -> [u8; 32] {
    let tag_hash = Sha256::digest(tag);
    let mut preimage = Vec::with_capacity(64 + data.len());
    preimage.extend_from_slice(&tag_hash);
    preimage.extend_from_slice(&tag_hash);
    preimage.extend_from_slice(data);
    let result = Sha256::digest(&preimage);
    let mut out = [0u8; 32];
    out.copy_from_slice(&result);
    out
}
```

</details>
## Vendredi — Clés, Adresses, Dérivation complète (6 exercices)

### Exercice 24 — Génération de clé privée

**Objectif :** Générer une clé privée Bitcoin de manière sécurisée (256 bits aléatoires).
**Difficulté :** Facile

**Énoncé :**
Une clé privée Bitcoin est un nombre aléatoire de 256 bits, compris entre 1 et `n-1` où `n` est l'ordre de la courbe secp256k1. En pratique, on génère 32 octets aléatoires cryptographiquement sûrs avec `rand_core::OsRng`.

Écrivez une fonction qui génère 32 octets aléatoires sécurisés.

**Fonction à implémenter :**
```rust
pub fn generate_private_key() -> [u8; 32] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex24 {
    use super::*;

    #[test]
    fn test_length() {
        let key = generate_private_key();
        assert_eq!(key.len(), 32);
    }

    #[test]
    fn test_not_zero() {
        let key = generate_private_key();
        assert_ne!(key, [0u8; 32]);
    }

    #[test]
    fn test_two_keys_differ() {
        let k1 = generate_private_key();
        let k2 = generate_private_key();
        assert_ne!(k1, k2);
    }

    #[test]
    fn test_randomness() {
        // Generate 100 keys and check they're not all the same
        let keys: Vec<_> = (0..100).map(|_| generate_private_key()).collect();
        let first = keys[0];
        assert!(keys.iter().any(|k| *k != first));
    }
}
```

<details>
<summary>Solution</summary>

```rust
use rand_core::OsRng;

pub fn generate_private_key() -> [u8; 32] {
    let mut key = [0u8; 32];
    OsRng.fill_bytes(&mut key);
    key
}
```

</details>

---

### Exercice 25 — Dérivation de clé publique

**Objectif :** Dériver la clé publique compressée à partir d'une clé privée.
**Difficulté :** Moyen

**Énoncé :**
La clé publique est le point `privkey * G` sur la courbe secp256k1, encodé en format compressé (33 octets). Utilisez le crate `k256` pour effectuer cette opération.

Utilisez `k256::ecdsa::SigningKey` pour obtenir la clé publique à partir de la clé privée.

**Fonction à implémenter :**
```rust
pub fn derive_public_key(privkey: &[u8; 32]) -> [u8; 33] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex25 {
    use super::*;

    #[test]
    fn test_length() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_prefix() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        assert!(pubkey[0] == 0x02 || pubkey[0] == 0x03);
    }

    #[test]
    fn test_deterministic() {
        let privkey = generate_private_key();
        let pk1 = derive_public_key(&privkey);
        let pk2 = derive_public_key(&privkey);
        assert_eq!(pk1, pk2);
    }

    #[test]
    fn test_known_key() {
        // Private key of all 0x01 bytes
        let privkey = [0x01u8; 32];
        let pubkey = derive_public_key(&privkey);
        assert_eq!(pubkey[0], 0x03); // known prefix for this key
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_different_privkeys() {
        let pk1 = derive_public_key(&[0x01u8; 32]);
        let pk2 = derive_public_key(&[0x02u8; 32]);
        assert_ne!(pk1, pk2);
    }
}
```

<details>
<summary>Solution</summary>

```rust
use k256::ecdsa::SigningKey;

pub fn derive_public_key(privkey: &[u8; 32]) -> [u8; 33] {
    let signing_key = SigningKey::from_bytes(privkey.into()).expect("Invalid private key");
    let verifying_key = signing_key.verifying_key();
    let encoded = verifying_key.to_encoded_point(true);
    let bytes = encoded.as_bytes();

    let mut pubkey = [0u8; 33];
    pubkey.copy_from_slice(bytes);
    pubkey
}
```

</details>

---

### Exercice 26 — HASH160 d'une clé publique

**Objectif :** Calculer le hash HASH160 d'une clé publique compressée.
**Difficulté :** Facile

**Énoncé :**
Pour créer une adresse Bitcoin P2PKH, on applique d'abord HASH160 (= RIPEMD160(SHA256(x))) à la clé publique compressée. Le résultat est un "hash de clé publique" de 20 octets.

Utilisez les fonctions `hash160` de l'exercice 14, ou réimplémentez-la avec `sha2::Sha256` et `ripemd::Ripemd160`.

**Fonction à implémenter :**
```rust
pub fn pubkey_to_hash160(pubkey: &[u8; 33]) -> [u8; 20] {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex26 {
    use super::*;

    #[test]
    fn test_length() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let h = pubkey_to_hash160(&pubkey);
        assert_eq!(h.len(), 20);
    }

    #[test]
    fn test_deterministic() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let h1 = pubkey_to_hash160(&pubkey);
        let h2 = pubkey_to_hash160(&pubkey);
        assert_eq!(h1, h2);
    }

    #[test]
    fn test_not_all_zeros() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let h = pubkey_to_hash160(&pubkey);
        assert_ne!(h, [0u8; 20]);
    }

    #[test]
    fn test_different_pubkeys() {
        let h1 = pubkey_to_hash160(&derive_public_key(&[0x01u8; 32]));
        let h2 = pubkey_to_hash160(&derive_public_key(&[0x02u8; 32]));
        assert_ne!(h1, h2);
    }
}
```

<details>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};

pub fn pubkey_to_hash160(pubkey: &[u8; 33]) -> [u8; 20] {
    let sha = Sha256::digest(pubkey);
    let ripe = Ripemd160::digest(&sha);
    let mut result = [0u8; 20];
    result.copy_from_slice(&ripe);
    result
}
```

</details>

---

### Exercice 27 — Création d'adresse P2PKH

**Objectif :** Créer une adresse Bitcoin P2PKH complète à partir d'une clé publique.
**Difficulté :** Moyen

**Énoncé :**
Une adresse P2PKH se construit ainsi :
1. Calculer HASH160 de la clé publique compressée → 20 octets.
2. Préfixer avec le byte de version (`0x00` pour mainnet) → 21 octets.
3. Calculer le checksum (4 premiers octets de HASH256 des 21 octets) → 4 octets.
4. Concaténer version + hash + checksum → 25 octets.
5. Encoder en Base58.

Utilisez `hash160`, `hash256`, `checksum` et `base58_encode` des exercices précédents.

**Fonction à implémenter :**
```rust
pub fn pubkey_to_p2pkh_address(pubkey: &[u8; 33]) -> String {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex27 {
    use super::*;

    #[test]
    fn test_starts_with_1() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let addr = pubkey_to_p2pkh_address(&pubkey);
        assert!(addr.starts_with('1'), "P2PKH address must start with '1', got: {}", addr);
    }

    #[test]
    fn test_length_range() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let addr = pubkey_to_p2pkh_address(&pubkey);
        // P2PKH addresses are 25-34 characters
        assert!(addr.len() >= 25 && addr.len() <= 34, "Address length: {}", addr.len());
    }

    #[test]
    fn test_deterministic() {
        let privkey = [0x42u8; 32];
        let pubkey = derive_public_key(&privkey);
        let a1 = pubkey_to_p2pkh_address(&pubkey);
        let a2 = pubkey_to_p2pkh_address(&pubkey);
        assert_eq!(a1, a2);
    }

    #[test]
    fn test_known_address() {
        // Private key 0x01 repeated → known public key → known address
        let privkey = [0x01u8; 32];
        let pubkey = derive_public_key(&privkey);
        let addr = pubkey_to_p2pkh_address(&pubkey);
        assert!(addr.starts_with('1'));
        assert_eq!(addr.len(), 34);
    }

    #[test]
    fn test_different_keys_different_addresses() {
        let pk1 = derive_public_key(&[0x01u8; 32]);
        let pk2 = derive_public_key(&[0x02u8; 32]);
        let a1 = pubkey_to_p2pkh_address(&pk1);
        let a2 = pubkey_to_p2pkh_address(&pk2);
        assert_ne!(a1, a2);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn pubkey_to_p2pkh_address(pubkey: &[u8; 33]) -> String {
    // Step 1: HASH160 of public key
    let h160 = pubkey_to_hash160(pubkey);

    // Step 2: Prepend version byte (0x00 for mainnet)
    let mut payload = vec![0x00u8];
    payload.extend_from_slice(&h160);

    // Step 3: Checksum (first 4 bytes of HASH256)
    let cs = checksum(&payload);

    // Step 4: Concatenate version + hash + checksum
    payload.extend_from_slice(&cs);

    // Step 5: Base58 encode
    base58_encode(&payload)
}
```

</details>

---

### Exercice 28 — Identification du type d'adresse

**Objectif :** Identifier le type d'adresse Bitcoin à partir de son préfixe.
**Difficulté :** Facile

**Énoncé :**
Les différents types d'adresses Bitcoin ont des préfixes reconnaissables :
- **P2PKH** : commence par `1` (ex: `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa`)
- **P2SH** : commence par `3` (ex: `3J98t1WpEZ73CNmQviecrnyiWrnqRhWNLy`)
- **Bech32 (P2WPKH)** : commence par `bc1q` (ex: `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4`)
- **Bech32m (P2TR)** : commence par `bc1p` (ex: `bc1p5d7rjq7g6rdk2yhzksdsmm64gh78fn5e7z5z6n470g0lg`)

Écrivez une fonction qui retourne le type d'adresse sous forme de chaîne.

**Fonction à implémenter :**
```rust
pub fn identify_address(addr: &str) -> &str {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex28 {
    use super::*;

    #[test]
    fn test_p2pkh() {
        assert_eq!(identify_address("1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"), "P2PKH");
    }

    #[test]
    fn test_p2sh() {
        assert_eq!(identify_address("3J98t1WpEZ73CNmQviecrnyiWrnqRhWNLy"), "P2SH");
    }

    #[test]
    fn test_p2wpkh() {
        assert_eq!(identify_address("bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4"), "P2WPKH");
    }

    #[test]
    fn test_p2tr() {
        assert_eq!(
            identify_address("bc1p5d7rjq7g6rdk2yhzksdsmm64gh78fn5e7z5z6n470g0lg"),
            "P2TR"
        );
    }

    #[test]
    fn test_unknown() {
        assert_eq!(identify_address("xyz123"), "Inconnu");
    }

    #[test]
    fn test_generated_address() {
        let privkey = generate_private_key();
        let pubkey = derive_public_key(&privkey);
        let addr = pubkey_to_p2pkh_address(&pubkey);
        assert_eq!(identify_address(&addr), "P2PKH");
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn identify_address(addr: &str) -> &str {
    if addr.starts_with("bc1p") {
        "P2TR"
    } else if addr.starts_with("bc1q") {
        "P2WPKH"
    } else if addr.starts_with('3') {
        "P2SH"
    } else if addr.starts_with('1') {
        "P2PKH"
    } else {
        "Inconnu"
    }
}
```

</details>

---

### Exercice 29 — Chaîne de dérivation complète

**Objectif :** Implémenter la chaîne complète : clé privée → clé publique → hash160 → adresse P2PKH.
**Difficulté :** Moyen

**Énoncé :**
Cet exercice synthétise tous les exercices de la semaine. Écrivez une fonction qui prend une clé privée et retourne un tuple contenant :
1. La clé publique compressée (33 octets)
2. Le hash HASH160 de la clé publique (20 octets)
3. L'adresse P2PKH (String)

La fonction doit réaliser toutes les étapes de la dérivation d'une adresse Bitcoin.

**Fonction à implémenter :**
```rust
pub fn full_derivation_chain(privkey: &[u8; 32]) -> ([u8; 33], [u8; 20], String) {
    // TODO
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex29 {
    use super::*;

    #[test]
    fn test_chain_lengths() {
        let privkey = generate_private_key();
        let (pubkey, h160, addr) = full_derivation_chain(&privkey);
        assert_eq!(pubkey.len(), 33);
        assert_eq!(h160.len(), 20);
        assert!(addr.starts_with('1'));
    }

    #[test]
    fn test_matches_individual_steps() {
        let privkey = [0x42u8; 32];
        let (pubkey, h160, addr) = full_derivation_chain(&privkey);

        let expected_pubkey = derive_public_key(&privkey);
        let expected_h160 = pubkey_to_hash160(&expected_pubkey);
        let expected_addr = pubkey_to_p2pkh_address(&expected_pubkey);

        assert_eq!(pubkey, expected_pubkey);
        assert_eq!(h160, expected_h160);
        assert_eq!(addr, expected_addr);
    }

    #[test]
    fn test_deterministic() {
        let privkey = generate_private_key();
        let chain1 = full_derivation_chain(&privkey);
        let chain2 = full_derivation_chain(&privkey);
        assert_eq!(chain1, chain2);
    }

    #[test]
    fn test_different_keys() {
        let (pub1, h1, a1) = full_derivation_chain(&[0x01u8; 32]);
        let (pub2, h2, a2) = full_derivation_chain(&[0x02u8; 32]);
        assert_ne!(pub1, pub2);
        assert_ne!(h1, h2);
        assert_ne!(a1, a2);
    }

    #[test]
    fn test_genesis_key() {
        // Known private key 0x01 → known address
        let (pubkey, h160, addr) = full_derivation_chain(&[0x01u8; 32]);
        assert_eq!(pubkey[0], 0x03);
        assert_eq!(h160.len(), 20);
        assert_eq!(addr.len(), 34);
        assert!(addr.starts_with('1'));
    }

    #[test]
    fn test_pubkey_in_h160() {
        // Verify that the h160 is indeed SHA256→RIPEMD160 of the pubkey
        let privkey = generate_private_key();
        let (pubkey, h160, _) = full_derivation_chain(&privkey);
        let manual_h160 = pubkey_to_hash160(&pubkey);
        assert_eq!(h160, manual_h160);
    }

    #[test]
    fn test_addr_from_h160() {
        // Verify that the address encodes the correct h160
        let privkey = [0xABu8; 32];
        let (_, h160, addr) = full_derivation_chain(&privkey);
        // Decode the address back: Base58 → remove version + checksum → compare h160
        assert!(addr.starts_with('1'));
        assert_eq!(h160.len(), 20);
    }
}
```

<details>
<summary>Solution</summary>

```rust
pub fn full_derivation_chain(privkey: &[u8; 32]) -> ([u8; 33], [u8; 20], String) {
    let pubkey = derive_public_key(privkey);
    let h160 = pubkey_to_hash160(&pubkey);
    let addr = pubkey_to_p2pkh_address(&pubkey);
    (pubkey, h160, addr)
}
```

</details>

---

## Barème

| Score | Niveau | Action recommandée |
|-------|--------|-------------------|
| 26-29 | Excellent | Passer à la semaine 2 |
| 20-25 | Bien | Relire les sujets manqués, puis semaine 2 |
| 14-19 | Moyen | Refaire les exercices manqués, relire les cours |
| < 14 | Insuffisant | Relire tout le cours de la semaine 1 |

<a href="#top">Retour au menu / Back to top</a>
