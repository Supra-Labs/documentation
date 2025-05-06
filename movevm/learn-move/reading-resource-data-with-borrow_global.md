---
description: >-
  borrow_global allows you to read an existing resource struct from a specific
  address without needing a signer, but the function must declare that it
  accesses the resource using acquires ResourceName.
---

# Reading Resource Data with borrow\_global

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## `borrow_global`: read an existing resource struct

We just need to call `borrow_global` with the right resource type and address. The address can be passed into the function as an argument or referred to specifically with @ such as `@0xcafe`. A signer is not required here. If the resource is not found at the address, the function will error when the code is run on the blockchain. The function that calls `borrow_global` also needs to declare that it does so by adding “`acquires ResourceName`” at the end.

**Let’s write a new function named get\_Gendna\_digits that returns the Gendna\_digits field of the DinosaurGendna resource stored at 0xcafe:**

```toml
module 0xcafe::Dinosaur_nest {
    use std::vector;

    struct DinosaurGendna has key {
        Gendna_digits: u64,
        Gendna_modulus: u64,
    }

    struct Dinosaur has store {
        Gendna: u64,
    }

    struct DinosaurSwarm has key {
        Dinosaurs: vector<Dinosaur>,
    }

    fun init_module(cafe_signer: &signer) {
        let Gendna_digits = 10;
        let Gendna_modulus = 10 ^ Gendna_digits;
        move_to(cafe_signer, DinosaurGendna {
            Gendna_digits,
            Gendna_modulus: (Gendna_modulus as u256),
        });
        move_to(cafe_signer, DinosaurSwarm {
            Dinosaurs: vector[],
        });
    }

    public fun spawn_Dinosaur(Gendna: u64): Dinosaur {
        Dinosaur {
            Gendna,
        }
    }

public fun get_Gendna_digits(): u64 acquires DinosaurGendna {
borrow_global<DinosaurGendna>(@0xcafe).Gendna_digits
  }
}
```
