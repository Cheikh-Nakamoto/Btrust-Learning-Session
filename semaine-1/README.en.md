> [![Français](https://img.shields.io/badge/Français-blue)](README.fr.md) [![English](https://img.shields.io/badge/English-blue)](README.en.md)


# Week 1 — Foundations

**Objective:** Understand the building blocks: encoding, cryptography, keys and addresses.
**Period:** May 25 - 29, 2026

**Rust prerequisites:**
```toml
# Cargo.toml
[dependencies]
sha2 = "0.10"
hex = "0.4"
rand = "0.8"
```

---

## Monday May 25 — Basic Encoding

### 1. Hexadecimal

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/hexadecimal/)

#### Theory

Hexadecimal is a **base-16** numbering system using 16 symbols: `0-9` and `A-F` (values 0 to 15). Case doesn't matter — `1337af` = `1337AF`.

**Why Bitcoin uses it:** A half-byte (nibble) has 16 combinations (2^4 = 16), mapping perfectly to one hex character. A full byte = 2 hex characters. All Bitcoin data (keys, hashes, transactions) is displayed in hex.

| Hex | Decimal | Binary |
|-----|---------|--------|
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

**Prefixes:** `0x` = hex, `0d` = decimal, `0b` = binary. Ex: `0x100` = 256.

#### Hex to decimal conversion

Multiply each character by increasing powers of 16 (right to left).

Example with `02A13B`:
```
B(11) × 16^0 =          11
3      × 16^1 =          48
1      × 16^2 =         256
A(10)  × 16^3 =      40,960
2      × 16^4 =     131,072
0      × 16^5 =           0
                  --------
Total               172,347
```

#### Decimal to hex conversion

Divide by 16, note remainders bottom to top.

Example with 6735:
```
6735 ÷ 16 = 420  remainder 15 → F
 420 ÷ 16 =  26  remainder  4 → 4
  26 ÷ 16 =   1  remainder 10 → A
   1 ÷ 16 =   0  remainder  1 → 1
                              → 1A4F
```

#### Rust Code

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

    // Using the hex crate (for bytes)
    let hex_str = "deadbeef";
    let bytes = hex::decode(hex_str).expect("Invalid hex");
    println!("{:?} → {:?}", hex_str, bytes);
    // "deadbeef" → [222, 173, 190, 239]

    let back = hex::encode(&bytes);
    println!("{:?} → {:?}", bytes, back);
    // [222, 173, 190, 239] → "deadbeef"
}
```

#### Quick exercise
Write a program that converts `0xCAFEBABE` to decimal. Expected result: 3405691582.

---

### 2. Bytes

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/bytes/)

#### Theory

A **bit** = a single binary value (0 or 1). A **byte** = 8 bits grouped together. It's the default unit of measurement for Bitcoin data.

- 2 hex characters = 1 byte = 8 bits
- A 32-byte private key = 256 bits

**Representation:** A byte can hold different types of data depending on context:

| Type | Bitcoin Example | Size |
|------|----------------|------|
| Number (integer) | Private key, amount, nonce | 4-32 bytes |
| Text (ASCII) | OP_RETURN, coinbase message | Variable |
| Bit field | Node services, block version | 4-8 bytes |
| Unique identifier | TXID, Block Hash, Public Key Hash | 32 bytes |
| Arbitrary data | Magic bytes (`f9beb4d9`) | 4 bytes |

**Important:** The same byte can mean different things — `01100001` = the letter "g" (ASCII) or the number 103 (integer). It all depends on context.

#### Bits in detail

```
Byte: 1 0 1 1 0 1 0 1
Bit:  7 6 5 4 3 2 1 0  (position, decreasing weight)

Value = 1×2^7 + 0×2^6 + 1×2^5 + 1×2^4 + 0×2^3 + 1×2^2 + 0×2^1 + 1×2^0
      = 128  +  0   +  32  +  16  +  0   +  4   +  0   +  1
      = 181
```

#### Rust Code

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
    // One byte = 8 bits
    let byte: u8 = 0b10110101;
    println!("0b10110101 = {} (decimal) = 0x{:02X} (hex)", byte, byte);
    // 0b10110101 = 181 (decimal) = 0xB5 (hex)

    // Display the bits of a byte
    println!("Bits of 181: {}", byte_to_bits(181));
    // Bits of 181: 10110101

    // ASCII: same byte, different context
    let byte_ascii: u8 = 0b01100001; // 97
    println!("97 = '{}'", byte_ascii as char); // 97 = 'a'

    // Type conversions
    let n: u32 = 1000;
    let bytes = n.to_le_bytes(); // little-endian
    println!("1000 in LE: {:02x?}", bytes); // [e8, 03, 00, 00]

    let bytes_be = n.to_be_bytes(); // big-endian
    println!("1000 in BE: {:02x?}", bytes_be); // [00, 00, 03, e8]
}
```

#### Quick exercise
Write a program that displays the bits of each byte of the string `"Bitcoin"`.
Hint: `"Bitcoin".as_bytes()` returns `&[u8]`.

---

### 3. Little Endian

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/little-endian/)

#### Theory

Little-endian is a byte ordering convention where the **least significant byte comes first** — bytes appear "backwards" compared to human reading. It's the standard byte order for almost all integers in raw Bitcoin data.

**Big-Endian** (human-readable): `00 00 00 00 00 bc 61 4e`
**Little-Endian** (used by Bitcoin): `4e 61 bc 00 00 00 00 00`

**Origin of the term:** Jonathan Swift, *Gulliver's Travels* (1726) — two factions dispute which end of an egg to break.

