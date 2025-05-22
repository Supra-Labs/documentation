---
description: Create and deploy your first move module.
---

# Create a Move Package

## Initialize your move package

{% stepper %}
{% step %}
### Create a new Move package

{% code title="execute me!" %}
```
supra move tool init --package-dir /supra/move_workspace/exampleContract --name exampleContract
```
{% endcode %}

This will create your project directory automatically, including a `Move.toml` file. This is a manifest file that contains important metadata about your package. This includes the package name, version, dependencies, named addresses, and more.&#x20;

The `SupraFramework` dependency is automatically populated. This framework includes core components that you will interact with during your time on Supra. The framework package includes clearly defined documentation within the `/doc` directory for you to review alongside the Move code within the `/sources` directory. You can view [this package here](https://github.com/Entropy-Foundation/aptos-core/tree/dev/aptos-move/framework/supra-framework).

{% code title="Move.toml" %}
```toml
[package]
name = "exampleContract"
version = "1.0.0"
authors = []

[addresses]

[dev-addresses]

[dependencies.SupraFramework]
git = "https://github.com/Entropy-Foundation/aptos-core.git"
rev = "dev"
subdir = "aptos-move/framework/supra-framework"

[dev-dependencies]

```
{% endcode %}
{% endstep %}

{% step %}
### Update the named addresses

Open the `move.toml` file with your code editor. You can find these files on your host machine in the `/Documents/supra/move_workspace/exampleContract` directory if you followed this setup guide.

Named addresses allow identifiers such as `@hello_blockchain` to be used throughout your package, rather than hard coding an address value. When compiled into bytecode, any occurrence of the identifier such as `@hello_blockchain` will be replaced with the set value below.\
\
For now, add the below named address to your `Move.toml` file and set the value to your address. We'll explain why in the next step.

{% hint style="info" %}
You can view the address of your profiles by executing: `supra profile -l`
{% endhint %}

```toml
[addresses]
hello_blockchain ="YOUR-ADDRESS-HERE"
```
{% endstep %}
{% endstepper %}
