# Binary Canonical Serialization (BCS) Standard Guide

Binary Canonical Serialization (BCS) is the fundamental serialization format powering the Supra L1 ecosystem. BCS is a binary canonical non-self-describing serialization format designed for efficient data structure serialization. The Supra MoveVM, utilizes BCS for all critical blockchain operations, ensuring deterministic and efficient data handling across the network.

### Overview

Key Characteristics:

* **Binary format**: Compact, efficient storage and transmission
* **Canonical:** Deterministic serialization ensures identical outputs for identical inputs
* **Non-self-describing:** The reader must know the expected data format beforehand
* **Comprehensive**: Used for all on-chain data, API responses, and transaction arguments

## Primitive Types

All primitive types in BCS follow little-endian byte ordering for multi-byte integers.

### Boolean (bool)

Booleans are serialized as a single byte with strict value constraints.

| Value | Bytes |
| ----- | ----- |
| true  | 0x01  |
| false | 0x00  |

```
#[test_only]
module supra_example::bcs_examples {
    use std::bcs;
    use std::from_bcs;
    
    #[test]
    fun test_bool_serialization() {
        // Serialize
        let val: bool = true;
        let bytes: vector<u8> = bcs::to_bytes(&val);
        assert!(bytes == vector[0x01], 0);
        
        // Deserialize
        let val_des = from_bcs::to_bool(bytes);
        assert!(val_des == true, 1);
    }
}
```

### Unsigned Integers

#### **U8 (8-bit unsigned integer)**

```
let val: u8 = 255;
let bytes = bcs::to_bytes(&val); // Results in [0xFF]
```

#### **U16 (16-bit unsigned integer)**

2 bytes in little-endian order:

```
#[test_only]
module supra_example::bcs_examples {
    use std::bcs;
    use std::from_bcs;
    
    #[test]
    fun test_u16_serialization() {
        // Serialize
        let val: u16 = 1000;
        let bytes: vector<u8> = bcs::to_bytes(&val);
        assert!(bytes == vector[0xe8, 0x03], 0); // 1000 in little-endian
        
        // Deserialize
        let val_des = from_bcs::to_u16(bytes);
        assert!(val_des == 1000, 1);
    }
}
```

#### **U32 (32-bit unsigned integer)**

4 bytes in little-endian order:

```
#[test]
fun test_u32_serialization() {
    // Serialize
    let val: u32 = 1000000000;
    let bytes: vector<u8> = bcs::to_bytes(&val);
    assert!(bytes == vector[0x00, 0xca, 0x9a, 0x3b], 0);
    
    // Deserialize
    let val_des = from_bcs::to_u32(bytes);
    assert!(val_des == 1000000000, 1);
}
```

#### **U64 (64-bit unsigned integer)**

8 bytes in little-endian order:

```
#[test]
fun test_u64_serialization() {
    // Serialize
    let val: u64 = 10000000000000000;
    let bytes: vector<u8> = bcs::to_bytes(&val);
    assert!(bytes == vector[0x00, 0x40, 0x9c, 0x4f, 0x2c, 0x68, 0x00, 0x00], 0);
    
    // Deserialize
    let val_des = from_bcs::to_u64(bytes);
    assert!(val_des == 10000000000000000, 1);
}
```

#### **U128 (128-bit unsigned integer)**

16 bytes in little-endian order:

```
#[test]
fun test_u128_serialization() {
    // Serialize
    let val: u128 = 10000000000000000;
    let bytes: vector<u8> = bcs::to_bytes(&val);
    assert!(vector::length(&bytes) == 16, 0); // Always 16 bytes
    
    // Deserialize
    let val_des = from_bcs::to_u128(bytes);
    assert!(val_des == 10000000000000000, 1);
}
```

#### **U256 (256-bit unsigned integer)**

32 bytes in little-endian order:

```
#[test]
fun test_u256_serialization() {
    // Serialize
    let val: u256 = 10000000000000000;
    let bytes: vector<u8> = bcs::to_bytes(&val);
    assert!(vector::length(&bytes) == 32, 0); // Always 32 bytes
    
    // Deserialize
    let val_des = from_bcs::to_u256(bytes);
    assert!(val_des == 10000000000000000, 1);
}
```

#### Variable Length Encoding (Uleb128)