**Where it appears in Bitcoin:**
- Transaction `version` (ex: `02000000`)
- Input `vout` (ex: `01000000` = output #1)
- Output `amount` (ex: `2a5f020000000000`)
- Block header `time`, `bits`, `nonce`

**Why Bitcoin uses it:** Satoshi was developing on a little-endian processor. Most modern CPUs natively operate in little-endian.

**Conversion:** Reverse byte order (not character order). `6fe2` reversed = `e26f`, not `2ef6`.

#### Visualization

```
Number: 305,419,896  (0x12345678)

Big-Endian (BE):    12 34 56 78   ← natural reading
Little-Endian (LE): 78 56 34 12   ← reversed order
                     ↑
              least significant byte first
```

#### Rust Code

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
    // LE ↔ BE conversion
    let be = "0000000000bc614e";
    let le = swap_endian(be);
    println!("BE → LE: {} → {}", be, le);
    // BE → LE: 0000000000bc614e → 4e61bc0000000000

    // Native Rust usage
    let n: u32 = 0x12345678;
    println!("Number:   0x{:08X}", n);

    let be_bytes = n.to_be_bytes();
    let le_bytes = n.to_le_bytes();
    println!("BE bytes: {:02x?}", be_bytes); // [12, 34, 56, 78]
    println!("LE bytes: {:02x?}", le_bytes); // [78, 56, 34, 12]

    // Bitcoin case: transaction "version" field
    let version_le: [u8; 4] = [0x02, 0x00, 0x00, 0x00];
    let version = u32::from_le_bytes(version_le);
    println!("Transaction version: {}", version); // 2

    // Bitcoin case: input "vout" field
    let vout_le: [u8; 4] = [0x01, 0x00, 0x00, 0x00];
    let vout = u32::from_le_bytes(vout_le);
    println!("Vout: {}", vout); // 1 (output #1)

    // Bitcoin case: amount (in satoshis)
    let amount_le: [u8; 8] = [0x2a, 0x5f, 0x02, 0x00, 0x00, 0x00, 0x00, 0x00];
    let amount = u64::from_le_bytes(amount_le);
    println!("Amount: {} satoshis", amount); // 155434 satoshis
    println!("Amount: {} BTC", amount as f64 / 100_000_000.0); // 0.00155434 BTC
}
```

#### Quick exercise
Write a program that reads the `nonce` field of a block header in little-endian.
The raw nonce (LE) is: `[0x3a, 0x1f, 0x01, 0x00]`. What is the decimal value?

---

## Monday Summary

| Concept | Key Point | Rust Code |
|---------|-----------|-----------|
| **Hex** | 2 hex chars = 1 byte, base 16 | `hex::decode()`, `hex::encode()` |
| **Bytes** | 8 bits, fundamental unit | `u8`, `.to_le_bytes()`, `.to_be_bytes()` |
| **Little-Endian** | Low byte first (Bitcoin) | `u32::from_le_bytes()`, `u64::from_le_bytes()` |

---

## Tuesday May 26 — Advanced Encoding

### 4. Byte Order (hash byte ordering)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/byte-order/)

#### Theory

Bitcoin uses **two distinct byte orders** for transaction hashes (TXIDs) and block hashes:

| Context | Order | Usage |
|---------|-------|-------|
| Raw data (protocol) | Natural (hash output) | Internal references: previous block, TXID in inputs |
| RPC, explorers, display | Reversed (bytes flipped) | Search and display of hashes |

**Example — genesis block hash:**
- Natural: `6fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000`
- Reversed: `000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f`

**Why reverse?** Satoshi interpreted block hashes as **integers** to compare against the target during mining. In reversed format, leading zeros are on the left, making the comparison visually intuitive.

**Beware:** TXIDs and block hashes are reversed for display, but hashes in raw data are not. You must reverse byte order to search for a hash on an explorer.

#### Rust Code

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
    // Simulate a block hash (genesis block hash)
    let genesis_hash_natural = "6fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000";
    let genesis_hash_display = reverse_hex(genesis_hash_natural);

    println!("Natural : {}", genesis_hash_natural);
    println!("Reversed: {}", genesis_hash_display);
    // Reversed: 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f

    // Verify: reversed hash starts with zeros (proof of work)
    assert!(genesis_hash_display.starts_with("0000000000"));

    // Hash data and reverse for display
    let data = b"Hello Bitcoin";
    let hash = hash256(data);
    let hash_hex = hex::encode(&hash);
    let hash_display = reverse_hex(&hash_hex);

    println!("\nData: {:?}", std::str::from_utf8(data).unwrap());
    println!("Hash: {}", hash_hex);
    println!("Display: {}", hash_display);
}
```

#### Practical case: searching a TXID on an explorer

```rust
fn main() {
    // TXID in raw transaction data
    let txid_raw = "c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704";

    // To search on an explorer, reverse the bytes
    let txid_display = reverse_hex(txid_raw);
    println!("TXID (explorer): {}", txid_display);
    // 0437cd7f8525ceed2324359c2d0ba26006d92d856a9c20fa0241106ee5a597c9
}
```

---

### 5. CompactSize (variable-length integers)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/general/compact-size/)

#### Theory

A CompactSize field encodes variable-length integers. The first byte (prefix) determines the structure:

| Prefix | Meaning | Value Range | Total Size |
|--------|---------|-------------|------------|
| `00`-`FC` | The byte itself is the value | 0 - 252 | 1 byte |
| `FD` | Next 2 bytes (little-endian) | 253 - 65,535 | 3 bytes |
| `FE` | Next 4 bytes (little-endian) | 65,536 - 4,294,967,295 | 5 bytes |
| `FF` | Next 8 bytes (little-endian) | 4,294,967,296 - 2^64-1 | 9 bytes |

**Examples:**

| Decimal | CompactSize Hex |
|---------|-----------------|
| 0 | `00` |
| 100 | `64` |
| 252 | `fc` |
| 253 | `fdfd00` |
| 1,000 | `fde803` |
| 65,536 | `fe00000100` |

**Where it appears:**
- Number of inputs/outputs in a transaction
- ScriptSig / ScriptPubKey size
- Number of elements in the witness stack
- Number of transactions in a block

**Benefit:** Space savings — 1 byte instead of 2 fixed for the most common small values.

**Difference from VarInt:** CompactSize is used in Bitcoin network messages. VarInt is used in LevelDB's Chainstate. BIP 141 calls CompactSize `var_int`, which causes confusion.

