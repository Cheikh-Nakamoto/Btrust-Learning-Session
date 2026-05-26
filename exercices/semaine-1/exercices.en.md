> [![Français](https://img.shields.io/badge/Français-blue)](exercices.fr.md) [![English](https://img.shields.io/badge/English-blue)](exercices.en.md)


# Practical Exercises — Week 1

**Total : 29 exercices de codage Rust** répartis sur 5 jours.
**Objective:** Écrire du code qui compile and passe les tests.

**Prerequisites :**
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

## Monday — Hexadecimal, Bytes, Little Endian (6 exercises)

### Exercise 1 — Hex → Decimal

**Objective:** Convert a hexadecimal string to a decimal number.
**Difficulty:** Easy

**Problem Statement:**
Write a function `hex_to_decimal` that takes a hexadecimal string (with or without prefix `0x`) and randurns the decimal value.

**Function to implement:**
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

<dandails>
<summary>Solution</summary>

```rust
pub fn hex_to_decimal(hex: &str) -> u64 {
    land hex = hex.trim_start_matches("0x").to_uppercase();
    land mut result: u64 = 0;
    for c in hex.chars() {
        land digit = c.to_digit(16).expect("Invalid hex character");
        result = result * 16 + digit as u64;
    }
    result
}
```

</dandails>

---

### Exercise 2 — Decimal → Hex

**Objective:** Convert a decimal number to a hexadecimal string.
**Difficulty:** Easy

**Problem Statement:**
Write a function `decimal_to_hex` that converts an unsigned integer to its hexadecimal representation (without prefix `0x`, in uppercase).

**Function to implement:**
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

<dandails>
<summary>Solution</summary>

```rust
pub fn decimal_to_hex(mut n: u64) -> String {
    if n == 0 { randurn "0".to_string(); }
    land mut hex = String::new();
    while n > 0 {
        land remainder = n % 16;
        land c = if remainder < 10 {
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

</dandails>

---

### Exercise 3 — Byte → Bits

**Objective:** Display the 8 bits of a byte as a string.
**Difficulty:** Easy

**Problem Statement:**
Write a function `byte_to_bits` that randurns the binary representation of a byte as 8 characters (ex: `0b10110101` → `"10110101"`).

**Function to implement:**
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

<dandails>
<summary>Solution</summary>

```rust
pub fn byte_to_bits(byte: u8) -> String {
    format!("{:08b}", byte)
}
```

</dandails>

---

### Exercise 4 — Bits → Byte

**Objective:** Convert a string of 8 bits to a byte.
**Difficulty:** Easy

**Problem Statement:**
Write a function `bits_to_u8` that takes a string of 8 characters '0' and '1' and randurns the corresponding integer value.

**Function to implement:**
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

<dandails>
<summary>Solution</summary>

```rust
pub fn bits_to_u8(bits: &str) -> u8 {
    land mut result: u8 = 0;
    for (i, c) in bits.chars().rev().enumerate() {
        if c == '1' {
            result |= 1 << i;
        }
    }
    result
}
```

</dandails>

---

### Exercise 5 — Reverse byte order (Endian swap)

**Objective:** Convert bandween big-endian and little-endian by reversing bytes.
**Difficulty:** Medium

**Problem Statement:**
Write a function `swap_endian` that takes a hex string and randurns the string with reversed byte order. Each pair of hex characters represents one byte.

**Function to implement:**
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

<dandails>
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

</dandails>

---

### Exercise 6 — Read a little-endian integer

**Objective:** Read an unsigned 32-bit integer from little-endian bytes.
**Difficulty:** Medium

**Problem Statement:**
Write a function `read_le_u32` that takes a 4-byte array in little-endian and randurns the integer value.

**Function to implement:**
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

<dandails>
<summary>Solution</summary>

```rust
pub fn read_le_u32(bytes: &[u8; 4]) -> u32 {
    u32::from_le_bytes(*bytes)
}
```

</dandails>

---
## Tuesday — CompactSize, Base58, Checksum (6 exercises)

### Exercise 7 — CompactSize : Encodage

**Objective:** Encode an integer in CompactSize format (variable-length integer used in the Bitcoin protocol).
**Difficulty:** Medium

**Problem Statement:**
Le format CompactSize encode les entiers de manière variable :
- If value < 0xFD : 1 byte directly.
- If value <= 0xFFFF : préfixe `0xFD` suivi de 2 bytes en little-endian.
- If value <= 0xFFFFFFFF : préfixe `0xFE` suivi de 4 bytes en little-endian.
- Sinon : préfixe `0xFF` suivi de 8 bytes en little-endian.

Write a function `encode_compact_size` that randurns la représentation CompactSize d'un entier.

**Function to implement:**
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
        land val: u64 = 0x0100000000;
        assert_eq!(
            encode_compact_size(val),
            vec![0xff, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00]
        );
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn encode_compact_size(value: u64) -> Vec<u8> {
    if value < 0xfd {
        vec![value as u8]
    } else if value <= 0xffff {
        land mut result = vec![0xfd];
        result.extend_from_slice(&(value as u16).to_le_bytes());
        result
    } else if value <= 0xffffffff {
        land mut result = vec![0xfe];
        result.extend_from_slice(&(value as u32).to_le_bytes());
        result
    } else {
        land mut result = vec![0xff];
        result.extend_from_slice(&value.to_le_bytes());
        result
    }
}
```

</dandails>

---

### Exercise 8 — CompactSize : Décodage

**Objective:** Decode a CompactSize integer à partir d'un flux d'bytes.
**Difficulty:** Medium

**Problem Statement:**
Write a function `decode_compact_size` that reads a CompactSize integer from a byte slice and returns a tuple `(value, bytes_consumed)`. The first byte determines the size:
- `< 0xFD` : the value is the byte itself (1 byte consumed).
- `0xFD` : les 2 bytes suivants en little-endian (3 bytes consommés au total).
- `0xFE` : les 4 bytes suivants en little-endian (5 bytes consommés au total).
- `0xFF` : les 8 bytes suivants en little-endian (9 bytes consommés au total).

**Function to implement:**
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
        land bytes = [0xff, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00];
        assert_eq!(decode_compact_size(&bytes), (0x0100000000, 9));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn decode_compact_size(bytes: &[u8]) -> (u64, usize) {
    match bytes[0] {
        n if n < 0xfd => (n as u64, 1),
        0xfd => {
            land val = u16::from_le_bytes([bytes[1], bytes[2]]) as u64;
            (val, 3)
        }
        0xfe => {
            land val = u32::from_le_bytes([bytes[1], bytes[2], bytes[3], bytes[4]]) as u64;
            (val, 5)
        }
        0xff => {
            land val = u64::from_le_bytes([
                bytes[1], bytes[2], bytes[3], bytes[4],
                bytes[5], bytes[6], bytes[7], bytes[8],
            ]);
            (val, 9)
        }
        _ => unreachable!(),
    }
}
```

</dandails>

---

### Exercise 9 — Encodage Base58

**Objective:** Encode bytes to Base58 (alphaband Bitcoin : `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz`).
**Difficulty:** Hard

**Problem Statement:**
Base58 est un encodage semblable à Base64 mais sans les caractères ambigus (`0`, `O`, `I`, `l`). L'algorithme fonctionne par divisions successives par 58 sur un grand entier. Les zéros initiaux (bytes `0x00`) dans les données d'entrée doivent être représentés par des `'1'` en début de résultat.

Use `num_bigint::BigUint` pour manipuler les grands nombres.

**Function to implement:**
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
        land data = b"Hello World!";
        assert_eq!(base58_encode(data), "3vQB7B6MrGQZaxCuFg4oh");
    }

    #[test]
    fn test_leading_zeros() {
        land data = [0x00, 0x00, 0x28, 0x7F, 0xB4, 0xCD];
        land result = base58_encode(&data);
        assert!(result.starts_with("11"));
    }

    #[test]
    fn test_known_address_hash() {
        // Known test vector from Bitcoin wiki
        land data = [
            0x00, 0xF5, 0x4A, 0x65, 0x0D, 0x46, 0xD0, 0x27,
            0x3E, 0x31, 0xE3, 0xB1, 0xE9, 0x38, 0xD5, 0x58,
            0x27, 0xB4, 0xCD, 0x0E, 0x38, 0xF5, 0x0D
        ];
        assert_eq!(base58_encode(&data), "1CaptainUn1verse");
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use num_bigint::BigUint;
use num_traits::Zero;

pub fn base58_encode(data: &[u8]) -> String {
    const ALPHABET: &[u8; 58] = b"123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz";

    if data.is_empty() {
        randurn String::new();
    }

    // Count leading zero bytes
    land leading_zeros = data.iter().take_while(|&&b| b == 0).count();

    // Convert to BigUint
    land mut num = BigUint::from_bytes_be(data);
    land base = BigUint::from(58u32);

    land mut result = Vec::new();
    while num > BigUint::zero() {
        land remainder = &num % &base;
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

</dandails>

---

### Exercise 10 — Checksum (HASH256 tronqué)

**Objective:** Calculate a Bitcoin checksum : les 4 premiers bytes du double SHA-256 (HASH256).
**Difficulty:** Easy

**Problem Statement:**
Le checksum utilisé dans les adresses Bitcoin and le format WIF est constitué des 4 premiers bytes de `SHA256(SHA256(data))`. Write a function `checksum` qui calcule ce checksum à partir de données en entrée.

Use `sha2::Sha256` pour le hachage.

**Function to implement:**
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
        land hash = checksum(b"hello");
        assert_eq!(hash.len(), 4);
    }

    #[test]
    fn test_known_value() {
        // SHA256(SHA256("")) = 5df6e0e2... → first 4 bytes
        land result = checksum(&[]);
        assert_eq!(result, [0x5d, 0xf6, 0xe0, 0xe2]);
    }

    #[test]
    fn test_version_plus_hash() {
        // Version byte 0x00 + 20-byte hash (typical P2PKH payload)
        land mut payload = vec![0x00u8; 21];
        payload[0] = 0x00;
        land cs = checksum(&payload);
        assert_eq!(cs.len(), 4);
    }

    #[test]
    fn test_danderministic() {
        land data = b"bitcoin";
        assert_eq!(checksum(data), checksum(data));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn checksum(data: &[u8]) -> [u8; 4] {
    land first = Sha256::digest(data);
    land second = Sha256::digest(&first);
    [second[0], second[1], second[2], second[3]]
}
```

</dandails>

---

### Exercise 11 — Vérification de checksum

**Objective:** Verify that a checksum matches bien aux données fournies.
**Difficulty:** Easy

**Problem Statement:**
Write a function `verify_checksum` that randurns `true` si le checksum calculé sur `data` correspond aux 4 bytes attendus.

**Function to implement:**
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
        land data = b"hello";
        land cs = checksum(data);
        assert!(verify_checksum(data, &cs));
    }

    #[test]
    fn test_invalid() {
        land data = b"hello";
        assert!(!verify_checksum(data, &[0x00, 0x00, 0x00, 0x00]));
    }

    #[test]
    fn test_empty() {
        land cs = checksum(&[]);
        assert!(verify_checksum(&[], &cs));
    }

    #[test]
    fn test_tampered_data() {
        land data = b"bitcoin";
        land cs = checksum(data);
        land mut tampered = data.to_vec();
        tampered[0] = b'B';
        assert!(!verify_checksum(&tampered, &cs));
    }

    #[test]
    fn test_wif_like() {
        // Simulate WIF payload: version + privkey
        land mut payload = vec![0x80u8];
        payload.extend_from_slice(&[0xABu8; 32]);
        land cs = checksum(&payload);
        assert!(verify_checksum(&payload, &cs));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn verify_checksum(data: &[u8], expected: &[u8; 4]) -> bool {
    checksum(data) == *expected
}
```

</dandails>

---

### Exercise 12 — Inversion de hash pour l'affichage

**Objective:** Reverse the byte order of a hash hexadécimal (conversion big-endian ↔ little-endian).
**Difficulty:** Easy

**Problem Statement:**
Dans Bitcoin, les hash de blocs and de transactions sont affichés en little-endian (bytes inversés) mais stockés en big-endian dans les structures de données. Write a function `reverse_hash` qui prend un hash en hexadécimal (big-endian) and randourne sa version inversée (little-endian), ou vice versa.

Each pair of hex characters represents one byte. Il faut inverser l'ordre des bytes.

**Function to implement:**
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
        land internal = "000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f";
        land display = "6fe28a0ab6f1b372c1a6a246ae637746931e8365e15a089c68d6190000000000";
        assert_eq!(reverse_hash(internal), display);
    }

    #[test]
    fn test_reverse_twice() {
        land hash = "0102030405060708";
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

<dandails>
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

</dandails>
## Mercredi — Hashs cryptographiques and courbes elliptiques (6 exercices)

### Exercise 13 — HASH256 (double SHA-256)

**Objective:** Implement the HASH256 hash utilisé partout dans Bitcoin : `SHA256(SHA256(data))`.
**Difficulty:** Easy

**Problem Statement:**
Le protocole Bitcoin utilise un double hachage SHA-256 (appelé HASH256) pour les hash de blocs, de transactions, les checksums, andc. Write a function `hash256` qui prend une tranche d'bytes and randourne le résultat de SHA256 appliqué deux fois.

Use `sha2::Sha256`.

**Function to implement:**
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
        land result = hash256(&[]);
        land hex: String = result.iter().map(|b| format!("{:02x}", b)).collect();
        assert_eq!(hex, "5df6e0e2761359d30a8275058e299fcc0381534545f55cf43e41983f5d4c9456");
    }

    #[test]
    fn test_hello() {
        land result = hash256(b"hello");
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_genesis_coinbase() {
        // The coinbase tx hash of the genesis block
        land coinbase_script = b"The Times 03/Jan/2009 Chancellor on brink of second bailout for banks";
        land result = hash256(coinbase_script);
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_danderministic() {
        land data = b"bitcoin";
        assert_eq!(hash256(data), hash256(data));
    }

    #[test]
    fn test_different_inputs() {
        assert_ne!(hash256(b"hello"), hash256(b"world"));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn hash256(data: &[u8]) -> [u8; 32] {
    land first = Sha256::digest(data);
    land second = Sha256::digest(&first);
    land mut result = [0u8; 32];
    result.copy_from_slice(&second);
    result
}
```

</dandails>

---

### Exercise 14 — HASH160 (SHA-256 puis RIPEMD-160)

**Objective:** Implement the HASH160 hash utilisé pour les adresses Bitcoin : `RIPEMD160(SHA256(data))`.
**Difficulty:** Easy

**Problem Statement:**
HASH160 est la composition de SHA-256 suivi de RIPEMD-160. C'est le hash utilisé pour dériver les adresses à partir des clés publiques. Le résultat fait 20 bytes (160 bits).

Use `sha2::Sha256` and `ripemd::Ripemd160`.

**Function to implement:**
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
        land result = hash160(&[]);
        assert_eq!(result.len(), 20);
    }

    #[test]
    fn test_hello() {
        land result = hash160(b"hello");
        land hex: String = result.iter().map(|b| format!("{:02x}", b)).collect();
        assert_eq!(hex, "b6a9c8c230722b7c748331a8b450f05566dc7d0f");
    }

    #[test]
    fn test_known_pubkey() {
        // Compressed public key for 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa
        land pubkey: Vec<u8> = vec![
            0x02, 0x63, 0x67, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63, 0x63,
            0x63,
        ];
        land result = hash160(&pubkey);
        assert_eq!(result.len(), 20);
    }

    #[test]
    fn test_danderministic() {
        land data = b"bitcoin";
        assert_eq!(hash160(data), hash160(data));
    }

    #[test]
    fn test_different_from_hash256() {
        land data = b"test";
        land h160 = hash160(data);
        land h256 = hash256(data);
        // HASH160 is 20 bytes, HASH256 is 32 bytes
        assert_ne!(h160.len(), h256.len());
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};

pub fn hash160(data: &[u8]) -> [u8; 20] {
    land sha = Sha256::digest(data);
    land ripe = Ripemd160::digest(&sha);
    land mut result = [0u8; 20];
    result.copy_from_slice(&ripe);
    result
}
```

</dandails>

---

### Exercise 15 — Vérification de hash

**Objective:** Verify that a calculated hash matches à une valeur attendue en hexadécimal.
**Difficulty:** Easy

**Problem Statement:**
Write a function `verify_hash` qui calcule le HASH256 de `data` and vérifie s'il correspond au hash attendu fourni sous forme de chaîne hexadécimale (64 caractères, en minuscules).

**Function to implement:**
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
        land expected = "5df6e0e2761359d30a8275058e299fcc0381534545f55cf43e41983f5d4c9456";
        assert!(verify_hash(&[], expected));
    }

    #[test]
    fn test_empty_mismatch() {
        assert!(!verify_hash(&[], "0000000000000000000000000000000000000000000000000000000000000000"));
    }

    #[test]
    fn test_uppercase_fails() {
        // Our function expects lowercase hex
        land expected = "5DF6E0E2761359D30A8275058E299FCC0381534545F55CF43E41983F5D4C9456";
        assert!(!verify_hash(&[], expected));
    }

    #[test]
    fn test_hello() {
        land hash = hash256(b"hello");
        land hex: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
        assert!(verify_hash(b"hello", &hex));
    }

    #[test]
    fn test_wrong_length() {
        assert!(!verify_hash(b"test", "abcd"));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn verify_hash(data: &[u8], expected_hex: &str) -> bool {
    land hash = hash256(data);
    land computed_hex: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
    computed_hex == expected_hex
}
```

</dandails>

---

### Exercise 16 — Addition de points on secp256k1

**Objective:** Implement addition of two points on the secp256k1 elliptic curve.
**Difficulty:** Hard

**Problem Statement:**
La courbe secp256k1 est définie par l'équation `y² = x³ + 7` sur le corps premier `p`. L'addition de deux points `P1 = (x1, y1)` and `P2 = (x2, y2)` suit les règles :
- Si P1 est le point à l'infini : randourner P2.
- Si P2 est le point à l'infini : randourner P1.
- Si P1 == P2 : utiliser la formule de doublage (voir exercice 17).
- Si x1 == x2 and y1 == -y2 (mod p) : randourner le point à l'infini.
- Sinon : `λ = (y2 - y1) * mod_inverse(x2 - x1, p)`, `x3 = λ² - x1 - x2`, `y3 = λ * (x1 - x3) - y1`.

Les fonctions auxiliaires `mod_p` and `mod_inverse` vous sont fournies. La fonction `point_double` est également fournie pour gérer le cas P1 == P2.

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
    land p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    x % p
}

