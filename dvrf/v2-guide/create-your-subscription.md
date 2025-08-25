---
hidden: true
---

# Create your subscription

To start using dVRF, you need to create a subscription that will manage your random number requests and handle gas payments for callbacks. You can create a subscription in two ways: using the new web interface or through on-chain functions.



{% tabs %}
{% tab title="Via the web interface" %}
The easiest way to create your dVRF subscription is through our web interface at [supra.com/data/dvrf](https://supra.com/data/dvrf).

#### Prerequisites

* MetaMask or StarKey wallet installed and configured
* Sufficient funds in your wallet for gas fees
* Connected to the appropriate network (see [Available Networks](https://docs.supra.com/dvrf/networks))

#### Steps

1. **Connect Your Wallet**
   * Navigate to [supra.com/data/dvrf](https://supra.com/data/dvrf)
   * Click "Connect Wallet"
   * Select either MetaMask or StarKey wallet
   * Approve the connection request\

2. **Access Subscription Management**
   * Click on "My Subscription" in the interface
   * If you don't have a subscription yet, you'll see an option to create one\

3.  **Create New Subscription**

    * Click "Create Subscription"
    * Fill in the required details:

    **For EVM Chains:**

    * **Max Gas Price**: Maximum gas price you're willing to pay for callback transactions.
    * **Max Gas Limit**: Maximum gas limit allocated for callback transactions.

    **For Supra L1:**

    * **Max Gas Fee**: Maximum Gas fee you are willing to spend for your callback transaction


4. **Review and Confirm**
   * Review your Gas configuration settings
   * Confirm the transaction in your wallet
   * Wait for transaction confirmation\

5. **Verification**
   * Once confirmed, your subscription will be active
   * You can view your subscription details in the "My Subscription" section
   * Your wallet address is now registered as the subscription manager


{% endtab %}

{% tab title="Via Onchain functions" %}
For developers who prefer programmatic subscription creation, you can interact directly with the smart contracts.\
\
EVM Chains

```solidity
// Interface for dVRF 3.0 Deposit Contract
interface IDeposit {
    function addClientToWhitelist(
        uint128 maxGasPrice, 
        uint128 maxGasLimit
    ) external payable;
}
```



Supra L1

```solidity
/ Create subscription on Supra L1
public entry fun create_subscription(
    sender: &signer,
    max_gas_fee: u64,
    initial_deposit: u64
) {
    // Call the deposit module to create subscription
    deposit::add_client_to_whitelist(sender, max_gas_fee);
    deposit::deposit_fund(sender, initial_deposit);
}

```

#### Verification

**Check subscription status**: Use web interface or `getSubscriptionByClient()` function

After creating your subscription, verify it's working correctly:



\

{% endtab %}
{% endtabs %}

Still not comfortable with Minimum Balance,  Max Gas Limit, Max Gas price or Max Gas Fee? In the next section we talk about it.&#x20;