#### Rust Code: Encode and Decode

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
    // Encode
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

    // Decode
    let test_bytes = hex::decode("fdfd00").unwrap();
    let (value, size) = decode_compact_size(&test_bytes);
    println!("\nfdfd00 → value={}, size={} bytes", value, size);
    // fdfd00 → value=253, size=3 bytes

    // Practical case: number of inputs in a transaction
    let tx_data: &[u8] = &[0x02]; // 2 inputs
    let (num_inputs, _) = decode_compact_size(tx_data);
    println!("\nNumber of inputs: {}", num_inputs); // 2
}
```

---

### 6. Base58

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/base58/)

#### Theory

Base58 is a **base-58** system created by Satoshi for human-readable Bitcoin data representation.

**Character set:**
```
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz
```
58 characters: 10 digits + 25 uppercase + 23 lowercase. Characters `0`, `O`, `I`, `l` are excluded as visually ambiguous.

**Encoding:** Repeated modulo division by 58.

**Leading zeros:** Each `0x00` byte in input is converted to a leading `1` in Base58 output (to preserve length).

**Version prefixes:**

| Hex Prefix | Leading Char | Usage | Example |
|------------|-------------|-------|---------|
| `00` | 1 | P2PKH Address | `1AKDDsfTh8uY4X3ppy1m7jw1fVMBSMkzjP` |
| `05` | 3 | P2SH Address | `34nSkinWC9rDDJiUY438qQN1JHmGqBHGW7` |
| `80` | K, L, or 5 | WIF Private Key | `L4mee2GrpBSckB9SgC9WhHxvtEgKUvgvTiy...` |
| `0488ADE4` | xprv | Extended Private Key | `xprv9tuogRdb5YTgcL3P8Waj7REqDuQx4sXc...` |
| `0488B21E` | xpub | Extended Public Key | `xpub67uA5wAUuv1ypp7rEY7jUZBZmwFSULFU...` |

**Base58Check** = data + checksum (first 4 bytes of SHA256(SHA256(data))) + Base58 encoding. The checksum detects input errors (1 in 4.3 billion false positive chance).

#### Rust Code

```rust
const BASE58_ALPHABET: &[u8; 58] = b"123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz";

fn base58_encode(data: &[u8]) -> String {
    // Count leading zeros
    let leading_zeros = data.iter().take_while(|&&b| b == 0).count();

    // Convert to base58 by modulo division
    let mut num = num_bigint::BigUint::from_bytes_be(data);
    let base = num_bigint::BigUint::from(58u32);
    let mut result = Vec::new();

    while num > 0 {
        let remainder = (&num % &base).to_u32_digits().first().copied().unwrap_or(0) as usize;
        num /= &base;
        result.push(BASE58_ALPHABET[remainder]);
    }

    // Add '1' for leading zeros
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
    // Encode data to Base58
    let data = hex::decode("000102030405").unwrap();
    let encoded = base58_encode(&data);
    println!("Base58: {}", encoded); // 16Ho7Hs

    // Decode
    let decoded = base58_decode("16Ho7Hs");
    println!("Hex: {}", hex::encode(&decoded));

    // Leading zeros
    let data_with_zeros = hex::decode("000000abcdef").unwrap();
    let encoded = base58_encode(&data_with_zeros);
    println!("With zeros: {}", encoded); // 111Rmg...
    // Note: 3 leading zeros → 3 leading '1's
}
```

---

### 7. Bech32

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/bech32/)

#### Theory

Bech32 is the address format for SegWit scripts, designed by Pieter Wuille. It uses a **32-character lowercase set** (case-insensitive) with an improved checksum.

**Structure:** `bc1q` + encoded data

| Type | Prefix | Length | Encoding | Example |
|------|--------|--------|----------|---------|
| P2WPKH | `bc1q` | 42 chars | Bech32 | `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4` |
| P2WSH | `bc1q` | 62 chars | Bech32 | `bc1qrp33g0q5c5txsp9arysrx4k6zdkfs4nce4xj0gdcccefvpysxf3qccfmv3` |
| P2TR | `bc1p` | 62 chars | Bech32m | `bc1p5d7rjq7g6rdk2yhzksdssmvp9afg6hv6l2j235...` |

**Bech32 character set:**
```
qpzry9x8gf2tvdw0s3jn54khce6mua7l
```

**Advantages over Base58:**
- Case-insensitive (all lowercase) — easier to type and communicate
- Improved checksum — better error detection
- Encodes the full ScriptPubKey (not just the hash)

**Bech32 vs Bech32m:** The checksum calculation differs. Bech32 is used for SegWit v0 (P2WPKH, P2WSH). Bech32m is used for SegWit v1+ (P2TR/Taproot). The change fixes a weakness: inserting/deleting `q`s before a final `p` wasn't detected.

#### Rust Code (using bech32 crate)

```rust
use bech32::{self, FromBase32, ToBase32, Variant};

fn main() {
    // Decode a Bech32 address
    let addr = "bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4";
    let (hrp, data, variant) = bech32::decode(addr).expect("Invalid bech32");
    let payload = Vec::<u8>::from_base32(&data).expect("Invalid base32");

    println!("HRP:     {}", hrp);                    // bc
    println!("Variant: {:?}", variant);              // Bech32
    println!("Payload: {} bytes", payload.len());    // 20 bytes (P2WPKH)
    println!("Payload: {}", hex::encode(&payload));

    // Encode a Bech32 address
    let witness_program = hex::decode("751e76e8199196d454941c45d1b3a323f1433bd6").unwrap();
    let encoded = bech32::encode("bc", witness_program.to_base32(), Variant::Bech32).unwrap();
    println!("\nAddress: {}", encoded);
    // bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4

    // Identify address type
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
            _ => "Unknown",
        };
        println!("{} → {} ({} bytes)", addr, type_name, payload.len());
    }
}
```

---

### 8. Checksum

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/checksum/)

#### Theory

A checksum is a small piece of data allowing you to verify the **integrity** of other data. It detects errors but doesn't correct them.

**Creation:** First 4 bytes of `SHA256(SHA256(data))`.

```
data     = aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
hash256  = 05c4de7c1069e9de703efd172e58c1919f48ae03910277a49c9afd7ded58bbeb
checksum = 05c4de7c
```

**Where it appears:**
- Base58 addresses (P2PKH, P2SH)
- WIF private keys
- Extended keys (xpub, xprv)
- Network messages (every message between nodes)

**Reliability:** 4 bytes = 2^32 = 4,294,967,296 possible values. False positive probability ~1 in 4.3 billion.

#### Rust Code

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
    // Calculate a checksum
    let data = hex::decode("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa").unwrap();
    let cs = checksum(&data);
    println!("Data: {}", hex::encode(&data));
    println!("Checksum: {}", hex::encode(&cs));
    // Checksum: 05c4de7c

    // Verify a checksum
    let data = hex::decode("deadbeef12345678").unwrap();
    let cs = checksum(&data);
    println!("\nData: {}", hex::encode(&data));
    println!("Calculated checksum: {}", hex::encode(&cs));

    // Build a complete Base58Check address
    let version: u8 = 0x00; // P2PKH mainnet
    let pubkey_hash = hex::decode("010966776006953d5567439e5e39f86a0d273bee").unwrap();

    let mut payload = vec![version];
    payload.extend_from_slice(&pubkey_hash);
    let cs = checksum(&payload);

    let mut full = payload.clone();
    full.extend_from_slice(&cs);

    println!("\nVersion + Hash: {}", hex::encode(&payload));
    println!("Checksum:        {}", hex::encode(&cs));
    println!("Full:            {}", hex::encode(&full));
    // Base58Check of this gives: 16UwLL9Risc3QfPqBUvKofHmBQ7wMtjvM

    // Verification
    let received = &full[..full.len()-4];
    let received_cs: [u8; 4] = full[full.len()-4..].try_into().unwrap();
    println!("\nVerification: {}", verify_checksum(received, &received_cs)); // true
}
```

