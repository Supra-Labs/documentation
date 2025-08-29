# Gas Configurations

Now that you understand how dVRF works and have created your subscription, it's time to configure gas settings properly. Gas configuration is crucial because it determines both your callback performance and minimum balance requirements. Getting this right ensures reliable random number delivery while managing costs effectively.

### Why Gas Configuration Matters

* **Performance**: Higher gas settings = faster callback execution during network congestion
* **Cost Control**: Optimized settings prevent overpaying while ensuring reliability
* **Minimum Balance**: Your gas settings directly calculate the required minimum balance
* **Request Success**: Insufficient gas settings can cause callback failures

### Chain Specific Gas Configuration

{% tabs %}
{% tab title="EVM Chains" %}
Supra dVRF in EVM chains has four gas parameters configured at two levels.  If per contract configurations not set,  it will inherit values from subscription level limits.&#x20;

**Subscription Level (Required)**

* **Max Gas Price**: Highest gas price you'll pay for any callback
* **Max Gas Limit**: Maximum gas limit for any callback execution

**Per-Contract Level (Optional )**

* **Callback Gas Price**: Gas price for specific contract (≤ maxGasPrice)
* **Callback Gas Limit**: Gas limit for specific contract (≤ maxGasLimit)\


#### Change via UI

You are prompted to set these value at the subscription creation stage.  You can also modify them later from the menu.&#x20;

#### Change via On chain functions

```solidity
// Setting subscription-level at the time of subscription creation
addClientToWhitelist(maxGasPrice, maxGasLimit);

// Setting per-contract customization at contract addition
addContractToWhitelist(contractAddress, callbackGasPrice, callbackGasLimit);


// Update maximum gas price
updateMaxGasPrice(newMaxGasPrice);

// Update maximum gas limit  
updateMaxGasLimit(newMaxGasLimit);

// Update specific contract's gas price
updateCallbackGasPrice(contractAddress, newGasPrice);

// Update specific contract's gas limit
updateCallbackGasLimit(contractAddress, newGasLimit);
```
{% endtab %}

{% tab title="Supra L1" %}
&#x20;Supra L1 uses simplified configuration with a single parameter:

* **Max Transaction Fee**: Combined parameter that handles both price and limit
{% endtab %}
{% endtabs %}



### Minimum Balance

Your total fund balance should always be higher than the minimum balance for Supra dVRF to accept dVRF requests. Your gas settings directly affect the minimum balance requirement:. you can check your minimum balance in the UI after subscription creation.&#x20;

```
Minimum Balance = 30 requests × maxGasPrice × (maxGasLimit + verificationGas)
```

**Verification Gas**: Additional gas consumed by BLS signature verification and random number generation on-chain. This is automatically added to your callback gas limit during minimum balance calculation

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
