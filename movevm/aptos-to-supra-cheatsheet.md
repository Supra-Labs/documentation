# Aptos to Supra Cheatsheet

This quick-reference guide helps you take what you know from the Aptos framework and translate it into building Move modules with the Supra framework.

### Framework Overview

Both frameworks share a few similar foundational designs of code. As such, many modules and patterns are familiar, but the migration requires updating import paths, constants, and sometimes function names to reflect the Supra ecosystem.

#### What’s Different?

* **Module Prefixes:** All module references in Aptos starting with `aptos_framework::` are replaced with `supra_framework::` in Supra.
* **Additional Features**: Supra includes extra modules (e.g., for randomness, advanced governance) that expand functionality.
* **Configuration and Constants**: Some constants, such as domain separators or error codes, have been updated to reflect the Supra ecosystem.

## Import Paths and Their Changes

There are differences in import paths that set Supra apart:

#### Aptos Example:

```
use aptos_framework::account::{Self, SignerCapability, new_event_handle, create_resource_address};
use aptos_framework::aptos_coin::AptosCoin;
```

#### Supra Example:

```
use supra_framework::account::{Self, SignerCapability, new_event_handle, create_resource_address};
use supra_framework::supra_coin::SupraCoin;

```

{% hint style="success" %}
All your module imports should replace `aptos_framework` with `supra_framework` accordingly.
{% endhint %}

## Module and Naming Conventions

Table mapping **a few key modules and their counterparts** between Aptos & Supra Move Framework

<table><thead><tr><th width="159.5">Functionality</th><th width="292.35723876953125">aptos_framework</th><th width="299.0357666015625">supra_framework</th></tr></thead><tbody><tr><td>Account Management</td><td>aptos_framework::account</td><td>supra_framework::account</td></tr><tr><td>Coin Operations</td><td>aptos_coin:: AptosCoin</td><td>supra_coin::SupraCoin</td></tr><tr><td>Multisig &#x26; Voting</td><td>aptos_framework::multisig_account</td><td>supra_framework::multisig_account</td></tr><tr><td>Fungible Assets</td><td>aptos_framework::fungible_asset</td><td> supra_framework::fungible_asset</td></tr><tr><td>Event Handling</td><td>aptos_framework::event</td><td>supra_framework::event</td></tr><tr><td>Governance</td><td>aptos_framework::governance</td><td>supra_framework::supra_governance</td></tr><tr><td>Staking/Consensus</td><td>aptos_framework::staking_config</td><td>supra_framework::staking_config</td></tr><tr><td>Randomness Configuration</td><td>Customized or external implementation</td><td>[dependencies.SupraVrf]<br>git = "https://github.com/Entropy-Foundation/vrf-interface"<br><br><sup><em><strong>Supra offers its own Supra dVRF services for developers to integrate</strong></em></sup></td></tr></tbody></table>

{% hint style="danger" %}
The Fungible Assets Module has Migration disabled on Mainnet, kindly use the coin\_wrapper for using FA Standard in any Supra project from [**THIS REPO**](https://github.com/Entropy-Foundation/aptos-core/blob/dev/aptos-move/move-examples/swap/sources/coin_wrapper.move)
{% endhint %}

## Detailed Code Template Comparisons

Here is an example of diff in code for both frameworks, **let’s take a look at Multisig\_account module from both Frameworks**:

#### Aptos Version

```
Aptos Version:
module aptos_framework::multisig_account {
    use aptos_framework::account::{Self, SignerCapability, new_event_handle, create_resource_address};
    use aptos_framework::aptos_coin::AptosCoin;
    // Other imports ...

    const DOMAIN_SEPARATOR: vector<u8> = b"aptos_framework::multisig_account";

    // Error codes
    const EDUPLICATE_OWNER: u64 = 1;
    const EACCOUNT_NOT_MULTISIG: u64 = 2002;
    const ENOT_OWNER: u64 = 2003;
    const EPAYLOAD_CANNOT_BE_EMPTY: u64 = 4;
    const ENOT_ENOUGH_OWNERS: u64 = 5;
    const ETRANSACTION_NOT_FOUND: u64 = 2006;

    /// Creates a new multisig account with specified owners.
    public fun create_multisig_account(account: &signer, owners: vector<address>) {
        // Validate and setup multisig account...
    }
}
```

#### Supra Version

```
module supra_framework::multisig_account {
    use supra_framework::account::{Self, SignerCapability, new_event_handle, create_resource_address};
    use supra_framework::supra_coin::SupraCoin;
    // Other updated imports ...

    const DOMAIN_SEPARATOR: vector<u8> = b"supra_framework::multisig_account";

    // Error codes remain conceptually similar
    const EDUPLICATE_OWNER: u64 = 1;
    const EACCOUNT_NOT_MULTISIG: u64 = 2002;
    const ENOT_OWNER: u64 = 2003;
    const EPAYLOAD_CANNOT_BE_EMPTY: u64 = 4;
    const ENOT_ENOUGH_OWNERS: u64 = 5;
    const ETRANSACTION_NOT_FOUND: u64 = 2006;

    /// Creates a new multisig account with specified owners.
    public fun create_multisig_account(account: &signer, owners: vector<address>) {
        // Implementation logic for Supra. The business logic is generally identical;
        // only the references and potentially some internal data structures have changed.
    }
}
```

{% hint style="warning" %}
Aside from updating the import paths and the domain separator, the business logic and error codes often remain the same. However, for advanced operations, check the Supra-specific documentation for changes in transaction handling or resource setup.
{% endhint %}

## Additional Modules

Supra extends many functionalities to support a broader set of on-chain applications. Here’s a closer look at some specialized modules:

#### Governance & Voting

* **Aptos Governance**: Typically found under `aptos_framework::governance`
* **Supra Governance**: Mapped to `supra_framework::supra_governance`&#x20;

#### Randomness

* **Aptos**: May rely on external or manually configured randomness.
* **Supra**: Supra offers its own Supra dVRF services for developers to integrate: [https://docs.supra.com/oracles/dvrf](https://docs.supra.com/oracles/dvrf)

<table data-view="cards"><thead><tr><th></th><th data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Link to Framework Repo</strong></td><td><a href="https://github.com/Entropy-Foundation/aptos-core/tree/dev/aptos-move/framework/supra-framework">https://github.com/Entropy-Foundation/aptos-core/tree/dev/aptos-move/framework/supra-framework</a></td></tr></tbody></table>