---

## Tuesday Summary

| Concept | Key Point | Rust Code |
|---------|-----------|-----------|
| **Byte Order** | Hashes reversed for display | `reverse_hex()`, `from_le_bytes()` |
| **CompactSize** | 1/3/5/9 bytes depending on value | `encode_compact_size()`, `decode_compact_size()` |
| **Base58** | 58 chars, built-in checksum | `base58_encode()`, `base58_decode()` |
| **Bech32** | SegWit, lowercase, Bech32m for Taproot | `bech32::decode()`, `bech32::encode()` |
| **Checksum** | First 4 bytes of HASH256 | `checksum()`, `verify_checksum()` |

---

## Wednesday May 27 — Cryptography

### 9. Hash Function

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/hash-function/)

#### Theory

A hash function creates **digital fingerprints** of data. It takes arbitrary-length input and produces fixed-length output.

**Fundamental properties:**

| Property | Description | Example |
|----------|-------------|---------|
| **Deterministic** | Same input → same output | Always the same hash |
| **Fixed-length output** | SHA-256 = always 32 bytes | Regardless of input size |
| **Avalanche effect** | One bit changed → completely different hash | `"abc"` vs `"abd"` → totally different hashes |
| **One-way** | Impossible to recover input from hash | No known inverse algorithm |
| **Collision resistance** | Impossible to find two inputs with same hash | ~2^128 operations for SHA-256 |

**Functions used in Bitcoin:**

| Function | Formula | Usage | Output size |
|----------|---------|-------|-------------|
| **HASH256** | SHA256(SHA256(data)) | Blocks, TXID, Merkle root, checksums | 32 bytes |
| **HASH160** | RIPEMD160(SHA256(data)) | Public key hash, legacy addresses | 20 bytes |
| **SHA256** | SHA256(data) | P2WSH script hash (SegWit) | 32 bytes |
| **HMAC-SHA512** | HMAC(SHA512, key, data) | HD extended key derivation | 64 bytes |
| **PBKDF2** | 2048 iterations HMAC-SHA512 | Mnemonic → HD seed | 64 bytes |

**Usage in Bitcoin:**
- **Mining:** Block hash < target → valid block
- **Blockchain integrity:** Each block references the previous block's hash
- **TXID:** Unique transaction identifier
- **Merkle Root:** Fingerprint of all TXIDs in the block
- **Checksums:** First 4 bytes of a hash for error detection

**Common mistake:** Hashing the ASCII codes of a hex string instead of the actual bytes. In Rust, you need `hex::decode(hex)` before hashing.

#### Rust Code: HASH256 and HASH160

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
    // HASH256: double SHA-256
    let data = b"Hello Bitcoin";
    let h = hash256(data);
    println!("HASH256: {}", hex::encode(&h));
    // 32 bytes = 64 hex characters

    // HASH160: SHA-256 then RIPEMD-160
    let h = hash160(data);
    println!("HASH160: {}", hex::encode(&h));
    // 20 bytes = 40 hex characters

    // COMMON MISTAKE: hashing ASCII codes instead of bytes
    let hex_str = "ab";
    // WRONG: hashing the bytes of the string "ab" (= [0x61, 0x62])
    let wrong = hash256(hex_str.as_bytes());
    println!("\nWRONG:   {}", hex::encode(&wrong));

    // CORRECT: decode hex then hash
    let correct = hash256(&hex::decode(hex_str).unwrap());
    println!("CORRECT: {}", hex::encode(&correct));
    // Completely different results!

    // Demonstrate avalanche effect
    let a = hash256(b"abc");
    let b = hash256(b"abd");
    println!("\nabc → {}", hex::encode(&a));
    println!("abd → {}", hex::encode(&b));
    // One bit changed → totally different hash
}
```

#### Rust: dependencies for ripemd160

```toml
# Cargo.toml
[dependencies]
sha2 = "0.10"
hex = "0.4"
ripemd = "0.1"
```

---

### 10. Elliptic Curve (secp256k1)

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/)

#### Theory

An elliptic curve is a set of points satisfying the equation **y^2 = x^3 + ax + b**. It provides a **one-way function**: you can move forward (multiplication) but not backward (discrete logarithm).

**Bitcoin uses the secp256k1 curve:**

| Parameter | Value | Description |
|-----------|-------|-------------|
| **a** | 0 | Coefficient |
| **b** | 7 | Coefficient |
| **p** | 2^256 - 2^32 - 2^9 - 2^8 - 2^7 - 2^6 - 2^4 - 1 | Prime modulus (finite field) |
| **n** | 115792089237316195423570985008687907852837564279074904382605163141518161494337 | Order (number of points) |

**Generator point (G):**
```
x = 55066263022277343669578718895168534326250603453777594175500187360389116729240
y = 32670510020758816978083085130507043184471273380659243275938904335757337482424
```

**Finite field:** Operations are done modulo p (a prime number), turning the smooth curve into a discrete scatter plot. The mathematics remain identical.

#### Fundamental operations

**1. Point addition:**
```
Given P1(x1, y1) and P2(x2, y2) on the curve.

If P1 ≠ P2:
    s = (y2 - y1) × (x2 - x1)^(-1) mod p    (slope)
    x3 = s^2 - x1 - x2 mod p
    y3 = s(x1 - x3) - y1 mod p

If P1 = P2 (doubling):
    s = (3x1^2 + a) × (2y1)^(-1) mod p
    x3 = s^2 - 2x1 mod p
    y3 = s(x1 - x3) - y1 mod p
