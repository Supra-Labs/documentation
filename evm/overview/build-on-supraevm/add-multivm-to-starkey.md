---
description: >-
  Connect your StarKey Wallet to the Supra MultiVM Testnet by adding the custom
  network details and funding your account using the built-in faucet. This setup
  ensures your wallet is ready to deploy and
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Add MultiVM to StarKey

***

{% stepper %}
{% step %}
### <mark style="color:red;">Step 1: Open StarKey Wallet</mark>

Make sure the [StarKey Wallet](https://starkey.app/) extension/app is installed and unlocked.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 2: Navigate to Networks</mark>

Click the **All Networks** at the top of the wallet.\
Select **Add Network** (the **+** icon).
{% endstep %}

{% step %}
### <mark style="color:red;">Step 3: Enter the Network Details</mark>

Use the Supra MultiVM Testnet settings:

* **Network Name:** Supra MultiVM Testnet
* **Network Type:** Ethereum
* **RPC URL:** _Use the URL from the_ [_Network Information_](../network-information.md) _page_
* **Symbol:** SupEVM
* **Chain ID:** _(will auto-fill)_

Click **Add** to save the network.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 4: Fund Your Wallet</mark>

Visit the MultiVM RPC endpoint:

> [https://rpc-multivm.supra.com/docs/#/operations/evm\_faucet](https://rpc-multivm.supra.com/docs/#/operations/evm_faucet)

Scroll to **Faucet > EVM Faucet Endpoint**\
Copy the GET request:

```
curl --request GET \
  --url https://rpc-multivm.supra.com/rpc/v1/wallet/evm_faucet/{address} \
  --header 'Accept: application/json'
```

Replace `{address}` with your StarKey wallet address, then run the command in your terminal.

Example Response:

```
{
 "Accepted": "c8f87d0d-6e96-45a2-9ac9-94ce41e8aae1"
}
```

Your wallet will receive test funds within a few moments.
{% endstep %}
{% endstepper %}