Uleb128 (unsigned 128-bit variable length integer) uses a variable number of bytes where the most significant bit indicates continuation. This encoding is commonly used for:

<kbd>// Currently not supported by itself in Move</kbd>

## Complex Types

### Sequences and Fixed Sequences

**Sequences (Vectors):** Sequences are serialized as a variable length vector of an item. The length of the vector is serialized as a Uleb128 followed by repeated items.

```
#[test]
fun test_vector_serialization() {
    let val = vector[1u8, 2u8, 3u8];
    let bytes = bcs::to_bytes(&val);
    assert!(bytes == vector[3, 1, 2, 3], 0); // Length (3) + items
    
    let val_des = from_bcs::to_bytes(bytes);
    assert!(val_des == vector[1, 2, 3], 1);
}
```

**Fixed Sequences**: Fixed Sequences are serialized without the leading size byte. The reader must know the number of bytes prior to deserialization. This is efficient when the size is known at compile time.

```
// For a fixed array of 3 u8 values: [1, 2, 3]
// Serialized as: [0x01, 0x02, 0x03] (no length prefix)
```

### Strings

Strings are serialized as a vector of bytes with `UTF-8` encoding. The length is stored as a Uleb128 prefix followed by the UTF-8 encoded bytes.

```
#[test]
fun test_string_serialization() {
    // Simple ASCII string
    let simple_str = b"hello";
    let bytes = bcs::to_bytes(&simple_str);
    assert!(bytes == vector[5, b'h', b'e', b'l', b'l', b'o'], 0);
    
    // UTF-8 string with special characters
    // Note: "çå∞≠¢õß∂ƒ∫" has 10 characters but 24 bytes in UTF-8
    let utf8_str = "çå∞≠¢õß∂ƒ∫";
    let bytes = bcs::to_bytes(&utf8_str);
    // First byte is length (24), followed by UTF-8 encoded bytes
    assert!(*vector::borrow(&bytes, 0) == 24, 1);
}
```

### Account Addresses

Account Address is serialized as a fixed `32-byte` vector of bytes.

```
// Address @0x1 becomes:
// [0x00, 0x00, ..., 0x00, 0x01] (32 bytes total
```

### Structs

Structs are serialized as an ordered set of fields. The fields are serialized in the order they are defined in the struct.

```
struct Color {
    r: u8,  // Red component
    g: u8,  // Green component  
    b: u8,  // Blue component
}
// Color { r: 1, g: 2, b: 3 } serializes to [0x01, 0x02, 0x03]
```

### Option Types

Options are serialized as a single byte to determine whether it's filled. If the option is None, the byte is `0x00`. If the option is Some, the byte is `0x01` followed by the serialized value.

```
let some_data: Option<u8> = option::some(8);
// Serializes to [0x01, 0x08]

let no_data: Option<u8> = option::none();
// Serializes to [0x00]
```

### Enums

Enums are serialized as a `uleb128` to determine which variant is being used. The variant index is followed by the serialized value of the variant data.

```
// Example enum definition
enum PaymentMethod has drop {
    Cash(u64),           // Variant 0
    CreditCard(vector<u8>), // Variant 1 
    Crypto(address),     // Variant 2
}

// Serialization examples:
// PaymentMethod::Cash(1000) => [0x00, 0xe8, 0x03] (variant 0 + amount)
// PaymentMethod::CreditCard(b"1234") => [0x01, 0x04, 0x31, 0x32, 0x33, 0x34] (variant 1 + length + data)
// PaymentMethod::Crypto(@0x1) => [0x02, ...32 bytes of address] (variant 2 + address)
```

### Maps

Maps are stored as a sequence of key-value tuples. The length of the map is serialized as a `Uleb128` followed by repeated key-value pairs. Maps are typically sorted by key for canonical ordering.&#x20;

```
// Example with SimpleMap or Table
use std::simple_map::{Self, SimpleMap};

public fun serialize_map_example(): vector<u8> {
    let map = simple_map::create<u8, u8>();
    simple_map::add(&mut map, 1, 10);
    simple_map::add(&mut map, 2, 20);
    simple_map::add(&mut map, 3, 30);
    
    bcs::to_bytes(&map)
    // Results in: [0x03, 0x01, 0x0a, 0x02, 0x14, 0x03, 0x1e]
    // Format: length(3) + (key1, value1) + (key2, value2) + (key3, value3)
}
```

