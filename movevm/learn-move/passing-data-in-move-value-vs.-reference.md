---
description: >-
  Move uses pass-by-value for copies and pass-by-reference (& for reading, &mut
  for modifying) to data handling.
---

# Passing Data in Move: Value vs. Reference

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## pass-by-value & pass-by-reference

In Move, when you pass a simple value as u64 to a function, you might be making a copy of it. This is called pass-by-value, **for example:**

```toml
fun add_one(value: u64) {
    // This doesn't change the original value!
    value = value + 1;
}

fun call_add_one() {
    let value = 10;
    // This is wrong as it makes a copy of value!
    add_one(value);

    // This will error out as the value is still 10!
    assert!(value == 11, 0);
}
```

**So how do we modify the original value?**

We need to pass a mut reference (`&mut`) to the value instead of the value itself. This is called pass-by-reference. This is similar to how you pass a pointer to a value in C/C++ or Rust.

There are two types of references in Move: `references` (&) and `mutable references` (\&mut). The immutable reference (&) is often used to pass a value such as a vector to a function that only intends to read data instead of writing it. A function that takes a reference needs to explicitly declare:

```toml
// get_first_element takes a reference to a vector and returns the first element.

fun get_first_element(values: &vector<u64>): u64 {
    // vector::borrow also takes a vector reference and returns a reference to the value.
    // * is needed here to dereference the value and make a copy of it to return.
    *vector::borrow(values, 0)
}
```

You can also pass a reference to a struct and modify it:

```
struct Balance {
    value: u64,
}

// This would fail if the function takes &Balance instead of &mut Balance.
fun double_balance(balance: &mut Balance): u64 {
    balance.value = balance.value * 2;
}
```

**Now, Write a function in `Dinosaur_nest` that returns the `Gendna` of the first Dinosaur in the `DinosaurSwarm`. Don’t forget the “`acquires`” declaration!**

```
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

    public fun set_Gendna_digits(new_Gendna_digits: u64) acquires DinosaurGendna {
        let Dinosaur_Gendna = borrow_global_mut<DinosaurGendna>(@0xcafe);
        Dinosaur_Gendna.Gendna_digits = new_Gendna_digits;
    }

public fun get_first_Dinosaur_Gendna(): u64 acquires DinosaurSwarm {
        let Dinosaur_swarm = borrow_global<DinosaurSwarm>(@0xcafe);    
        let first_Dinosaur = vector::borrow(&Dinosaur_swarm.Dinosaurs, 0);
        first_Dinosaur.Gendna        
  }
}
```
