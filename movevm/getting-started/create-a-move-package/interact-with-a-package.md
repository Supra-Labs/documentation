---
description: Create and deploy your first move module.
---

# Interact with a Package

## Commands used to interact with modules

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

Both commands expect the argument `--function-id` which designates the function to be called. The format is as follows: `MODULE_ADR::MODULE_NAME::FUNCTION_NAME`&#x20;

Function parameters are passed with the argument `--args`. If multiple parameters must be passed, they are separated by spaces. The format is as follows: `TYPE:VALUE`

{% code title="Example command" %}
```
supra move tool view --function-id '0xCAF3::transfer::view_balance' --args address:0xCAF3 --rpc-url https://rpc-testnet.supra.com
```
{% endcode %}

## Interacting with your module

The example module will transfer tokens from the calling account to the two destination accounts. To gain experience with interacting with a package, we will:

1. Check the balance of your account with the `view_balance` function
2. Transfer tokens to the destination addresses with the `two_by_two` function
3. Check the balance of your account with the `view_balance` function

We should observe that the balance returned in step 3 is less than the balance returned in step 1 due to the tokens being transferred in step 2.

{% stepper %}
{% step %}
### Check the initial balance

{% hint style="info" %}
&#x20;In this example, the module was deployed to the named address `@exampleAddress` that we set in the `move.toml` file. Replace the place holder values denoted with `<>` below to execute the command.
{% endhint %}

Our example module included a view function `view_balance`. For our first interaction, lets check the balance of your account.&#x20;

{% hint style="info" %}
As we have deployed the module to the same account that you will be calling the function from, both the module address and the address argument will be the same.
{% endhint %}

{% code title="replace & execute me!" %}
```
supra move tool view --function-id '<exampleAddress>::transfer::view_balance' --args address:<ADDRESS_TO_CHECK> --rpc-url https://rpc-testnet.supra.com
```
{% endcode %}
{% endstep %}

{% step %}
### Call the deployed `two_by_two` entry function

{% hint style="info" %}
Replace the place holder values denoted with `<>` below to execute the command. Following the execution of this command, repeat step 1 to observe the new balance(s).
{% endhint %}

{% code title="replace & execute me!" %}
```
supra move tool run --function-id '<exampleAddress>::transfer::two_by_two' --args u64:10000000 address:<FIRST_ADR> address:<SECOND_ADR> --rpc-url https://rpc-testnet.supra.com
```
{% endcode %}
{% endstep %}

{% step %}
### Repeat step 1 to check the updated balance

You should now observe that the balance of your account is less than it was when first observed in step 1. Feel free to play around with this more to get a feel for the commands.
{% endstep %}

{% step %}
### Final step: [Join our Discord](https://discord.gg/supralabs)!
{% endstep %}
{% endstepper %}
