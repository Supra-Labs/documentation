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



### Deposit Balance Alerts

The system automatically sends email notifications when your  deposit balance drops to critical levels:

* **300% of minimum balance**: Early warning - good time to plan your next deposit
* **100% of minimum balance**: Critical alert - new requests will be blocked soon
* **25% of minimum balance**: Final warning - immediate action required

💡 **Good news**: These alert thresholds can be changed via UI to match your operational needs.\


### Dev tips for better experience

* **Start Conservative**: Begin with higher limits, optimize down over time
* **Test Thoroughly**: Always test your callback gas usage before production
* **Monitor Actively**: Set up alerts for deposit balance warnings at matching threshold levels via UI
* **Update Regularly**: Adjust settings based on network conditions and usage patterns
*   **Plan for Peaks**: Account for network congestion during high-demand periods

    \
