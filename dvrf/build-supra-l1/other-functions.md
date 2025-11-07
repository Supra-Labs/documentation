---
hidden: true
---

# Other Functions

Beyond requesting random numbers, Supra dVRF provides additional functions for managing your subscription, monitoring balances, and configuring module settings. These utility functions help you maintain your dVRF integration and optimise your random number generation workflow.

### **Interact with Supra Deposit Module - Supra L1**

The simplest way to interact with the Deposit Module is to use the **Supra CLI** directly or integrate the functions into your Move scripts.

### **Essential Functions**

#### **Subscription Management**

| Function                    | Description                       | Parameters               | Returns | Notes                         |
| --------------------------- | --------------------------------- | ------------------------ | ------- | ----------------------------- |
| is\_client\_whitelisted\_v2 | Checks if a wallet is whitelisted | client\_address: address | bool    | Returns `true` if whitelisted |
|                             |                                   |                          |         |                               |

***

#### **Module Management**

| Function       | Description                  | Parameters                      | Returns | Notes                                        |
| -------------- | ---------------------------- | ------------------------------- | ------- | -------------------------------------------- |
| remove\_module | Permanently removes a module | permit\_cap: SupraVRFPermit\<T> | bool    | Consumes permit capability. Cannot be undone |
|                |                              |                                 |         |                                              |

#### **Transaction Fee Configuration**

{% code overflow="wrap" %}
```solidity
```
{% endcode %}

***

#### **Fund Management**

```
```

***

#### **Balance Information**

```
```

***

#### **Grant Information**

```
```

***

#### **Module Information**

```
```

***

#### **System Information**

```
```

***

### **Complete Example: Balance Monitoring**

move

```solidity
module example::subscription_monitor {
    use std::signer;
    use supra_addr::deposit;
    
    // Check overall subscription health
    #[view]
    public fun get_subscription_status(client_address: address): (
        bool,  // is_whitelisted
        u64,   // total_balance
        u64,   // minimum_balance
        u64,   // effective_balance
        bool,  // needs_deposit
        u64,   // remaining_grant
        u64,   // max_txn_fee
    ) {
        let is_whitelisted = deposit::is_client_whitelisted_v2(client_address);
        let total = deposit::check_client_fund(client_address);
        let minimum = deposit::check_min_balance_client_v2(client_address);
        let effective = deposit::check_effective_balance_v2(client_address);
        let at_minimum = deposit::has_minimum_balance_reached_v2(client_address);
        let grant = deposit::get_client_remaining_grant(client_address);
        let max_fee = deposit::check_max_txn_fee_client_v2(client_address);
        
        (is_whitelisted, total, minimum, effective, at_minimum, grant, max_fee)
    }
    
    // Check module health
    #[view]
    public fun get_module_status<T>(): (bool, ModuleInfo) {
        let is_enabled = deposit::is_module_enabled<T>();
        let module_info = deposit::get_module_info<T>();
        
        (is_enabled, module_info)
    }
    
    // Emergency deposit check and top-up
    public entry fun emergency_check_and_deposit(
        sender: &signer,
        client: address
    ) {
        let at_minimum = deposit::has_minimum_balance_reached_v2(client);
        
        if (at_minimum) {
            let minimum = deposit::check_min_balance_client_v2(client);
            // Deposit to reach 5x minimum
            deposit::deposit_fund_v2(sender, client, minimum * 5);
        }
    }
    
    // Safe withdrawal with balance checks
    public entry fun safe_withdraw(
        sender: &signer,
        amount: u64
    ) {
        let client_addr = signer::address_of(sender);
        let current = deposit::check_client_fund(client_addr);
        let minimum = deposit::check_min_balance_client_v2(client_addr);
        
        // Ensure withdrawal maintains 2x minimum balance
        assert!(current - amount >= minimum * 2, E_INSUFFICIENT_BALANCE);
        
        deposit::withdraw_fund(sender, amount);
    }
}
```

***

### **Important Notes**

* Supra does not provide automatic email alerts for Supra L1 - implement your own monitoring using the view functions
* Your funds are used to pay transaction fees for VRF callback executions
* Always ensure your total balance remains well above the minimum requirement to avoid request failures
* Grant funds are used before deposited funds for callback transaction fees
* All view functions can be called without gas costs
* Module-specific settings must be within wallet-level limits

These functions provide comprehensive control over your Supra dVRF integration on Supra L1, allowing you to manage funds, monitor usage, and configure your setup according to your application's needs.
