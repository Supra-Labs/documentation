---
description: >-
  Connect your StarKey Wallet to the Supra MultiVM Testnet by adding the custom
  network details and funding your account using the built-in faucet. This setup
  ensures your wallet is ready to deploy and
---

# Add MultiVM to StarKey

### Step 1: Open StarKey Wallet

Make sure the [StarKey Wallet](https://starkey.app/) extension/app is installed and unlocked.

### Step 2: Navigate to Networks

Click the **All Networks** at the top of the wallet.\
Select **Add Network** (the **+** icon).

### Step 3: Enter the Network Details

Use the Supra MultiVM Testnet settings:

* **Network Name:** Supra MultiVM Testnet
* **Network Type:** Ethereum
* **RPC URL:** _Use the URL from the Network Information page_
* **Symbol:** SupEVM
* **Chain ID:** _(both provided IDs work)_

Click **Add** to save the network.

### Step 4: Fund Your Wallet

Visit the MultiVM RPC endpoint:

> &#x20;[https://rpc-multivm.supra.com/](https://rpc-multivm.supra.com/)

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