## BCS Stream Deserialization Module

Supra includes a specialized BCS stream deserialization module that enables efficient processing of BCS-formatted byte arrays into Move primitive types. This module is available in the official Supra framework repository.

### Deserialization Strategies

**Per-Byte Deserialization**

* Used for most primitive types to minimize gas consumption
* Processes each byte individually to match length and type requirements
* Optimized for cost-effective on-chain operations

**Function-Based Deserialization**

* Used specifically for `deserialize_address` function
* Leverages `aptos_std::from_bcs` due to type constraints
* Higher gas cost but necessary for certain complex types

### Key Features

```
// Example usage of BCS stream deserialization
module supra_framework::bcs_stream {
    // Deserializes primitive types byte-by-byte for efficiency
    public fun deserialize_u8(bytes: &vector<u8>, offset: &mut u64): u8 { ... }
    public fun deserialize_u16(bytes: &vector<u8>, offset: &mut u64): u16 { ... }
    public fun deserialize_u32(bytes: &vector<u8>, offset: &mut u64): u32 { ... }
    public fun deserialize_u64(bytes: &vector<u8>, offset: &mut u64): u64 { ... }
    public fun deserialize_u128(bytes: &vector<u8>, offset: &mut u64): u128 { ... }
    public fun deserialize_bool(bytes: &vector<u8>, offset: &mut u64): bool { ... }
    
    // Uses function-based approach for address due to type constraints
    public fun deserialize_address(bytes: &vector<u8>, offset: &mut u64): address { ... }
}
```

## Important Considerations

### Non-Self-Describing Format

**BCS is a non-self-describing format, which means:**

* The `serialized` data does not contain type information
* The `deserializer` must know the expected type structure beforehand
* Schema evolution requires careful planning and versioning
* Type mismatches during deserialization can cause runtime errors

### Canonical Ordering

**BCS ensures canonical (deterministic) serialization:**

* Same input always produces same output
* Field order in structs matters and must be consistent
* Map entries are typically sorted by key
* This is crucial for consensus and verification

## Sample Example

{% hint style="success" %}
**Learn how to Initialize & Publish a Move Module** [**from HERE.**](getting-started/create-a-move-package/)
{% endhint %}

{% code title="source.move" %}
```
module supra_example::framework_integration {
    use supra_framework::supra_coin::SupraCoin;
    use supra_framework::coin;
    use supra_framework::account;
    use supra_framework::event;
    use std::bcs;
    use std::signer;
    
    #[event]
    struct SupraTransactionEvent has drop, store {
        transaction_data: vector<u8>,
        transaction_hash: vector<u8>,
    }
    
    public entry fun supra_transfer_with_verification(
        sender: &signer,
        recipient: address,
        amount: u64
    ) {
        // Create account for recipient if needed
        if (!account::exists_at(recipient)) {
            account::create_account(recipient);
        };
        
        // Register SupraCoin if needed
        if (!coin::is_account_registered<SupraCoin>(recipient)) {
            coin::register<SupraCoin>(sender);
        };
        
        // Perform transfer using Supra framework
        coin::transfer<SupraCoin>(sender, recipient, amount);
        
        // Serialize transaction for verification
        struct TransactionData has drop {
            sender: address,
            recipient: address,
            amount: u64,
            timestamp: u64,
        }
        
        let tx_data = TransactionData {
            sender: signer::address_of(sender),
            recipient,
            amount,
            timestamp: timestamp::now_seconds(),
        };
        
        let serialized_tx = bcs::to_bytes(&tx_data);
        let tx_hash = hash::sha3_256(serialized_tx);
        
        // Emit event with BCS-serialized data
        event::emit(SupraTransactionEvent {
            transaction_data: serialized_tx,
            transaction_hash: tx_hash,
        });
    }
}
```
{% endcode %}

## Conclusion

Binary Canonical Serialization provides Supra Move developers with a robust, efficient, and deterministic foundation for data handling across the entire Supra ecosystem. By mastering BCS fundamentals and leveraging the specialized stream deserialization capabilities within the Supra framework, developers can build more efficient and reliable applications while minimizing gas costs and maximizing performance.
