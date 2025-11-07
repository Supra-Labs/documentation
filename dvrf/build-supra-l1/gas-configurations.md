# Gas Configurations

Now that you understand how dVRF works and have created your subscription, it's time to configure gas settings properly. Gas configuration is crucial because it determines both your callback performance and minimum balance requirements. Getting this right ensures reliable random number delivery while managing costs effectively.

### Why Gas Configuration Matters

* **Performance**: Higher gas settings = faster callback execution during network congestion
* **Cost Control**: Optimized settings prevent overpaying while ensuring reliability
* **Minimum Balance**: Your gas settings directly calculate the required minimum balance
* **Request Success**: Insufficient gas settings can cause callback failures



### **Supra L1 Transaction Fee Configuration**

Supra dVRF on Supra L1 uses a simplified transaction fee model with configurations at two levels:

#### **Wallet Level (Required)**

**Maximum Transaction Fee** (`max_txn_fee`): The highest transaction fee you'll pay for any callback across all your modules. This is set when whitelisting your wallet address.

#### **Module Level (Optional)**

**Maximum Callback Transaction Fee** (`max_callback_txn_fee`): Transaction fee limit for a specific requester module (must be ≤ max\_txn\_fee)\


### **Setting Transaction Fees**

You are prompted to set these value at the subscription creation stage in the subscription manager UI.  You can also modify them later from the menu. Do you also do the same via onchain functions.\
\
**Wallet level**

```solidity
deposit::whitelist_client_address(client: &signer, max_txn_fee: u64)
```

Example (move):

```solidity
// Whitelist wallet with max transaction fee of 1,000,000 (in smallest unit)
deposit::whitelist_client_address(&signer, 1000000);
```

**Module Level**

For granular control, set different fee limits for specific modules:

```solidity
deposit::update_max_callback_txn_fee<T>(client: &signer, max_callback_txn_fee: u64)
```

#### **Optimising After Testing**

```solidity
// After testing, lower your max fee if actual usage is less
deposit::update_max_txn_fee(&signer, 800000);

// Update specific module if needed
deposit::update_max_callback_txn_fee<MyModule>(&signer, 400000);
```



### Minimum Balance

Your total fund balance should always be significantly higher than the minimum balance for Supra dVRF to accept dVRF requests. Your gas settings directly affect the minimum balance requirement:. you can check your minimum balance in the UI after subscription creation.&#x20;

```
Minimum Balance = Minimum Balance = max_txn_fee × min_request_count
```

**Where:**

* `min_request_count` = Minimum number of requests Supra requires you to be able to afford (typically 30)
*

### **Monitoring Your Funds**

#### **Create a Balance Monitoring Script**

#### **Recommended Alert Thresholds**

* **300% of minimum balance**: Plan your next deposit
* **200% of minimum balance**: Schedule deposit soon
* **100% of minimum balance**: Urgent - new requests will be blocked
* **At minimum balance**: Critical - immediate deposit required

Set up your own monitoring at these levels:



Here's an example structure for monitoring:

```solidity
#[view]
public fun get_subscription_health(client_address: address): (u64, u64, u64, bool) {
    let total = deposit::check_client_fund(client_address);
    let minimum = deposit::check_min_balance_client_v2(client_address);
    let effective = deposit::check_effective_balance_v2(client_address);
    let at_minimum = deposit::has_minimum_balance_reached_v2(client_address);
    
    (total, minimum, effective, at_minimum)
}

```

### Dev tips for better experience

* **Start Conservative**: Begin with higher limits, optimize down over time
* **Test Thoroughly**: Always test your callback gas usage before production
* **Monitor Actively**: Set up alerts for deposit balance warnings at matching threshold levels via UI
* **Update Regularly**: Adjust settings based on network conditions and usage patterns
*   **Plan for Peaks**: Account for network congestion during high-demand periods

    \
