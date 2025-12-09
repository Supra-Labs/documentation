---
description: >-
  Set up Remix, connect StarKey Wallet, and deploy your first smart contract on
  SupraEVM.
---

# Start Building

### <mark style="color:red;">Step 1: Open Remix IDE</mark>

1. Visit [**https://remix.ethereum.org**](https://remix.ethereum.org)
2. On the left sidebar, click **Deploy & Run Transactions**
3. Click **Customize this list**
4. Select **Injected Provider – StarKey Wallet**
5. Ensure your wallet is connected to **Supra MultiVM**

A confirmation modal will appear in StarKey Wallet.

### <mark style="color:red;">Step 2: Create a New Solidity File</mark>

1. Click the **File Explorer** icon
2. Click **New File**
3. Name it:

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

### <mark style="color:red;">Step 3: Compile the Contract</mark>

1. Click the **Solidity Compiler** tab
2. Choose compiler version: **0.8.x**
3. Click **Compile SimpleSupraStorage.sol**

A green checkmark means success.

### <mark style="color:red;">Step 4: Deploy to SupraEVM MultiVM</mark>

1. Open **Deploy & Run Transactions**
2. Under **Environment**, choose:

**Injected Web3 (StarKey Wallet)**

3. Make sure StarKey Wallet is switched to:

✔ **Supra MultiVM Testnet**\
✘ Not Ethereum, BNB, or any other network

4. Click **Deploy**
5. Confirm the transaction in StarKey Wallet

Your contract is now deployed on SupraEVM.

### <mark style="color:red;">Step 6: Verify Transactions on the Explorer</mark>

Every action (deploy, set, get) is recorded on Supra’s MultiVM Explorer.

Paste your transaction hash or contract address into:

[**Supra MultiVM Block Explorer**](https://multivm.suprascan.io/)

You can view:

* Gas used
* Function calls
* Storage changes
* Contract creator
* Execution results

