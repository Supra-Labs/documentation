---
description: Create and deploy your first move module.
---

# Interact with a Package

{% stepper %}
{% step %}
### Calling a view function

Command: `supra move tool view`

The `view` command is used to call `view` functions, which do not change the state of the VM in any way. View functions are denoted with `#[view]` above the function declaration.

Execute the following command to better understand the arguments of the command.

```bash
supra move tool view --help
```
{% endstep %}

{% step %}
### Executing an entry function

Command: `supra move tool run`

The `run` command is used to call `entry` functions, which result in some transaction. `Entry` functions are your gateway to interacting with modules. We are unable to call functions without the `entry` declaration.

Execute the following command to better understand the arguments of the command.

```bash
supra move tool run --help
```
{% endstep %}
{% endstepper %}

## Interacting with your module

The `hello_blockchain` module stores a string at an account within the `MessageHolder` resource that can later be retrieved. To try interacting with the contract we will:

1. Call `set_message` to store a string on-chain
2. Call `get_message` to view the stored message on-chain

{% stepper %}
{% step %}
### Store a string/message on-chain

{% hint style="info" %}
Replace the place holder values denoted by `<>` with your account address before executing the commands.
{% endhint %}

```
supra move tool run --function-id '<hello_blockchain>::message::set_message' --args string:"Hello world!" --rpc-url https://rpc-testnet.supra.com
```
{% endstep %}

{% step %}
### View the string/message stored on-chain

{% code title="replace & execute me!" %}
```
supra move tool view --function-id '<hello_blockchain>::message::get_message' --args address:<hello_blockchain> --rpc-url https://rpc-testnet.supra.com
```
{% endcode %}
{% endstep %}

{% step %}
### Final step: [Join our Discord](https://discord.gg/supralabs)!
{% endstep %}
{% endstepper %}

## Commands used to interact with modules

<details>

<summary>Calling a view function: <code>supra move tool view</code></summary>

The `view` command is used to call `view` functions, which do not change the state of the VM in any way. View functions are denoted with `#[view]` above the function declaration.



Expects the two following arguments:\
&#x20;`--function-id` which designates the function to be called. The format is as follows: `MODULE_ADR::MODULE_NAME::FUNCTION_NAME`&#x20;

&#x20;`--args` which designates the arguments to be passed to the function. If multiple parameters must be passed, they are separated by spaces. The format is as follows: `TYPE:VALUE`



Execute the following command to better understand the arguments of the command.

```bash
supra move tool view --help
```

</details>

<details>

<summary>Executing an entry function: <code>supra move tool run</code></summary>

The `run` command is used to call `entry` functions, which result in some transaction. `Entry` functions are your gateway to interacting with modules. We are unable to call functions without the `entry` declaration.



Expects the two following arguments:\
&#x20;`--function-id` which designates the function to be called. The format is as follows: `MODULE_ADR::MODULE_NAME::FUNCTION_NAME`&#x20;

&#x20;`--args` which designates the arguments to be passed to the function. If multiple parameters must be passed, they are separated by spaces. The format is as follows: `TYPE:VALUE`



Execute the following command to better understand the arguments of the command.

```bash
supra move tool run --help
```

</details>
