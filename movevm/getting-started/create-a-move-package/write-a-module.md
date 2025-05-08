---
description: Create and deploy your first move module.
---

# Write a Module

{% hint style="danger" %}
If you have not already, open your newly initiatlized project within your code editor of choice on your host machine. Remember, the container `configs` directory is bound to the `supra_configs` directory on your host machine. Any changes made to these files on the host machine will be reflected within the container.

\
If you are using VS Code, we highly recommend that you[ install the Supra Move extension](../../dev/supra-move-vs-code-extension.md).
{% endhint %}

{% stepper %}
{% step %}
### Create a new move file within the `sources` directory.

{% hint style="warning" %}
In the previous step, we set the named address of `@exampleAddress` to that of your own account. Notice how the name of this module is `exampleAddress::transfer`. Modules are defined with the following format: `ADDRESS::MODULE_NAME` \
\
In Move, global storage is a forest with trees rooted at an account address. Accounts can store both resource data and module code. Modules are deployed to an account, rather than a unique address being generated at deployment as you may be custom to with other VMs. \
\
At compilation, `exampleAddress` will be replaced with the value you set in the named addresses within the `Move.toml` file. Upon publishing the module, it will be published at that designated account.
{% endhint %}

The sources directory holds the move modules that you will be working on within your project. Within the `sources` directory,  create the `example.move` file with the following code.&#x20;

{% hint style="info" %}
You can create the file through your code editor, or by executing one of the following commands: `touch example.move`  or `echo > example.move`
{% endhint %}

{% code title="example.move" lineNumbers="true" %}
```
module exampleAddress::transfer {

    use supra_framework::supra_coin;
    use supra_framework::coin;

    // Function to transfer the specified amount to two destinations from the source signer
    public entry fun two_by_two(
        first: &signer,
        amount_first: u64,
        dst_first: address,
        dst_second: address,
    ) {
        // Transfer the specified amount to the first destination
        coin::transfer<supra_coin::SupraCoin>(first, dst_first,amount_first);
        // Transfer the same amount to the second destination
        coin::transfer<supra_coin::SupraCoin>(first,dst_second, amount_first);
    }

     // Function to view the balance of an address
    #[view]
    public fun view_balance(address: address): u64 {
        coin::balance<supra_coin::SupraCoin>(address)
    }

}
```
{% endcode %}

The `two_by_two` function provided has four parameters. The signer, amount to be transferred, and two destination addresses. The function will transfer the passed amount to both destination addresses.

{% hint style="info" %}
Signer is a built-in Move resource. It indicates which account is responsible for calling the transaction. This value is automatically injected by the VM and not passed by the user.
{% endhint %}
{% endstep %}
{% endstepper %}
