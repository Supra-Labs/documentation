# Create your subscription

To start using dVRF, you need to create a subscription that will manage your random number requests and handle gas payments for callbacks. You can create a subscription in two ways: using the new web interface or through on-chain functions.

{% tabs %}
{% tab title="Via the web interface" %}
The easiest way to create your dVRF subscription is through subscription manager UI at [supra.com/data/dvrf](https://supra.com/data/dvrf). (Currently testnet only).

#### Prerequisites

* StarKey wallet installed and configured.
* Sufficient funds in your wallet for gas fees.
* Connected to the appropriate network (see [Available Networks](https://docs.supra.com/dvrf/networks))

#### Steps

1. **Connect Your Wallet**
   * Navigate to [supra.com/data/dvrf](https://supra.com/data/dvrf)
   * Click "Connect Wallet"
   * Approve the connection request\

2. **Access Subscription Management**
   * Click on "My Subscription" in the interface.
   * If you don't have a subscription yet, you'll see an option to create one.\

3.  **Create New Subscription**

    * Fill in the required details.
      * **Max Transaction Fee**: Maximum gas fee you're willing to pay for callback transactions.
    * Click "Create Subscription"


4. **Review and Confirm**
   * Review your Gas configuration settings.
   * Confirm the transaction in your wallet.
   * Wait for transaction confirmation.\

5.  **Verification**

    * Once confirmed, your subscription will be active.
    * You can view your subscription details in the "My Subscription" section.
    * Your wallet is now registered as the subscription manager.


6. **Whats next?**&#x20;
   1. **Supra Credits**: Supra automatically provides a default grant for every subscriber to cover initial callback transaction fees.  This will be displayed as Supra Credits in Subscription Manager UI. \
      This can only be used to fund dVRF requests. Once this grant is over, you need to deposit $SUPRA tokens to continue.&#x20;
   2. **Module Whitelisting**: You can now whitelist dVRF requester modules under this wallet address.
{% endtab %}

{% tab title="Via Onchain functions" %}
For developers who prefer programmatic subscription creation (Supra MoveVM), you can interact directly with the smart contracts.

```solidity
deposit::whitelist_client_address(client: &signer, max_txn_fee: u64)
```

**Parameters:**

* `client` - Your signer/wallet that will be whitelisted
* `max_txn_fee` - The maximum transaction fee (in the smallest unit) beyond which callback transactions won't be sent. This helps you control costs.

#### Verification

**Check subscription status**: Use web interface or `is_client_whitelisted_v2` function.

```solidity
deposit::is_client_whitelisted_v2(client_address: address): bool
```

After creating your subscription, verify it's working correctly.
{% endtab %}
{% endtabs %}



Still not comfortable with Minimum Balance,  Max Gas Limit, Max Gas price or Max Gas Fee? In the next section we talk about it.&#x20;

