---
description: >-
  Configure Hardhat to connect with SupraEVM, write a Solidity smart contract,
  and create a deployment script to automate deployment using Hardhat scripts.
---

# Write Smart Contract

***

### Configure Hardhat for SupraEVM

Update `hardhat.config.js` to define the **SupraEVM network**, specifying the RPC URL, chain ID, and private key to enable seamless contract deployment and interaction.

* Change your hardhat.config.js file and add the following configuration:

```
require("@nomicfoundation/hardhat-toolbox");

module.exports = {
  solidity: "0.8.4",
  networks: {
    supra: {
      url: "https://rpc-evmstaging.supra.com/rpc/v1/eth",
      chainId: 119,
      accounts: [`0xPRIVATE_KEY`]
    }
  }
};

```

### Create a Smart Contract File

Define a **Solidity smart contract** in `SimpleStorage.sol` to store and retrieve a `uint256` value, providing a basic example for deployment on **SupraEVM**.

* Create a `SimpleStorage.sol` File

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 public storedData;

    function set(uint256 x) public {
        storedData = x;
    }

    function get() public view returns (uint256) {
        return storedData;
    }
}

```

### Create a Deployment Script

Write a **deployment script** in `deploy.js` to automate smart contract deployment on **SupraEVM** using **Hardhat**, enabling seamless contract execution and interaction.

* Create a `scripts` directory and a file named `deploy.js`:

```
const { ethers } = require("hardhat");

async function main() {
    const [deployer] = await ethers.getSigners();
    console.log("Deploying contracts with the account:", deployer.address);

    const SimpleStorage = await ethers.getContractFactory("SimpleStorage");
    
    console.log("Deploying SimpleStorage...");
    const simpleStorage = await SimpleStorage.deploy();

    // Directly access the target property
    console.log("Contract Address from target property:", simpleStorage.target);
}

main().catch((error) => {
    console.error("Error:", error);
    process.exitCode = 1;
});

```