```

**2. Scalar multiplication (double-and-add):**
```
k × P = P + P + ... + P (k times)

Optimization: double-and-add
Example: 21 × P
21 in binary = 10101

Bit 1: R = P
Bit 0: R = 2R          (double)
Bit 1: R = 2R + P      (double + add)
Bit 0: R = 2R          (double)
Bit 1: R = 2R + P      (double + add)

4 operations instead of 20 additions
```

**3. Public key derivation:**
```
Public Key Q = k × G
where k = private key (number), G = generator point

Reverse (recovering k from Q):
→ Elliptic Curve Discrete Logarithm Problem (ECDLP)
→ Computationally infeasible (~2^256 operations)
```

#### Rust Code: elliptic curve operations (simplified)

```rust
use num_bigint::BigUint;
use num_traits::{Zero, One};

// secp256k1 parameters
struct Secp256k1 {
    p: BigUint,  // prime modulus
    a: BigUint,  // coefficient (0)
    b: BigUint,  // coefficient (7)
    n: BigUint,  // order
    g: (BigUint, BigUint), // generator point
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

    // Modular inverse: a^(-1) mod p
    fn mod_inverse(a: &BigUint, p: &BigUint) -> BigUint {
        // Uses Fermat's little theorem: a^(p-2) mod p
        a.modpow(&(p - BigUint::from(2u32)), p)
    }

    // Add two points
    fn point_add(&self, p1: &(BigUint, BigUint), p2: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let (x1, y1) = p1;
        let (x2, y2) = p2;

        if x1 == x2 && y1 == y2 {
            return self.point_double(p1);
        }

        // Slope: s = (y2 - y1) * (x2 - x1)^(-1) mod p
        let dy = if y2 >= y1 { y2 - y1 } else { &self.p - (y1 - y2) };
        let dx = if x2 >= x1 { x2 - x1 } else { &self.p - (x1 - x2) };
        let dx_inv = Self::mod_inverse(&dx, &self.p);
        let s = (&dy * &dx_inv) % &self.p;

        // x3 = s^2 - x1 - x2 mod p
        let x3 = (&s * &s + &self.p - x1 + &self.p - x2) % &self.p;
        // y3 = s(x1 - x3) - y1 mod p
        let y3 = (&s * (if x1 >= &x3 { x1 - &x3 } else { &self.p - (&x3 - x1) }) + &self.p - y1) % &self.p;

        (x3, y3)
    }

    // Double a point
    fn point_double(&self, p1: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let (x1, y1) = p1;

        // Slope: s = (3x1^2 + a) * (2y1)^(-1) mod p
        let three_x2 = (BigUint::from(3u32) * x1 * x1) % &self.p;
        let two_y = (BigUint::from(2u32) * y1) % &self.p;
        let two_y_inv = Self::mod_inverse(&two_y, &self.p);
        let s = (&three_x2 * &two_y_inv) % &self.p;

        // x3 = s^2 - 2x1 mod p
        let x3 = (&s * &s + &self.p - (BigUint::from(2u32) * x1)) % &self.p;
        // y3 = s(x1 - x3) - y1 mod p
        let y3 = (&s * (if x1 >= &x3 { x1 - &x3 } else { &self.p - (&x3 - x1) }) + &self.p - y1) % &self.p;

        (x3, y3)
    }

    // Scalar multiplication: k × P (double-and-add)
    fn scalar_mul(&self, k: &BigUint, point: &(BigUint, BigUint)) -> (BigUint, BigUint) {
        let mut result = (BigUint::zero(), BigUint::zero()); // point at infinity
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

    // Public key derivation: Q = k × G
    let private_key = BigUint::from(7u32); // example private key
    let public_key = curve.scalar_mul(&private_key, &curve.g);

    println!("Private key: {}", private_key);
    println!("Public key:");
    println!("  x = {}", public_key.0);
    println!("  y = {}", public_key.1);

    // Verify the point is on the curve: y^2 = x^3 + 7 mod p
    let y2 = (&public_key.1 * &public_key.1) % &curve.p;
    let x3_7 = (&public_key.0 * &public_key.0 * &public_key.0 + &curve.b) % &curve.p;
    println!("\nOn curve? {}", y2 == x3_7); // true

    // Demonstrate the one-way function
    println!("\n--- One-way function ---");
    println!("k → Q = k×G: EASY (scalar multiplication)");
    println!("Q → k       : INFEASIBLE (discrete logarithm)");
    println!("With ~2^256 possible keys, brute force would take");
    println!("longer than the age of the universe.");
}
```

#### Cargo.toml dependencies

```toml
[dependencies]
num-bigint = "0.4"
num-traits = "0.2"
```

---

## Wednesday Summary

| Concept | Key Point | Rust Code |
|---------|-----------|-----------|
| **HASH256** | Double SHA-256 (blocks, TXID) | `Sha256::digest` twice |
| **HASH160** | SHA-256 then RIPEMD-160 (addresses) | `Sha256` then `Ripemd160` |
| **Avalanche effect** | 1 bit changed → totally different hash | Compare `hash256(b"abc")` vs `hash256(b"abd")` |
| **secp256k1** | y^2 = x^3 + 7, a=0, b=7, prime modulus p | Modular operations on points |
| **Double-and-add** | O(log k) instead of O(k) | `scalar_mul()` with bits of k |
| **ECDLP** | k×G easy, Q→k infeasible | Bitcoin's security relies on this |

---

## Thursday May 28 — Digital Signatures

### 11. ECDSA

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/ecdsa/)

#### Theory

ECDSA (Elliptic Curve Digital Signature Algorithm) is the signature system used in Bitcoin to prove ownership of bitcoins.

**Key generation:**
- **Private key (d):** a large random number in [0..n-1]
- **Public key (Q):** `Q = d × G`

**Signing:** Requires 3 inputs:
1. **Random nonce k:** ensures uniqueness of each signature
2. **Message hash z:** SHA256 of data to sign
3. **Private key d**

Signature = **[r, s]** where:
- `r` = x-coordinate of point R (R = k × G), reduced mod n
- `s` = k^(-1) × (z + r × d) mod n

**Nonce security:** If the same nonce k is used for two different signatures, the private key is recoverable:
```
k = (z1 - z2) × (s1 - s2)^(-1) mod n
d = (k × s1 - z1) × r^(-1) mod n
```
Exploited in 2011 against the PS3 (Sony had reused k).

**Verification:** Calculate two points and add them:
- Point 1: G × (s^(-1) × z)
- Point 2: Q × (s^(-1) × r)
- If x of result mod n = r → signature valid

**DER encoding:** Bitcoin requires signatures in DER format:
```
30 [length] 02 [length_r] [r] 02 [length_s] [s] [sighash_type]
```
Typically 70-72 bytes + 1 sighash byte (`01` for SIGHASH_ALL).

**Low-S rule (BIP 62):** If s > n/2, replace with n - s. Prevents transaction malleability.

#### Rust Code: ECDSA signing and verification

```rust
use k256::ecdsa::{SigningKey, VerifyingKey, signature::{Signer, Verifier}};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Generate a key pair
    let signing_key = SigningKey::random(&mut OsRng);
    let verifying_key = VerifyingKey::from(&signing_key);

