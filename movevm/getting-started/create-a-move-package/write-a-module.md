---
description: Create and deploy your first move module.
---

# Write a Module

{% hint style="danger" %}
If you have not already, open your newly initiatlized project within your code editor of choice on your host machine. Remember, the `move_workspace` directory within the container is bound to the `/supra/move_workspace` directory on your host machine. Any changes made to these files on the host machine will be reflected within the container.

\
If you are using VS Code, we highly recommend that you[ install the Supra Move extension](../../dev/supra-move-vs-code-extension.md).
{% endhint %}

{% stepper %}
{% step %}
### Create a new move file within the `sources` directory.

{% hint style="warning" %}
In Move, global storage is a forest with trees rooted at an account address. Accounts can store both resource data and module code. Modules are deployed to an account, rather than a unique address being generated at deployment as you may be custom to with other VMs.
{% endhint %}

The sources directory holds the move modules that you will be working on within your project. Within the `sources` directory of your `exampleContract` package, create the `hello_blockchain.move` file.

{% code title="execute me!" %}
```
echo > /supra/move_workspace/exampleContract/sources/hello_blockchain.move
```
{% endcode %}

Once created, open the hello\_blockchain.move file within your code editor and add the following code.

{% code title="hello_blockchain.move" %}
```
module hello_blockchain::message {
    use std::error;
    use std::signer;
    use std::string;
    use supra_framework::event;

    //:!:>resource
    struct MessageHolder has key {
        message: string::String,
    }
    //<:!:resource

    #[event]
    struct MessageChange has drop, store {
        account: address,
        from_message: string::String,
        to_message: string::String,
    }

    /// There is no message present
    const ENO_MESSAGE: u64 = 0;

    #[view]
    public fun get_message(addr: address): string::String acquires MessageHolder {
        assert!(exists<MessageHolder>(addr), error::not_found(ENO_MESSAGE));
        borrow_global<MessageHolder>(addr).message
    }

    public entry fun set_message(account: signer, message: string::String)
    acquires MessageHolder {
        let account_addr = signer::address_of(&account);
        if (!exists<MessageHolder>(account_addr)) {
            move_to(&account, MessageHolder {
                message,
            })
        } else {
            let old_message_holder = borrow_global_mut<MessageHolder>(account_addr);
            let from_message = old_message_holder.message;
            event::emit(MessageChange {
                account: account_addr,
                from_message,
                to_message: copy message,
            });
            old_message_holder.message = message;
        }
    }

    #[test(account = @0x1)]
    public entry fun sender_can_set_message(account: signer) acquires MessageHolder {
        let addr = signer::address_of(&account);
        supra_framework::account::create_account_for_test(addr);
        set_message(account, string::utf8(b"Hello, Blockchain"));

        assert!(
            get_message(addr) == string::utf8(b"Hello, Blockchain"),
            ENO_MESSAGE
        );
    }
}
```
{% endcode %}
{% endstep %}
{% endstepper %}
