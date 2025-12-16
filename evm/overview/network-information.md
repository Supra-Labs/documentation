---
description: Relative network, faucet, and API details.
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

# Network Information

***

<table data-full-width="false"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><strong>Network Name</strong></td><td><strong>SupraEVM MultiVM Testnet</strong></td></tr><tr><td><strong>RPC URL Endpoints</strong></td><td><strong>learn more below</strong></td></tr><tr><td>*<strong>Direct RPC Endpoint</strong></td><td><a href="https://rpc-multivm.supra.com/rpc/v1/eth"><code>https://rpc-multivm.supra.com/rpc/v1/eth</code></a></td></tr><tr><td>*<strong>Wallet &#x26; Tooling Integration Endpoint</strong></td><td><a href="https://rpc-multivm.supra.com/rpc/v1/eth/wallet_integration"><code>https://rpc-multivm.supra.com/rpc/v1/eth/wallet_integration</code></a></td></tr></tbody></table>

***

## <mark style="color:red;">Notes for Third-Party Tooling</mark>&#x20;

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
