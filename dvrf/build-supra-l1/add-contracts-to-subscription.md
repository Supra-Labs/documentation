# Add Contracts to Subscription

After creating your subscription and configuring gas settings, you need to whitelist your consumer contracts. Only whitelisted contracts can request random numbers from your subscription.

{% hint style="info" %}
**Important**: Make sure you've read the Gas Configuration page first. Understanding gas settings is crucial before adding contracts and funds, as your contract-level gas configuration directly impacts performance, costs, and minimum balance requirements
{% endhint %}

{% tabs %}
{% tab title="Via Onchain Contracts" %}
**Whitelisting Your Module**

After deploying your requester module, whitelist it with Supra to enable VRF requests:

```solidity
deposit::init_vrf_module<T>(client: &signer): SupraVRFPermit<T>
```

**Parameters:**

* `client` - Your signer (must be the whitelisted wallet address)
* `T` - Your module's type/struct (e.g., `YourModule`)

**Returns:**

* `SupraVRFPermit<T>` - A capability that authorizes your module to interact with Supra VRF

**Important**: Store the returned `SupraVRFPermit<T>` capability in your module. This permit is required for all VRF requests.
{% endtab %}
{% endtabs %}



#### **Disabling a Contract**

Temporarily stop a module from making requests without removing it:

```solidity
deposit::disable_module<T>(client: &signer)
```

**Example:**

```solidity
// Temporarily disable the module
deposit::disable_module<ClientExample>(&signer);
```

#### **Re-enabling a Contract**

Reactivate a previously disabled module:

```solidity
deposit::enable_module<T>(client: &signer)
```

### **Best Practices**

**Store the Permit Safely**: The `SupraVRFPermit<T>` capability is essential for making VRF requests. Store it in a resource with `key` ability.

**One Permit Per Module**: Each module type can only have one permit. Don't try to create multiple permits for the same module.

**Module-Specific Limits**: Use per-module fee limits to isolate costs, especially for experimental features.



### **Troubleshooting**

**"Module disabled" error**:

* Check that you're using the correct signer (whitelisted wallet)
* Verify the `SupraVRFPermit<T>` is properly stored

**"Insufficient permissions" error**:

* Ensure you called `init_vrf_module<T>()` after deploying your module
* Verify your wallet was whitelisted first

**"Module not whitelisted" error**:

* Ensure you called `init_vrf_module<T>()` after deploying your module
* Verify your wallet was whitelisted first
