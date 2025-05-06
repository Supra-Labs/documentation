---
description: >-
  Vectors in Move are dynamic lists that can grow or shrink as needed, making
  them essential for managing collections of data, including structs.
---

# Using Vectors in Move

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## Vectors

When you want a list of values, use vectors. A `vector` in Move is dynamic by default and has no fixed size. It can get larger or smaller as needed. Vector in Supra and Aptos are available to import and use at `std::vector`.

You just need to do “`use std::vector`” at the top of your module to be able to access it.

You can also store structs in vectors, Note that for structs that are stored in a resource struct, you need to add the store attribute to the struct:

```toml
struct Balance has store {
        owner: address,
        balance: u64,
    }

struct GlobalData has key {
        balances: vector<Balance>,
    }
```

When creating an empty vector you can use the following syntax: `let empty_vector = vector[];`

We need to track all the Dinosaurs created from Dinosaur\_nest, We can do this by declaring two new structs:

`Dinosaur` Struct having Key and a new resource struct named `DinosaurSwarm` which has a vector of Dinosaur structs, this resource needs to be stored at `0xcafe` in `init_module` with an empty vector of Dinosaurs to start with.

## Code Follows Like:

```toml
module 0xcafe::Dinosaur_nest {
   use std::vector;

    struct DinosaurGendna has key {
        Gendna_digits: u64,
        Gendna_modulus: u256,
    }

    struct Dinosaur has store {
        Gendna: u64,
    }
    
    struct DinosaurSwarm has store {
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
}
```

We've only been using the `init_module` function, which is called when the module is deployed to initialize default values. we'll create one more function that will later be called by the user to create a new Dinosaur.

**Let’s create a new function named `spawn_Dinosaur` that takes one argument of type `u64` named `Gendna` and returns a Dinosaur struct with that Gendna:**

{% hint style="info" %}
Note: This function is public, which means it can be called from any other Move module. we'll keep all functions public, except for init\_module. init\_module has to be private because it's called only once when the module is deployed.
{% endhint %}

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
}
```
