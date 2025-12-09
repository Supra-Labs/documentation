---
description: Relative network, faucet, and API details.
hidden: true
---

# Network Information

{% hint style="info" %}
**MultiVM Testnet for SupraEVM Coming soon! (Early access for Devs via Discord)**
{% endhint %}

|                                       |                                                                                                                              |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Network Name                          | **SupraEVM MultiVM Testnet**                                                                                                 |
| RPC URL Endpoints                     | learn more below                                                                                                             |
| Direct RPC Endpoint                   | [`https://rpc-multivm.supra.com/rpc/v1/eth`](https://rpc-multivm.supra.com/rpc/v1/eth)                                       |
| Wallet & Tooling Integration Endpoint | [`https://rpc-multivm.supra.com/rpc/v1/eth/wallet_integration`](https://rpc-multivm.supra.com/rpc/v1/eth/wallet_integration) |

## Notes for Third-Party Tooling&#x20;

Supra’s EVM chain uses Moonshot consensus, which finalizes blocks in **sub-second time**.\
Because of this, **Supra EVM only supports `BlockTag=latest`.**

Tools like Foundry or Hardhat automatically request a specific block height, which the standard RPC endpoint will **reject**.

To support both direct RPC calls and tools we don’t control, we provide two endpoints:

**Direct RPC Endpoint**\
`https://rpc-multivm.supra.com/rpc/v1/eth`\
Use this when calling the RPC directly or building custom tooling for Supra.

**Wallet & Tooling Integration Endpoint**\
`https://rpc-multivm.supra.com/rpc/v1/eth/wallet_integration`\
Use this when working with Foundry, Hardhat, or 3rd-party wallets.\
This endpoint ignores block-height tags and always returns the latest block state, so no extra configuration is required.
