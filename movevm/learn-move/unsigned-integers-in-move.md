---
description: >-
  Learn how Move handles different unsigned integer types, their use cases, and
  how they impact storage, performance, and gas efficiency.
---

# Unsigned Integers in Move

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## Unsigned Integers: u8, u32, u64, u128, u256

Move supports multiple different types of unsigned integers. The values of these integers are always zero or larger. The different types of integers have different maximum values they can store. For example, `u8` can store values up to 255, while `u256` can store values up to 2^256 - 1. The most commonly used integer type is `u64`. Smaller types such as `u8` and `u32` are usually only used to save on gas (less storage space used). Larger sizes such as `u128` and `u256` are only used for when you need to store very large numbers.

Now that we have defined the `DinosaurGendna` struct, we need to set its initial value. We can’t set the initial value directly when defining a `struct`, so we'll need to create a function to do that. Recall that structs can be stored directly in global storage on Supra. Structs stored in global storage are called Resources. Each Resource needs to be stored at a specific address. This can be the same address where the module is deployed or any other user address. This is different from other blockchains - code and data can coexist at the same address on Supra. For our `Dinosaur_nest module`, we'll keep it simple and store the `DinosaurGendna` resource at the same address as the module. Let’s initialize it by writing the `init_module` function, where we get access to the signer of the address we're deploying to. `init_module` is called when the module is deployed to the blockchain and is the perfect place for initializing data. Don't worry about what a signer is for now - we'll cover that in later lessons. You just need to know that the signer is required to create and store a new resource at an address the first time.

Moving on with our code:

```toml
module 0xcafe::Dinosaur_nest {
    struct DinosaurGendna has key {
        Gendna_digits: u64,
    }
    
    fun init_module(cafe_signer: &signer) {
        move_to(cafe_signer, DinosaurGendna {
            Gendna_digits: 10,
        });
    }
}
```

In the example above, we're creating a new `DinosaurGendna` resource with a default value (`Gendna_digits`) of 10 (our Gendna Value as shared earlier) and storing it at 0xcafe (same address as the module) with `move_to`.

`move_to is` a special function that requires a signer that can be called to store a resource at a specific address.
