---
description: >-
  Initialize and configure a Foundry project for SupraEVM by setting up the
  directory, configuring the foundry.toml file, and defining the RPC URL, chain
  ID, and private key for deployment.
---

# Create Foundry Project

***

### Initialize and Set Up Your Project Directory

Create and navigate to your **Foundry** project directory, similar to Hardhat, to prepare for smart contract development.

```
forge init foundry-project
cd foundry-project
```

### Configure foundry.toml

Set up the **Foundry** configuration by specifying the **SupraEVM RPC URL**, chain ID, and your private key to enable seamless deployment and interaction with smart contracts.

```
[profile.supra]
rpc_url = "https://rpc-evmstaging.supra.com/rpc/v1/eth"
chain_id = 119
private_key = "0xYourPrivateKey"
```
