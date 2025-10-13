# Add Contracts to subscription

After creating your subscription and configuring gas settings, you need to whitelist your consumer contracts. Only whitelisted contracts can request random numbers from your subscription.

{% hint style="info" %}
**Important**: Make sure you've read the Gas Configuration page first. Understanding gas settings is crucial before adding contracts and funds, as your contract-level gas configuration directly impacts performance, costs, and minimum balance requirements
{% endhint %}

{% tabs %}
{% tab title="Via Subscription Manager UI" %}
The easiest way to manage your contracts is through the web interface at [supra.com/data/dvrf](https://supra.com/data/dvrf).

#### Steps

* **Navigate to Contract Management**
  * Go to [supra.com/data/dvrf](https://supra.com/data/dvrf)
  * Connect your wallet (subscription owner)
  * Click on "My Subscription"
* **Add New Contract**
  * Click "Add Contract"
  * Enter your contract address
  * Configure gas settings for this specific contract
*   **Configure Contract Gas Settings** **For EVM Chains:**

    * **Callback Gas Price**: Must be ≤ your subscription's maxGasPrice
    * **Callback Gas Limit**: Must be ≤ your subscription's maxGasLimit


*   **Submit and Confirm**


{% endtab %}

{% tab title="Via Onchain Contracts" %}
For EVM Chains

```solidity
solidity// Interface for adding contracts
interface IDeposit {
    function addContractToWhitelist(
        address contractAddress,
        uint128 callbackGasPrice,
        uint128 callbackGasLimit
    ) external;
}
```


{% endtab %}
{% endtabs %}



### Remove Contracts from subscription

You can remove already whitelisted contracts via subscription manager UI or via onchain function.

```solidity
// Remove contract from whitelist
removeContractFromWhitelist(contractAddress);
```

\
