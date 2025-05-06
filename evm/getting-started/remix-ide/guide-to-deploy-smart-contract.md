---
description: >-
  Follow this step-by-step guide to write, compile, deploy, and interact with a
  smart contract on SupraEVM using Remix IDE. Learn how to set up your contract,
  verify deployment, and test functionality.
---

# Guide to Deploy Smart Contract

***

### Create a New File

* Click on the **File Explorers** icon on the left sidebar.
* Click on the **New File** button.
* Name the file `SimpleSupraStorage.sol`.

### Write the Smart Contract

Copy and paste the following Solidity code into the `SimpleSupraStorage.sol` file:

### What is this Contract Doing?

The `SimpleSupraStorage` contract allows you to store a number (using the `set` function) and retrieve it (using the `get` function). This basic example is perfect for learning to write and deploy Solidity smart contracts in SupraEVM

### What are the Functions Doing?

`storedData` is a public variable storing a single `uint256` value. As a public variable, anyone can read this value by calling the automatically generated `storedData()` function.

The `set` function takes a `uint256` value as an input and stores it in the `storedData` variable. This function is public, meaning anyone can call it to change the value of `storedData`.

The `get` function returns the current value of the `storedData` variable. It's marked as `view`, meaning it doesn't modify the state. It is also public, so anyone can call it to read the value of `storedData`.

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

### Compile the Smart Contract

* Click on the **Solidity Compiler** icon on the left sidebar.
* Select the appropriate Solidity compiler version (e.g., `0.8.0`).
* Click on the **Compile** `SimpleStorage.sol` button.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

### Deploy the Smart Contract

* Click on the **Deploy & Run Transactions** icon on the left sidebar.
* Under the **Environment** dropdown, select **Injected Web3** (this will connect your Starkey Wallet to Remix).
* Ensure that your Starkey Wallet is connected to the Supra EVM network.
* Click on the **Deploy** button.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Make sure your Starkey wallet is switched to Supra EVM Network and not any other before it. You can verify it by checking the network selected under an environment in the top section.
{% endhint %}

### Interact with the Deployed Smart Contract

* After deployment, you'll see your smart contract under the Deployed Contracts section.
* You can now interact with the contract.

### Verify Transaction on Block Explorer

You can click the transaction hash to open Supra's EVM Block Explorer and view all the details of your contract deployment and interactions.

[Supra's EVM Block Explorer](https://evm.suprascan.io/)
