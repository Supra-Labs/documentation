---
description: New to Move? Start here with the basics!
---

# Getting Started with Move

Learn the fundamentals of the Move programming language with simple, step-by-step examples. This section covers key concepts like integers, math operations, and more, helping you build a solid foundation for smart contract development.

***

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

## Getting Started with Move

Move code is organized into modules that are uploaded to the Supra blockchain. Users can interact with the modules by calling functions from these modules via transactions. Modules are grouped into packages. When uploading code to the blockchain, an entire package of multiple modules will be uploaded.

Each Move module has a unique identifier comprising the address it's deployed at and the module name. The fully qualified name is of the format `<address>::<module_name>`

```toml
module 0xcafe::Dinosaur_nest {
}
```

**We'll create a DinosaurNest contract with the following features:**

Keep track of all the Dinosaurs it has spawned. Birth new Dinosaurs via a function We'll generate a special Gendna code every time a Dinosaur is spawned to create unique genetics for each Dinosaur. The special abilities genetic code has digits and looks like below: `1234567890`

This special Gendna code represents each Dinosaur's unique attributes, with every 2 digits representing one, such as shirts, hats, and glasses. Later, you can have more fun and add new attributes by adding more digits to the special abilities code.

Let's start by adding a number to track the number of digits allowed in a Gendna code. This number will be stored on the blockchain (global storage). This data can be accessed and changed by the module's code as well as read by web UI.

On Supra, all data written to the blockchain needs to be declared in a struct that has the key attribute. For now, take a look at the simple example below:

```toml
module 0xcafe::Dinosaur_nest {
    // All structs that are written to blockchain need to have the `key` attribute.
    struct DinosaurGendna has key {
        Gendna_digits: u64,
    }   
}
```

In the example above, we're storing a simple value (`Gendna_digits`) in global storage. This value can be read in functions later. One common confusion is between global storage and local variables. Unless explicitly declared in `struct` the `key` attribute, all variables declared and modified in a function are local variables and thus not stored on the blockchain (in global storage). Also, note that the struct name is capitalized by convention (`DinosaurGendna` instead of `Dinosaur_Gendna`).
