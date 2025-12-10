---
description: >-
  Set up Remix, connect StarKey Wallet, and deploy your first smart contract on
  SupraEVM.
---

# Start Building

***

{% stepper %}
{% step %}
### <mark style="color:red;">Step 1: Open Remix IDE</mark>

Visit [**https://remix.ethereum.org**](https://remix.ethereum.org)\
On the left sidebar, click **Deploy & Run Transactions**\
Click **Customize this list**\
Select **Injected Provider – StarKey Wallet**\
Ensure your wallet is connected to **Supra MultiVM**\
A confirmation modal will appear in StarKey Wallet.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 2: Create a New Solidity File</mark>

Click the **File Explorer** icon\
Click **New File**\
Name it:

```
SimpleSupraStorage.sol
```

Paste the following contract:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleSupraStorage {
    uint256 public storedData;

    function set(uint256 x) public {
        storedData = x;
    }

    function get() public view returns (uint256) {
        return storedData;
    }
}
```

#### What this contract does:

* `storedData`: stores one number
* `set(x)`: updates the stored number
* `get()`: returns the stored number

Perfect for learning SupraEVM deployment flow.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 3: Compile the Contract</mark>

Click the **Solidity Compiler** tab\
Choose compiler version: **0.8.x**\
Click **Compile SimpleSupraStorage.sol**\
A green checkmark means success.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 4: Deploy to SupraEVM MultiVM</mark>

Open **Deploy & Run Transactions**\
Under **Environment**, choose:

**Injected Web3 (StarKey Wallet)**

* Make sure StarKey Wallet is switched to:\
  ✔ **Supra MultiVM Testnet**\
  ✘ Not Ethereum, BNB, or any other network

Click **Deploy**\
Confirm the transaction in StarKey Wallet

**Your contract is now deployed on SupraEVM.**
{% endstep %}

{% step %}
### <mark style="color:red;">Step 6: Verify Transactions on the Explorer</mark>

Every action (deploy, set, get) is recorded on Supra’s MultiVM Explorer.\
Paste your transaction hash or contract address into:

&#x20;\> [**Supra MultiVM Block Explorer**](https://multivm.suprascan.io/) **<**

You can view:

* Gas used
* Function calls
* Storage changes
* Contract creator
* Execution results
{% endstep %}
{% endstepper %}

