---
description: >-
  Events allow smart contracts to emit signals that external applications can
  listen for, making it possible to track blockchain actions in real time.
---

# Emitting Events with event::emit

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## Event::Emit

Events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and taking action when they happen.

In order to emit an event, you need to do three things:

* Define the event struct.
* Use `event::emit` to emit it

{% hint style="info" %}
**Note:** Event structs need to be declared with the `#[event]` annotation.
{% endhint %}

**Finally, Let's add an event named `SpawnDinosaurEvent` that contains the new Gendna code & Emit `SpawnDinosaurEvent` when a Dinosaur is created.**

```toml
module 0xcafe::Dinosaur_nest {
    use supra_framework::account;
    use supra_framework::event;
   use std::vector;

    struct DinosaurGendna has key {
        Gendna_digits: u64,
        Gendna_modulus: u64,
    }

    struct Dinosaur has store {
        Gendna: u64,
    }

    #[event]
    struct SpawnDinosaurEvent has drop, store {
        Gendna: u64,
    } 
    struct DinosaurSwarm has key {
        Dinosaurs: vector<Dinosaur>,
    }

    fun init_module(cafe_signer: &signer) {
        let Gendna_modulus = 10 ^ 10;
        move_to(cafe_signer, DinosaurGendna {
            Gendna_digits: 10,
            Gendna_modulus,
        });
        move_to(cafe_signer, DinosaurSwarm {
            Dinosaurs: vector[],
        });
    }

    fun spawn_Dinosaur(Gendna: u64) acquires DinosaurSwarm {
        let Dinosaur = Dinosaur {
            Gendna,
        };
        let Dinosaur_swarm = borrow_global_mut<DinosaurSwarm>(@0xcafe);
        vector::push_back(&mut Dinosaur_swarm.Dinosaurs, Dinosaur);

        event::emit(SpawnDinosaurEvent {
         Gendna,
        });
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

## Wrapping Up

Till now you must have gotten a good idea of how things get Built and How Logic works in MOVE. You can find the Move.toml and Source File in the repo, fort it, and run on your side to get hands-on and learn with building your version of the Move Module as well.

## Contribution

Feel free to contribute to this project by submitting pull requests or opening issues, all contributions that enhance the functionality or user experience of this project are welcome.
