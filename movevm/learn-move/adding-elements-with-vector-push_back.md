---
description: >-
  vector::push_back allows you to add elements to a vector by passing a mutable
  reference (&mut), enabling dynamic storage updates within a module.
---

# Adding Elements with vector::push\_back

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## `vector::push_back`: add all the things we've learned

We can add all the things we've learned so far to create a more complex module. One more thing you can do now that you know references: You can add an element to a vector using `vector::push_back` and pass a mutable reference to the vector.

**Let’s Modify `spawn_Dinosaur` to add the new Dinosaur to the DinosaurSwarm's vector of Dinosaurs instead of returning it.**

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

    public fun spawn_Dinosaur(Gendna: u64) acquires DinosaurSwarm {
        let Dinosaur = Dinosaur {
            Gendna,
        };
        let Dinosaur_swarm = borrow_global_mut<DinosaurSwarm>(@0xcafe);
        vector::push_back(&mut Dinosaur_swarm.Dinosaurs, Dinosaur);
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