    println!("Private key: {}", hex::encode(signing_key.to_bytes()));
    println!("Public key: {}", hex::encode(verifying_key.to_encoded_point(false).as_bytes()));

    // Sign a message
    let message = b"Transaction: Alice -> Bob: 0.5 BTC";
    let signature: k256::ecdsa::Signature = signing_key.sign(message);

    println!("\nMessage: {:?}", std::str::from_utf8(message).unwrap());
    println!("Signature: {}", hex::encode(signature.to_bytes()));

    // Verify the signature
    let is_valid = verifying_key.verify(message, &signature).is_ok();
    println!("Signature valid? {}", is_valid); // true

    // Verify with a modified message
    let tampered = b"Transaction: Alice -> Bob: 50 BTC";
    let is_valid = verifying_key.verify(tampered, &signature).is_ok();
    println!("Modified message valid? {}", is_valid); // false
}
```

#### Rust Code: ECDSA signature details

```rust
use k256::ecdsa::{SigningKey, signature::Signer};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Create a private key from bytes
    let private_key_bytes = hex::decode(
        "0000000000000000000000000000000000000000000000000000000000000001"
    ).unwrap();
    let signing_key = SigningKey::from_bytes(&private_key_bytes.into()).unwrap();

    // Sign
    let msg = b"test";
    let sig: k256::ecdsa::Signature = signing_key.sign(msg);

    // DER format of the signature
    let der_bytes = sig.to_bytes();
    println!("Signature ({} bytes): {}", der_bytes.len(), hex::encode(&der_bytes));

    // Deterministic nonce (RFC 6979)
    // k is derived from private key and message → same message gives same k
    // This avoids the Sony problem (reused nonce)
    println!("\nNonce security:");
    println!("- RFC 6979: deterministic nonce based on (key, message)");
    println!("- Same message → same nonce → same signature");
    println!("- Different messages → different nonces");
    println!("- Nonce is never reused");
}
```

#### DER encoding in detail

```rust
fn parse_der_signature(der: &[u8]) -> Option<(&[u8], &[u8])> {
    if der.len() < 8 || der[0] != 0x30 {
        return None; // Not a SEQUENCE
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
    // Real DER example
    let der = hex::decode(
        "3044022047ac8e878352d3ebbde1c94ce3a10d057c24175747116f8288e5d794d12d482f0220217f36a485cae903c713331d877c1f64677e3622ad4010726870540656fe9dcb"
    ).unwrap();

    match parse_der_signature(&der) {
        Some((r, s)) => {
            println!("Parsed DER signature:");
            println!("r = {} ({} bytes)", hex::encode(r), r.len());
            println!("s = {} ({} bytes)", hex::encode(s), s.len());
        }
        None => println!("Invalid DER signature"),
    }
}
```

#### Dependencies

```toml
[dependencies]
k256 = { version = "0.13", features = ["ecdsa"] }
hex = "0.4"
rand_core = "0.6"
```

---

### 12. Schnorr Signatures

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/cryptography/elliptic-curve/schnorr/)

#### Theory

Schnorr is a signature scheme added by the Taproot upgrade in 2021 (BIP 340). Same secp256k1 curve as ECDSA, but with significant advantages.

**Key differences from ECDSA:**

| Aspect | ECDSA | Schnorr |
|--------|-------|---------|
| Equation | `s = k^(-1)(z + r·d)` (non-linear) | `s = (k + e·d)` (linear) |
| Size | 70-72 bytes (DER) | 64 bytes (fixed) |
| Public key | 33 bytes (compressed) | 32 bytes (x-only) |
| Signature aggregation | No | Yes |
| Batch verification | No | Yes |
| Security proof | Absent | Proven (random oracle model) |
| Malleability | Yes (before SegWit) | No |

**Signing process (BIP 340):**
1. Calculate P = d'·G
2. Negate d if P[y] is odd → d
3. Generate private nonce k' via tagged hash
4. Calculate R = k'·G, negate k if R[y] is odd
5. Calculate challenge e = tagged_hash("BIP0340/challenge", R[x] || P[x] || m)
6. Signature = r(R[x]) || s((k + e·d) % n)

**Tagged hashes:** All hashes use a tag prefix (`BIP0340/aux`, `BIP0340/nonce`, `BIP0340/challenge`) to avoid collisions between contexts.

```
tag_hash = SHA256(tag)
tagged_hash(data) = SHA256(tag_hash || tag_hash || data)
```
The tag hash is doubled (64 bytes = one SHA-256 block) to allow pre-computation of the first round.

**Batch verification:** Multiple signatures can be verified simultaneously, faster than individual verification. Uses random multipliers to prevent invalid signatures from canceling out.

**Connection to Taproot:** Schnorr's linearity enables key aggregation (MuSig) and key-path / script-path spending in Taproot.

#### Rust Code: Schnorr signatures (BIP 340)

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
    // Generate a Schnorr key pair
    let signing_key = SigningKey::random(&mut OsRng);
    let verifying_key = VerifyingKey::from(&signing_key);

    // x-only public key (32 bytes)
    let pubkey_bytes = verifying_key.to_bytes();
    println!("x-only public key: {} ({} bytes)",
        hex::encode(pubkey_bytes), pubkey_bytes.len());

    // Sign
    let message = b"Taproot transaction";
    let signature = signing_key.sign(message);

    println!("Signature: {} (64 bytes fixed)",
        hex::encode(signature.to_bytes()));

    // Verify
    let is_valid = verifying_key.verify(message, &signature).is_ok();
    println!("Valid? {}", is_valid); // true

    // Tagged hash (BIP 340)
    let challenge = tagged_hash(b"BIP0340/challenge", b"test data");
    println!("\nTagged hash (challenge): {}", hex::encode(challenge));

    // Size comparison
    println!("\n--- Comparison ---");
    println!("ECDSA (DER): ~70-72 bytes + sighash byte");
    println!("Schnorr:     64 bytes fixed (r[32] || s[32])");
    println!("Savings:     ~10% space, fixed encoding");
}
```

#### Dependencies

```toml
[dependencies]
k256 = { version = "0.13", features = ["schnorr"] }
sha2 = "0.10"
hex = "0.4"
```

---

## Thursday Summary

| Concept | Key Point | Rust Code |
|---------|-----------|-----------|
| **ECDSA** | `s = k^(-1)(z + r·d) mod n`, DER 70-72 bytes | `k256::ecdsa`, `SigningKey`, `VerifyingKey` |
| **Nonce** | Never reuse → private key recoverable | RFC 6979 (deterministic) |
| **Low-S** | If s > n/2 → n - s (BIP 62) | Handled automatically by `k256` |
| **Schnorr** | `s = (k + e·d) mod n`, 64 bytes fixed | `k256::schnorr`, x-only keys |
| **Tagged hash** | `SHA256(tag_hash||tag_hash||data)` | `tagged_hash()` |
| **Taproot** | Linearity → key/signature aggregation | MuSig, key-path / script-path |

---

## Friday May 29 — Keys and Addresses

### 13. Private Key

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/private-key/)