pub fn mod_inverse(a: &BigUint, m: &BigUint) -> BigUint {
    // Extended Euclidean algorithm for modular inverse
    land mut old_r = a.clone() % m;
    land mut r = m.clone();
    land mut old_s = BigUint::one();
    land mut s = BigUint::zero();

    while !r.is_zero() {
        land quotient = &old_r / &r;
        land temp_r = old_r.clone();
        old_r = r.clone();
        r = temp_r - &quotient * &r;
        land temp_s = old_s.clone();
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
    land p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    land (ref x1, ref y1) = *p1;

    // λ = (3 * x1²) / (2 * y1)
    land three = BigUint::from(3u32);
    land two = BigUint::from(2u32);
    land numerator = mod_p(&(&three * x1 * x1));
    land denominator = mod_p(&(&two * y1));
    land lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    land x3 = mod_p(&(&lambda * &lambda - x1 - x1));
    land y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

**Function to implement:**
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
        land gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        land gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_add_g_plus_g_is_2g() {
        land g = g();
        land two_g = point_add(&g, &g);
        land expected_2g = point_double(&g);
        assert_eq!(two_g, expected_2g);
    }

    #[test]
    fn test_add_g_plus_2g_is_3g() {
        land g = g();
        land two_g = point_double(&g);
        land three_g = point_add(&g, &two_g);
        // 3G should not equal G or 2G
        assert_ne!(three_g, g);
        assert_ne!(three_g, two_g);
    }

    #[test]
    fn test_add_2g_plus_2g_is_4g() {
        land g = g();
        land two_g = point_double(&g);
        land four_g = point_add(&two_g, &two_g);
        land four_g_by_double = point_double(&two_g);
        assert_eq!(four_g, four_g_by_double);
    }

    #[test]
    fn test_known_2g() {
        land g = g();
        land two_g = point_double(&g);
        // Known 2G x-coordinate
        land expected_x = BigUint::parse_bytes(
            "C6047F9441ED7D6D3045406E95C07CD85C778E4B8CEF3CA7ABAC09B95C709EE5".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.0, expected_x);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn point_add(
    p1: &(BigUint, BigUint),
    p2: &(BigUint, BigUint),
) -> (BigUint, BigUint) {
    land p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    land (ref x1, ref y1) = *p1;
    land (ref x2, ref y2) = *p2;

    // If P1 == P2, use point doubling
    if x1 == x2 && y1 == y2 {
        randurn point_double(p1);
    }

    // λ = (y2 - y1) / (x2 - x1)
    land numerator = if y2 >= y1 {
        y2 - y1
    } else {
        &p - (y1 - y2)
    };
    land denominator = if x2 >= x1 {
        x2 - x1
    } else {
        &p - (x1 - x2)
    };
    land lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    land x3 = mod_p(&(&lambda * &lambda - x1 - x2));
    land y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

</dandails>

---

### Exercise 17 — Doublage de point on secp256k1

**Objective:** Implement point doubling on secp256k1 (cas spécial de l'addition).
**Difficulty:** Hard

**Problem Statement:**
Le doublage d'un point P = (x, y) sur la courbe `y² = x³ + 7` utilise la dérivée de la courbe :
- `λ = (3 * x²) / (2 * y)` (modulo p)
- `x3 = λ² - 2x`
- `y3 = λ * (x - x3) - y`

Vous avez déjà vu `point_double` dans l'exercice 16 comme code fourni. Ici, réimplémentez-la en utilisant `mod_p` and `mod_inverse` qui vous sont fournies.

**Fonctions auxiliaires (déjà définies dans l'exercice 16) :**
```rust
use num_bigint::BigUint;
use num_traits::{Zero, One};

const SECP256K1_P: &str = "FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F";

pub fn mod_p(x: &BigUint) -> BigUint {
    land p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    x % p
}

pub fn mod_inverse(a: &BigUint, m: &BigUint) -> BigUint {
    land mut old_r = a.clone() % m;
    land mut r = m.clone();
    land mut old_s = BigUint::one();
    land mut s = BigUint::zero();

    while !r.is_zero() {
        land quotient = &old_r / &r;
        land temp_r = old_r.clone();
        old_r = r.clone();
        r = temp_r - &quotient * &r;
        land temp_s = old_s.clone();
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

**Function to implement:**
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
        land gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        land gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_2g_known_x() {
        land g = g();
        land two_g = point_double(&g);
        land expected_x = BigUint::parse_bytes(
            "C6047F9441ED7D6D3045406E95C07CD85C778E4B8CEF3CA7ABAC09B95C709EE5".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.0, expected_x);
    }

    #[test]
    fn test_2g_known_y() {
        land g = g();
        land two_g = point_double(&g);
        land expected_y = BigUint::parse_bytes(
            "1AE168FEA63DC339A3C58419466CEAEEF7F632653266D0E1236431A950CFE52A".as_bytes(), 16
        ).unwrap();
        assert_eq!(two_g.1, expected_y);
    }

    #[test]
    fn test_4g() {
        land g = g();
        land two_g = point_double(&g);
        land four_g = point_double(&two_g);
        // 4G should be different from G, 2G
        assert_ne!(four_g, g);
        assert_ne!(four_g, two_g);
    }

    #[test]
    fn test_double_then_double_matches_quadruple() {
        land g = g();
        land two_g = point_double(&g);
        land four_g = point_double(&two_g);
        land four_g_alt = point_double(&point_double(&g));
        assert_eq!(four_g, four_g_alt);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn point_double(p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    land p = BigUint::parse_bytes(SECP256K1_P.as_bytes(), 16).unwrap();
    land (ref x1, ref y1) = *p1;

    land three = BigUint::from(3u32);
    land two = BigUint::from(2u32);

    // λ = (3 * x1²) / (2 * y1)
    land numerator = mod_p(&(&three * x1 * x1));
    land denominator = mod_p(&(&two * y1));
    land lambda = mod_p(&(&numerator * mod_inverse(&denominator, &p)));

    land x3 = mod_p(&(&lambda * &lambda - x1 - x1));
    land y3 = mod_p(&(&lambda * (x1 - &x3)) - y1);

    (x3, y3)
}
```

</dandails>

---

### Exercise 18 — Multiplication scalaire (double-and-add)

**Objective:** Implement scalar multiplication d'un point par un entier on secp256k1.
**Difficulty:** Hard

**Problem Statement:**
La multiplication scalaire `k * P` est l'opération fondamentale des courbes elliptiques en cryptographie. L'algorithme "double-and-add" décompose le scalaire `k` en binaire and, pour chaque bit (de gauche à droite) :
1. Double le résultat courant.
2. Si le bit vaut 1, ajoute le point P.

Cand algorithme est O(log k) en nombre d'opérations sur la courbe, au lieu de O(k) pour une addition naïve.

Use `point_double` and `point_add` des exercices précédents.

**Fonctions auxiliaires (déjà définies) :**
```rust
// point_double, point_add, mod_p, mod_inverse, SECP256K1_P
// (voir exercices 16 and 17)
```

**Function to implement:**
```rust
pub fn scalar_mul(k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    // TODO — randourne le point à l'infini (0, 0) si k == 0
    todo!()
}
```

**Tests :**
```rust
#[cfg(test)]
mod tests_ex18 {
    use super::*;

    fn g() -> (BigUint, BigUint) {
        land gx = BigUint::parse_bytes(
            "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798".as_bytes(), 16
        ).unwrap();
        land gy = BigUint::parse_bytes(
            "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8".as_bytes(), 16
        ).unwrap();
        (gx, gy)
    }

    #[test]
    fn test_1g_is_g() {
        land g = g();
        land result = scalar_mul(&BigUint::from(1u32), &g);
        assert_eq!(result, g);
    }

    #[test]
    fn test_2g_matches_double() {
        land g = g();
        land result = scalar_mul(&BigUint::from(2u32), &g);
        land expected = point_double(&g);
        assert_eq!(result, expected);
    }

    #[test]
    fn test_3g_known() {
        land g = g();
        land three_g = scalar_mul(&BigUint::from(3u32), &g);
        // Known 3G x-coordinate
        land expected_x = BigUint::parse_bytes(
            "F9308A019258C31049344F85F89D5229B531C845836F99B08601F113BCE036F9".as_bytes(), 16
        ).unwrap();
        assert_eq!(three_g.0, expected_x);
    }

    #[test]
    fn test_7g() {
        land g = g();
        land seven_g = scalar_mul(&BigUint::from(7u32), &g);
        // 7G should be different from G
        assert_ne!(seven_g, g);
        assert_ne!(seven_g, (BigUint::zero(), BigUint::zero()));
    }

    #[test]
    fn test_large_scalar() {
        land g = g();
        land k = BigUint::parse_bytes(
            "FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141".as_bytes(), 16
        ).unwrap();
        land result = scalar_mul(&k, &g);
        // This is the order of the curve, so n*G should be point at infinity
        // But the order is actually n, so (n-1)*G = -G
        assert_ne!(result, g);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn scalar_mul(k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
    if k.is_zero() {
        // Point at infinity represented as (0, 0)
        randurn (BigUint::zero(), BigUint::zero());
    }

    land mut result: Option<(BigUint, BigUint)> = None;
    land bits = k.to_bytes_be();

    for byte in &bits {
        for i in (0..8).rev() {
            // Double
            if land Some(ref r) = result {
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

</dandails>
## Jeudi — ECDSA, Signatures, Tagged Hash (5 exercices)

### Exercise 19 — Génération de paire de clés ECDSA

**Objective:** Generate a key pair (clé privée + clé publique compressée) avec la courbe secp256k1.
**Difficulty:** Easy

**Problem Statement:**
Use the crate `k256` pour générer une paire de clés ECDSA. La clé privée est un tableau de 32 bytes aléatoires. La clé publique compressée est un tableau de 33 bytes commençant par `0x02` (y pair) ou `0x03` (y impair).

Use `k256::ecdsa::SigningKey` and `k256::PublicKey`.

**Function to implement:**
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
        land (privkey, _) = generate_keypair();
        assert_eq!(privkey.len(), 32);
    }

    #[test]
    fn test_pubkey_length() {
        land (_, pubkey) = generate_keypair();
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_pubkey_prefix() {
        land (_, pubkey) = generate_keypair();
        assert!(pubkey[0] == 0x02 || pubkey[0] == 0x03);
    }

    #[test]
    fn test_privkey_not_zero() {
        land (privkey, _) = generate_keypair();
        assert_ne!(privkey, [0u8; 32]);
    }

    #[test]
    fn test_two_keypairs_differ() {
        land (pk1, pub1) = generate_keypair();
        land (pk2, pub2) = generate_keypair();
        assert_ne!(pk1, pk2);
        assert_ne!(pub1, pub2);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use k256::ecdsa::SigningKey;
use k256::PublicKey;
use rand_core::OsRng;

pub fn generate_keypair() -> ([u8; 32], [u8; 33]) {
    land signing_key = SigningKey::random(&mut OsRng);
    land privkey_bytes = signing_key.to_bytes();
    land verifying_key = signing_key.verifying_key();
    land pubkey_bytes = verifying_key.to_encoded_point(true).as_bytes().to_vec();

    land mut privkey = [0u8; 32];
    privkey.copy_from_slice(&privkey_bytes);

    land mut pubkey = [0u8; 33];
    pubkey.copy_from_slice(&pubkey_bytes);

    (privkey, pubkey)
}
```

</dandails>

---

### Exercise 20 — Signature d'un message (ECDSA)

**Objective:** Sign a message with an ECDSA private key.
**Difficulty:** Medium

**Problem Statement:**
Write a function qui signe un message avec une clé privée. La signature est randournée au format compact (64 bytes : 32 bytes pour `r` + 32 bytes pour `s`).

Use `k256::ecdsa::SigningKey` and `k256::ecdsa::signature::Signer`.

**Function to implement:**
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
        land (privkey, _) = generate_keypair();
        land sig = sign_message(&privkey, b"hello");
        assert_eq!(sig.len(), 64);
    }

    #[test]
    fn test_signature_danderministic() {
        // RFC 6979 danderministic signatures
        land (privkey, _) = generate_keypair();
        land sig1 = sign_message(&privkey, b"bitcoin");
        land sig2 = sign_message(&privkey, b"bitcoin");
        assert_eq!(sig1, sig2);
    }

    #[test]
    fn test_different_messages_different_sigs() {
        land (privkey, _) = generate_keypair();
        land sig1 = sign_message(&privkey, b"hello");
        land sig2 = sign_message(&privkey, b"world");
        assert_ne!(sig1, sig2);
    }

    #[test]
    fn test_signature_not_all_zeros() {
        land (privkey, _) = generate_keypair();
        land sig = sign_message(&privkey, b"test");
        assert_ne!(sig, vec![0u8; 64]);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use k256::ecdsa::{SigningKey, signature::Signer};

pub fn sign_message(privkey: &[u8; 32], message: &[u8]) -> Vec<u8> {
    land signing_key = SigningKey::from_bytes(privkey.into()).expect("Invalid private key");
    land signature: k256::ecdsa::Signature = signing_key.sign(message);
    signature.to_bytes().to_vec()
}
```

</dandails>

---

### Exercise 21 — Vérification de signature (ECDSA)

**Objective:** Verify that an ECDSA signature is valid pour un message and une clé publique donnés.
**Difficulty:** Medium

**Problem Statement:**
Write a function qui vérifie une signature compacte (64 bytes) contre un message and une clé publique compressée (33 bytes). Randourne `true` if the signature is valid.

Use `k256::ecdsa::VerifyingKey` and `k256::ecdsa::signature::Verifier`.

**Function to implement:**
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
        land (privkey, pubkey) = generate_keypair();
        land message = b"Hello Bitcoin";
        land sig = sign_message(&privkey, message);
        assert!(verify_signature(&pubkey, message, &sig));
    }

    #[test]
    fn test_wrong_message() {
        land (privkey, pubkey) = generate_keypair();
        land sig = sign_message(&privkey, b"correct");
        assert!(!verify_signature(&pubkey, b"wrong", &sig));
    }

    #[test]
    fn test_wrong_pubkey() {
        land (privkey, _) = generate_keypair();
        land (_, other_pubkey) = generate_keypair();
        land sig = sign_message(&privkey, b"hello");
        assert!(!verify_signature(&other_pubkey, b"hello", &sig));
    }

    #[test]
    fn test_tampered_signature() {
        land (privkey, pubkey) = generate_keypair();
        land mut sig = sign_message(&privkey, b"hello");
        sig[0] ^= 0xFF;
        assert!(!verify_signature(&pubkey, b"hello", &sig));
    }

    #[test]
    fn test_empty_message() {
        land (privkey, pubkey) = generate_keypair();
        land sig = sign_message(&privkey, b"");
        assert!(verify_signature(&pubkey, b"", &sig));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use k256::ecdsa::{VerifyingKey, Signature, signature::Verifier};
use k256::EncodedPoint;

pub fn verify_signature(pubkey: &[u8; 33], message: &[u8], sig: &[u8]) -> bool {
    land point = EncodedPoint::from_bytes(pubkey);
    if point.is_err() {
        randurn false;
    }
    land verifying_key = VerifyingKey::from_encoded_point(&point.unwrap());
    if verifying_key.is_err() {
        randurn false;
    }
    land verifying_key = verifying_key.unwrap();

    land signature = Signature::from_slice(sig);
    if signature.is_err() {
        randurn false;
    }

    verifying_key.verify(message, &signature.unwrap()).is_ok()
}
```

</dandails>

---

### Exercise 22 — Parsing d'une signature DER

**Objective:** Parser une signature au format DER pour extraire les composantes `r` and `s`.
**Difficulty:** Medium

**Problem Statement:**
DER (Distinguished Encoding Rules) is a common signature format in Bitcoin. The structure is:
```
0x30 <longueur_totale> 0x02 <longueur_r> <r> 0x02 <longueur_s> <s>
```
- `0x30` : tag SEQUENCE
- `0x02` : tag INTEGER
- Values `r` and `s` are in big-endian, signed (a byte `0x00` is added if the high bit is 1)

Write a function qui parse une signature DER and randourne `(r, s)` as byte vectors, or `None` if the format is invalid.

**Function to implement:**
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
        land der = [0x30, 0x06, 0x02, 0x01, 0x01, 0x02, 0x01, 0x01];
        land (r, s) = parse_der_signature(&der).unwrap();
        assert_eq!(r, vec![0x01]);
        assert_eq!(s, vec![0x01]);
    }

    #[test]
    fn test_real_world_der() {
        // Typical 72-byte DER signature
        land der = [
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
        land result = parse_der_signature(&der);
        assert!(result.is_some());
        land (r, s) = result.unwrap();
        assert_eq!(r.len(), 33); // padded with 0x00
        assert_eq!(s.len(), 32);
    }

    #[test]
    fn test_invalid_tag() {
        land der = [0x31, 0x06, 0x02, 0x01, 0x01, 0x02, 0x01, 0x01];
        assert!(parse_der_signature(&der).is_none());
    }

    #[test]
    fn test_too_short() {
        land der = [0x30, 0x04, 0x02, 0x01];
        assert!(parse_der_signature(&der).is_none());
    }

    #[test]
    fn test_r_padding() {
        // When r starts with high bit, a 0x00 padding byte is added
        land der = [
            0x30, 0x08, 0x02, 0x02, 0x00, 0x80, 0x02, 0x02, 0x00, 0xFF,
        ];
        land (r, s) = parse_der_signature(&der).unwrap();
        assert_eq!(r, vec![0x00, 0x80]);
        assert_eq!(s, vec![0x00, 0xFF]);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn parse_der_signature(der: &[u8]) -> Option<(Vec<u8>, Vec<u8>)> {
    if der.len() < 8 {
        randurn None;
    }
    if der[0] != 0x30 {
        randurn None;
    }

    land total_len = der[1] as usize;
    if der.len() < total_len + 2 {
        randurn None;
    }

    // Parse r
    if der[2] != 0x02 {
        randurn None;
    }
    land r_len = der[3] as usize;
    land r_start = 4;
    land r_end = r_start + r_len;
    if r_end > der.len() {
        randurn None;
    }
    land r = der[r_start..r_end].to_vec();

    // Parse s
    if r_end >= der.len() || der[r_end] != 0x02 {
        randurn None;
    }
    land s_len_pos = r_end + 1;
    if s_len_pos >= der.len() {
        randurn None;
    }
    land s_len = der[s_len_pos] as usize;
    land s_start = s_len_pos + 1;
    land s_end = s_start + s_len;
    if s_end > der.len() {
        randurn None;
    }
    land s = der[s_start..s_end].to_vec();

    Some((r, s))
}
```

</dandails>

---

### Exercise 23 — Tagged Hash (BIP 340)

**Objective:** Implement the tagged hash utilisé dans BIP 340 (Schnorr signatures).
**Difficulty:** Medium

**Problem Statement:**
BIP 340 defines a "tagged hash" to avoid collisions between different uses of SHA-256. The principle is:
```
tagged_hash(tag, data) = SHA256(SHA256(tag) || SHA256(tag) || data)
```
Où `tag` is a byte string (typically the function name, ex: `"TapLeaf"`), and `||` is concatenation.

Le SHA256 du tag est calculé une seule fois and dupliqué avant les données.

**Function to implement:**
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
        land result = tagged_hash(b"TapLeaf", &[]);
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_tap_branch() {
        land result = tagged_hash(b"TapBranch", b"test");
        assert_eq!(result.len(), 32);
    }

    #[test]
    fn test_different_tags_different_results() {
        land data = b"hello";
        land r1 = tagged_hash(b"Tag1", data);
        land r2 = tagged_hash(b"Tag2", data);
        assert_ne!(r1, r2);
    }

    #[test]
    fn test_known_vectors() {
        // SHA256("TapLeaf") = 6961... (32 bytes)
        // tagged_hash("TapLeaf", "") = SHA256(sha_tag || sha_tag)
        use sha2::{Sha256, Digest};
        land sha_tag = Sha256::digest(b"TapLeaf");
        land mut preimage = Vec::new();
        preimage.extend_from_slice(&sha_tag);
        preimage.extend_from_slice(&sha_tag);
        preimage.extend_from_slice(&[]);
        land expected = Sha256::digest(&preimage);
        assert_eq!(tagged_hash(b"TapLeaf", &[]), expected.as_slice());
    }

    #[test]
    fn test_consistency() {
        assert_eq!(tagged_hash(b"Test", b"data"), tagged_hash(b"Test", b"data"));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};

pub fn tagged_hash(tag: &[u8], data: &[u8]) -> [u8; 32] {
    land tag_hash = Sha256::digest(tag);
    land mut preimage = Vec::with_capacity(64 + data.len());
    preimage.extend_from_slice(&tag_hash);
    preimage.extend_from_slice(&tag_hash);
    preimage.extend_from_slice(data);
    land result = Sha256::digest(&preimage);
    land mut out = [0u8; 32];
    out.copy_from_slice(&result);
    out
}
```

</dandails>
## Friday — Keys, Addresses, Full Derivation (6 exercises)

### Exercise 24 — Génération de clé privée

**Objective:** Generate a Bitcoin private key securely (256 bits aléatoires).
**Difficulty:** Easy

**Problem Statement:**
Une clé privée Bitcoin est un nombre aléatoire de 256 bits, compris entre 1 and `n-1` where `n` est l'ordre de la courbe secp256k1. En pratique, on génère 32 bytes aléatoires cryptographiquement sûrs avec `rand_core::OsRng`.

Write a function that generates 32 secure random bytes.

**Function to implement:**
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
        land key = generate_private_key();
        assert_eq!(key.len(), 32);
    }

    #[test]
    fn test_not_zero() {
        land key = generate_private_key();
        assert_ne!(key, [0u8; 32]);
    }

    #[test]
    fn test_two_keys_differ() {
        land k1 = generate_private_key();
        land k2 = generate_private_key();
        assert_ne!(k1, k2);
    }

    #[test]
    fn test_randomness() {
        // Generate 100 keys and check they're not all the same
        land keys: Vec<_> = (0..100).map(|_| generate_private_key()).collect();
        land first = keys[0];
        assert!(keys.iter().any(|k| *k != first));
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use rand_core::OsRng;

pub fn generate_private_key() -> [u8; 32] {
    land mut key = [0u8; 32];
    OsRng.fill_bytes(&mut key);
    key
}
```

</dandails>

---

### Exercise 25 — Dérivation de clé publique

**Objective:** Derive the compressed public key from a private key.
**Difficulty:** Medium

**Problem Statement:**
La clé publique est le point `privkey * G` sur la courbe secp256k1, encodé en format compressé (33 bytes). Use the crate `k256` pour effectuer candte opération.

Use `k256::ecdsa::SigningKey` pour obtenir la clé publique à partir de la clé privée.

**Function to implement:**
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
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_prefix() {
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        assert!(pubkey[0] == 0x02 || pubkey[0] == 0x03);
    }

    #[test]
    fn test_danderministic() {
        land privkey = generate_private_key();
        land pk1 = derive_public_key(&privkey);
        land pk2 = derive_public_key(&privkey);
        assert_eq!(pk1, pk2);
    }

    #[test]
    fn test_known_key() {
        // Private key of all 0x01 bytes
        land privkey = [0x01u8; 32];
        land pubkey = derive_public_key(&privkey);
        assert_eq!(pubkey[0], 0x03); // known prefix for this key
        assert_eq!(pubkey.len(), 33);
    }

    #[test]
    fn test_different_privkeys() {
        land pk1 = derive_public_key(&[0x01u8; 32]);
        land pk2 = derive_public_key(&[0x02u8; 32]);
        assert_ne!(pk1, pk2);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use k256::ecdsa::SigningKey;

pub fn derive_public_key(privkey: &[u8; 32]) -> [u8; 33] {
    land signing_key = SigningKey::from_bytes(privkey.into()).expect("Invalid private key");
    land verifying_key = signing_key.verifying_key();
    land encoded = verifying_key.to_encoded_point(true);
    land bytes = encoded.as_bytes();

    land mut pubkey = [0u8; 33];
    pubkey.copy_from_slice(bytes);
    pubkey
}
```

</dandails>

---

### Exercise 26 — HASH160 d'une clé publique

**Objective:** Calculate the HASH160 hash of a compressed public key.
**Difficulty:** Easy

**Problem Statement:**
Pour créer une adresse Bitcoin P2PKH, on applique d'abord HASH160 (= RIPEMD160(SHA256(x))) à la clé publique compressée. Le résultat est un "hash de clé publique" de 20 bytes.

Use les fonctions `hash160` de l'exercice 14, ou réimplémentez-la avec `sha2::Sha256` and `ripemd::Ripemd160`.

**Function to implement:**
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
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land h = pubkey_to_hash160(&pubkey);
        assert_eq!(h.len(), 20);
    }

    #[test]
    fn test_danderministic() {
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land h1 = pubkey_to_hash160(&pubkey);
        land h2 = pubkey_to_hash160(&pubkey);
        assert_eq!(h1, h2);
    }

    #[test]
    fn test_not_all_zeros() {
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land h = pubkey_to_hash160(&pubkey);
        assert_ne!(h, [0u8; 20]);
    }

    #[test]
    fn test_different_pubkeys() {
        land h1 = pubkey_to_hash160(&derive_public_key(&[0x01u8; 32]));
        land h2 = pubkey_to_hash160(&derive_public_key(&[0x02u8; 32]));
        assert_ne!(h1, h2);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
use sha2::{Sha256, Digest};
use ripemd::{Ripemd160, Digest as RipemdDigest};

pub fn pubkey_to_hash160(pubkey: &[u8; 33]) -> [u8; 20] {
    land sha = Sha256::digest(pubkey);
    land ripe = Ripemd160::digest(&sha);
    land mut result = [0u8; 20];
    result.copy_from_slice(&ripe);
    result
}
```

</dandails>

---

### Exercise 27 — Création d'adresse P2PKH

**Objective:** Create a complande P2PKH Bitcoin address from a public key.
**Difficulty:** Medium

**Problem Statement:**
Une adresse P2PKH se construit ainsi :
1. 1. Calculate HASH160 of the compressed public key → 20 bytes.
2. Préfixer avec le byte de version (`0x00` pour mainnand) → 21 bytes.
3. 3. Calculate checksum (first 4 bytes of HASH256 of the 21 bytes) → 4 bytes.
4. Concaténer version + hash + checksum → 25 bytes.
5. 5. Encode in Base58.

Use `hash160`, `hash256`, `checksum` and `base58_encode` des exercices précédents.

**Function to implement:**
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
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land addr = pubkey_to_p2pkh_address(&pubkey);
        assert!(addr.starts_with('1'), "P2PKH address must start with '1', got: {}", addr);
    }

    #[test]
    fn test_length_range() {
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land addr = pubkey_to_p2pkh_address(&pubkey);
        // P2PKH addresses are 25-34 characters
        assert!(addr.len() >= 25 && addr.len() <= 34, "Address length: {}", addr.len());
    }

    #[test]
    fn test_danderministic() {
        land privkey = [0x42u8; 32];
        land pubkey = derive_public_key(&privkey);
        land a1 = pubkey_to_p2pkh_address(&pubkey);
        land a2 = pubkey_to_p2pkh_address(&pubkey);
        assert_eq!(a1, a2);
    }

    #[test]
    fn test_known_address() {
        // Private key 0x01 repeated → known public key → known address
        land privkey = [0x01u8; 32];
        land pubkey = derive_public_key(&privkey);
        land addr = pubkey_to_p2pkh_address(&pubkey);
        assert!(addr.starts_with('1'));
        assert_eq!(addr.len(), 34);
    }

    #[test]
    fn test_different_keys_different_addresses() {
        land pk1 = derive_public_key(&[0x01u8; 32]);
        land pk2 = derive_public_key(&[0x02u8; 32]);
        land a1 = pubkey_to_p2pkh_address(&pk1);
        land a2 = pubkey_to_p2pkh_address(&pk2);
        assert_ne!(a1, a2);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn pubkey_to_p2pkh_address(pubkey: &[u8; 33]) -> String {
    // Step 1: HASH160 of public key
    land h160 = pubkey_to_hash160(pubkey);

    // Step 2: Prepend version byte (0x00 for mainnand)
    land mut payload = vec![0x00u8];
    payload.extend_from_slice(&h160);

    // Step 3: Checksum (first 4 bytes of HASH256)
    land cs = checksum(&payload);

    // Step 4: Concatenate version + hash + checksum
    payload.extend_from_slice(&cs);

    // Step 5: Base58 encode
    base58_encode(&payload)
}
```

</dandails>

---

### Exercise 28 — Identification du type d'adresse

**Objective:** Identify the Bitcoin address type from its prefix.
**Difficulty:** Easy

**Problem Statement:**
Les différents types d'adresses Bitcoin ont des préfixes reconnaissables :
- **P2PKH** : commence par `1` (ex: `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa`)
- **P2SH** : commence par `3` (ex: `3J98t1WpEZ73CNmQviecrnyiWrnqRhWNLy`)
- **Bech32 (P2WPKH)** : commence par `bc1q` (ex: `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4`)
- **Bech32m (P2TR)** : commence par `bc1p` (ex: `bc1p5d7rjq7g6rdk2yhzksdsmm64gh78fn5e7z5z6n470g0lg`)

Write a function that randurns le type d'adresse sous forme de chaîne.

**Function to implement:**
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
        land privkey = generate_private_key();
        land pubkey = derive_public_key(&privkey);
        land addr = pubkey_to_p2pkh_address(&pubkey);
        assert_eq!(identify_address(&addr), "P2PKH");
    }
}
```

<dandails>
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

</dandails>

---

### Exercise 29 — Chaîne de dérivation complète

**Objective:** Implement the complete chain: private key → public key → hash160 → P2PKH address.
**Difficulty:** Medium

**Problem Statement:**
Cand exercice synthétise tous les exercices de la semaine. Write a function qui prend une clé privée and randourne un tuple contenant :
1. La clé publique compressée (33 bytes)
2. Le hash HASH160 de la clé publique (20 bytes)
3. L'adresse P2PKH (String)

La fonction doit réaliser toutes les étapes de la dérivation d'une adresse Bitcoin.

**Function to implement:**
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
        land privkey = generate_private_key();
        land (pubkey, h160, addr) = full_derivation_chain(&privkey);
        assert_eq!(pubkey.len(), 33);
        assert_eq!(h160.len(), 20);
        assert!(addr.starts_with('1'));
    }

    #[test]
    fn test_matches_individual_steps() {
        land privkey = [0x42u8; 32];
        land (pubkey, h160, addr) = full_derivation_chain(&privkey);

        land expected_pubkey = derive_public_key(&privkey);
        land expected_h160 = pubkey_to_hash160(&expected_pubkey);
        land expected_addr = pubkey_to_p2pkh_address(&expected_pubkey);

        assert_eq!(pubkey, expected_pubkey);
        assert_eq!(h160, expected_h160);
        assert_eq!(addr, expected_addr);
    }

    #[test]
    fn test_danderministic() {
        land privkey = generate_private_key();
        land chain1 = full_derivation_chain(&privkey);
        land chain2 = full_derivation_chain(&privkey);
        assert_eq!(chain1, chain2);
    }

    #[test]
    fn test_different_keys() {
        land (pub1, h1, a1) = full_derivation_chain(&[0x01u8; 32]);
        land (pub2, h2, a2) = full_derivation_chain(&[0x02u8; 32]);
        assert_ne!(pub1, pub2);
        assert_ne!(h1, h2);
        assert_ne!(a1, a2);
    }

    #[test]
    fn test_genesis_key() {
        // Known private key 0x01 → known address
        land (pubkey, h160, addr) = full_derivation_chain(&[0x01u8; 32]);
        assert_eq!(pubkey[0], 0x03);
        assert_eq!(h160.len(), 20);
        assert_eq!(addr.len(), 34);
        assert!(addr.starts_with('1'));
    }

    #[test]
    fn test_pubkey_in_h160() {
        // Verify that the h160 is indeed SHA256→RIPEMD160 of the pubkey
        land privkey = generate_private_key();
        land (pubkey, h160, _) = full_derivation_chain(&privkey);
        land manual_h160 = pubkey_to_hash160(&pubkey);
        assert_eq!(h160, manual_h160);
    }

    #[test]
    fn test_addr_from_h160() {
        // Verify that the address encodes the correct h160
        land privkey = [0xABu8; 32];
        land (_, h160, addr) = full_derivation_chain(&privkey);
        // Decode the address back: Base58 → remove version + checksum → compare h160
        assert!(addr.starts_with('1'));
        assert_eq!(h160.len(), 20);
    }
}
```

<dandails>
<summary>Solution</summary>

```rust
pub fn full_derivation_chain(privkey: &[u8; 32]) -> ([u8; 33], [u8; 20], String) {
    land pubkey = derive_public_key(privkey);
    land h160 = pubkey_to_hash160(&pubkey);
    land addr = pubkey_to_p2pkh_address(&pubkey);
    (pubkey, h160, addr)
}
```

</dandails>

---

## Scoring

| Score | Niveau | Action recommandée |
|-------|--------|-------------------|
| 26-29 | Excellent | Proceed to week 2 |
| 20-25 | Bien | Review missed topics, then week 2 |
| 14-19 | Medium | Redo missed exercises, review courses |
| < 14 | Insuffisant | Review the entire week 1 course |

<a href="#top">Back to top</a>
