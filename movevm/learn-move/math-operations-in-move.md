---
description: >-
  Move supports familiar math operations with integer-based precision, requiring
  typecasting for calculations between different unsigned integer types.
---

# Math Operations in Move

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## Math in Move

Doing math in Move is easy and very similar to other programming languages:

* Add: `x + y`
* Subtract: `x - y`
* Multiply: `x * y`
* Divide: `x / y`
* Mod: `x % y`
* Exponential: `x ^ y`

{% hint style="info" %}
Note: This is integer math, which means results are rounded down.

For example, `5 / 2 = 2`
{% endhint %}

To make sure our Dinosaur's Gendna is only 10 digits, let's make another `u64` value equal to 10^10. That way we can later use the modulus operator `%` to create valid Gendna codes from any randomly generated numbers.

Create another integer value named `Gendna_modulus` in the DinosaurGendna struct, and set it equal to 10 to the power of `Gendna_digits`

```toml
module 0xcafe::Dinosaur_nest {
    struct DinosaurGendna has key {
        Gendna_digits: u64,
        Gendna_modulus: u64, 
    }

    fun init_module(cafe_signer: &signer) {
        move_to(cafe_signer, DinosaurGendna {
            Gendna_digits: 10,
            Gendna_modulus: 10 ^ 10,
        });
    }
}
```

So far we've seen different types of integers: `u8`, `u32`, `u64`, `u128`, `u256`. Although math can be done easily among integers of the same type, it's not possible to do math directly between integers of different types,

## Example Below:

```toml
fun mixed_types_math(): u64 {
    let x: u8 = 1;
    let y: u64 = 2;
    // This will fail to compile as x and y are different types. One is u8, the other is u64.
    x + y
}
```

### **Let’s cast Gendna\_modulus to u256 with (Gendna\_modulus as u256). Remember that the parentheses () are required when typecasting:**

```toml
fun mixed_types_math(): u64 {
    let x: u8 = 1;
    let y: u64 = 2;
    // This will fail to compile as x and y are different types. One is u8, the other is u64.
    (x as u64) + y
}
```

### The actual contract would look like this:

```toml
module 0xcafe::Dinosaur_nest {
    struct DinosaurGendna has key {
        Gendna_digits: u64,
        Gendna_modulus: u256,
    }

    fun init_module(cafe_signer: &signer) {
        let Gendna_digits = 10;
        let Gendna_modulus = 10 ^ Gendna_digits;
        move_to(cafe_signer, DinosaurGendna {
            Gendna_digits,
            Gendna_modulus: (Gendna_modulus as u256),
        });
    }
}
```