#### Theory

A private key is a very large **random number** used to derive a public key. It must never be publicly exposed.

**Generation:** 32 random bytes (256 bits) from a cryptographically secure source (`/dev/urandom` on Linux).

**Valid range:**
- Min: 1
- Max: n - 1 (where n = order of secp256k1)

**Security:** Standard `rand()` functions are NOT sufficient. Use cryptographically secure generators. Milk Sad incident (2023): libbitcoin used weak randomness → $900,000+ in losses.

**Key space:** ~2^256 ≈ 10^77 possible keys. For comparison, there are ~10^78 atoms in the observable universe. Accidental collision is effectively impossible.

#### Rust Code: secure generation

```rust
use k256::SecretKey;
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    // Secure generation with OsRng (OS cryptographic source)
    let secret_key = SecretKey::random(&mut OsRng);
    let key_bytes = secret_key.to_bytes();

    println!("Private key (32 bytes):");
    println!("Hex: {}", hex::encode(&key_bytes));
    println!("Size: {} bytes = {} bits", key_bytes.len(), key_bytes.len() * 8);

    // Verify the key is in valid range [1, n-1]
    // k256 does this automatically, but let's check manually
    let n = num_bigint::BigUint::parse_bytes(
        b"FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141", 16
    ).unwrap();
    let key_num = num_bigint::BigUint::from_bytes_be(&key_bytes);
    println!("\nKey < n? {}", key_num < n);
    println!("Key > 0? {}", key_num > num_bigint::BigUint::from(0u32));
}
```

#### Common mistake: weak randomness

```rust
// DO NOT DO THIS:
// let bad_key = rand::random::<[u8; 32]>(); // default rand is not crypto-secure

// DO THIS:
use k256::elliptic_curve::rand_core::OsRng;
let good_key = SecretKey::random(&mut OsRng); // OsRng uses /dev/urandom
```

---

### 14. Public Key

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/public-key/)

#### Theory

A public key is a **point on the secp256k1 curve** (x, y coordinates). It's derived from the private key via elliptic curve multiplication: **Q = k × G**.

**Encoding formats:**

| Format | Size | Prefix | Content |
|--------|------|--------|---------|
| Uncompressed | 65 bytes | `04` | x (32 bytes) + y (32 bytes) |
| Compressed | 33 bytes | `02` (even y) or `03` (odd y) | x (32 bytes) |
| x-only | 32 bytes | None | x (32 bytes), y assumed even |

**Properties:**
- **One-way function:** Private → Public (easy), Public → Private (infeasible)
- **Signature capability:** Prove possession of private key without revealing it

**Decompression:** Solve y^2 = x^3 + 7 (mod p) via y = y_sq^((p+1)/4) mod p, then choose the correct root based on prefix.

#### Rust Code: public key formats

```rust
use k256::{SecretKey, PublicKey, EncodedPoint};
use k256::elliptic_curve::rand_core::OsRng;

fn main() {
    let secret_key = SecretKey::random(&mut OsRng);
    let public_key = secret_key.public_key();

    // Uncompressed format (65 bytes, prefix 04)
    let uncompressed = public_key.to_encoded_point(false);
    println!("Uncompressed ({} bytes): {}...",
        uncompressed.as_bytes().len(),
        hex::encode(&uncompressed.as_bytes()[..10]));

    // Compressed format (33 bytes, prefix 02 or 03)
    let compressed = public_key.to_encoded_point(true);
    println!("Compressed ({} bytes): {}...",
        compressed.as_bytes().len(),
        hex::encode(&compressed.as_bytes()[..10]));

    // Identify the prefix
    let prefix = compressed.as_bytes()[0];
    let y_parity = if prefix == 0x02 { "even" } else { "odd" };
    println!("Prefix: 0x{:02x} (y {})", prefix, y_parity);

    // x-only format (32 bytes, used by Schnorr/Taproot)
    let x_only = &compressed.as_bytes()[1..]; // remove prefix
    println!("x-only ({} bytes): {}", x_only.len(), hex::encode(x_only));
}
```

---

### 15. Public Key Hash

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/public-key/hash/)

#### Theory

The public key hash is the result of hashing a public key via **HASH160**: `RIPEMD-160(SHA-256(public_key))`.

- **Input:** public key (33 or 65 bytes)
- **Output:** 20 bytes (160 bits)

**Usage:**
- **P2PKH:** ScriptPubKey contains the hash → `76a914<20-byte-hash>88ac`
- **P2WPKH:** ScriptPubKey contains the hash → `0014<20-byte-hash>`

**Why hash?** Satoshi: "To make Bitcoin Addresses short, they are a hash of the public key, not the public key itself." The hash shortens addresses and adds a security layer (public key only revealed when spending).

