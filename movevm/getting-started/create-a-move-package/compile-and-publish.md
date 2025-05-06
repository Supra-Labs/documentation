---
description: Create and deploy your first move module.
---

# Compile and Publish

{% hint style="warning" %}
It is good practice to review the possible arguments of each command before you execute them. You can do this by passing the `--help` argument. Example: `supra move tool publish --help`
{% endhint %}

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](https://docs.supra.com/network-information) page.
{% endhint %}

{% stepper %}
{% step %}
### Compile your package

{% hint style="info" %}
You do not need to pass the --package-dir command if your current working directory is the root of your package where the `Move.toml` file is located.
{% endhint %}

Compiling your package will fetch any dependencies defined in your `Move.toml` file before building your package. A new directory named `build` will be created storing the bytecode and sources for your package.

{% code overflow="wrap" %}
```
supra move tool compile --package-dir /supra/configs/move_workspace/exampleContract
```
{% endcode %}
{% endstep %}

{% step %}
### Get Testnet Supra from the faucet

{% hint style="info" %}
Note that CLI commands will be executed from the currently activated profile within your CLI. If you want to execute the command from a different profile, you can pass the `--profile` argument or you can activate a different profile with `supra key activate-profile accountB`.
{% endhint %}

To publish our package, we will need some gas. Obtain some Testnet gas from the faucet to proceed. Testnet tokens do not hold any monetary value.

```
supra move account fund-with-faucet --rpc-url https://rpc-testnet.supra.com
```

{% hint style="info" %}
Note, there is no faucet for Mainnet. If you are looking to deploy on Mainnet, you will have to obtain Supra separately.
{% endhint %}
{% endstep %}

{% step %}
### Deploy your package

Once your package is compiled and you have tokens to cover the gas, execute the following command to publish your package to the network.

<pre data-overflow="wrap"><code><strong>supra move tool publish --package-dir /supra/configs/move_workspace/exampleContract --rpc-url https://rpc-testnet.supra.com
</strong></code></pre>
{% endstep %}
{% endstepper %}