#### Rust Code

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

    // Compressed public key (33 bytes)
    let compressed = public_key.to_encoded_point(true);
    let pubkey_bytes = compressed.as_bytes();

    println!("Compressed public key:");
    println!("  {} ({} bytes)", hex::encode(pubkey_bytes), pubkey_bytes.len());

    // HASH160 = RIPEMD160(SHA256(pubkey))
    let pkh = hash160(pubkey_bytes);
    println!("\nPublic Key Hash:");
    println!("  {} (20 bytes)", hex::encode(pkh));

    // P2PKH ScriptPubKey
    let mut script = vec![0x76, 0xa9, 0x14]; // OP_DUP OP_HASH160 PUSH20
    script.extend_from_slice(&pkh);
    script.extend_from_slice(&[0x88, 0xac]); // OP_EQUALVERIFY OP_CHECKSIG
    println!("\nP2PKH ScriptPubKey:");
    println!("  {}", hex::encode(&script));

    // P2WPKH ScriptPubKey (SegWit)
    let mut script_wit = vec![0x00, 0x14]; // OP_0 PUSH20
    script_wit.extend_from_slice(&pkh);
    println!("\nP2WPKH ScriptPubKey:");
    println!("  {}", hex::encode(&script_wit));
}
```

---

### 16. Address

**Source:** [learnmeabitcoin.com](https://learnmeabitcoin.com/technical/keys/address/)

#### Theory

A Bitcoin address is a **human-readable encoding** of a public key hash or script hash. It doesn't exist on the blockchain — it's an external convention for wallets to know what ScriptPubKey to construct.

**Address types:**

| Type | Prefix | Length | Encoding | Content | Script |
|------|--------|--------|----------|---------|--------|
| P2PKH | `1` | 34 chars | Base58Check | 20-byte pubkey hash | OP_DUP OP_HASH160 <hash> OP_EQUALVERIFY OP_CHECKSIG |
| P2SH | `3` | 34 chars | Base58Check | 20-byte script hash | OP_HASH160 <hash> OP_EQUAL |
| P2WPKH | `bc1q` | 42 chars | Bech32 | 20-byte pubkey hash | OP_0 <hash> |
| P2WSH | `bc1q` | 62 chars | Bech32 | 32-byte script hash | OP_0 <hash> |
| P2TR | `bc1p` | 62 chars | Bech32m | 32-byte tweaked pubkey | OP_1 <key> |

**Historical progression:**
- 2009+: Base58Check (P2PKH `1...`, P2SH `3...`)
- 2016+: Bech32 (P2WPKH `bc1q...42`, P2WSH `bc1q...62`) — SegWit
- 2021+: Bech32m (P2TR `bc1p...62`) — Taproot

**SegWit address benefits:** The witness field consumes less block space → lower transaction fees.

#### Rust Code: create a complete P2PKH address

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
    // 1. HASH160 of the public key
    let pkh = hash160(pubkey);

    // 2. Prefix with version byte (0x00 for P2PKH mainnet)
    let mut payload = vec![0x00];
    payload.extend_from_slice(&pkh);

    // 3. Calculate checksum (first 4 bytes of HASH256)
    let checksum = hash256(&payload);
    payload.extend_from_slice(&checksum[..4]);

    // 4. Encode in Base58
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

    println!("Private key: {}", hex::encode(secret_key.to_bytes()));
    println!("Public key: {}", hex::encode(compressed.as_bytes()));

    // P2PKH address (legacy)
    let addr_p2pkh = pubkey_to_address_p2pkh(compressed.as_bytes());
    println!("\nP2PKH: {}", addr_p2pkh);
    // Starts with '1'

    // P2WPKH address (SegWit)
    let addr_bech32 = pubkey_to_address_bech32(compressed.as_bytes());
    println!("P2WPKH: {}", addr_bech32);
    // Starts with 'bc1q'

    // Derivation chain summary
    println!("\n--- Derivation chain ---");
    println!("Private key (32 bytes)");
    println!("  → k × G (elliptic curve multiplication)");
    println!("Public key (33 bytes, compressed)");
    println!("  → HASH160 (SHA256 then RIPEMD160)");
    println!("Public Key Hash (20 bytes)");
    println!("  → Base58Check / Bech32");
    println!("Address (34 chars P2PKH / 42 chars P2WPKH)");
}
```

---

## Friday Summary

| Concept | Key Point | Rust Code |
|---------|-----------|-----------|
| **Private Key** | 32 random bytes, range [1, n-1] | `SecretKey::random(&mut OsRng)` |
| **Public Key** | Point on curve, 3 formats (65/33/32 bytes) | `.to_encoded_point(true/false)` |
| **Public Key Hash** | HASH160 = RIPEMD160(SHA256(pubkey)), 20 bytes | `hash160()` |
| **P2PKH** | `1...`, Base58Check, version byte `00` | `base58_encode(version+hash+checksum)` |
| **P2WPKH** | `bc1q...`, Bech32, 42 characters | `bech32::encode("bc", ...)` |

---

## Week Summary

| Key Concept | Description | Rust Code |
|-------------|-------------|-----------|
| **Hex** | Basis of all Bitcoin displays (2 hex chars = 1 byte) | `hex::decode()`, `hex::encode()` |
| **Endian** | Little-endian for integers in protocol; hashes reversed for display | `u32::from_le_bytes()` |
| **CompactSize** | Variable-length integers (1/3/5/9 bytes depending on value) | `encode_compact_size()`, `decode_compact_size()` |
| **Base58** | Readable encoding with checksum (legacy addresses, WIF, extended keys) | `base58_encode()`, `base58_decode()` |
| **Bech32** | SegWit encoding, case-insensitive, improved checksum | `bech32::decode()`, `bech32::encode()` |
| **SHA256** | Hash used everywhere (blocks, transactions, keys) | `Sha256::digest()` |
| **secp256k1** | Bitcoin-specific elliptic curve (y^2 = x^3 + 7) | Modular operations on points |
| **ECDSA** | Classic signatures (DER, 70-72 bytes) | `k256::ecdsa` |
| **Schnorr** | Taproot signatures (64 bytes, aggregation, batch verification) | `k256::schnorr` |
| **Private → Public → Hash → Address** | The fundamental derivation chain | All functions combined |

---

## Next Week

[Week 2 — HD Wallets + Transactions](../semaine-2/README.md): WIF, signatures, HD wallets (BIP32/BIP39), inputs, outputs, UTXO.


<a href="#top">⬆ Retour au menu / Back to top</a>
