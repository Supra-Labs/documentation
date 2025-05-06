---
description: Relative network, faucet, and API details.
---

# Network Information

***

## Supra MoveVM

<table><thead><tr><th width="205">Network</th><th width="420">Rest API</th><th>Chain ID</th></tr></thead><tbody><tr><td>Mainnet</td><td>https://rpc-mainnet.supra.com</td><td>8</td></tr><tr><td>Testnet</td><td>https://rpc-testnet.supra.com</td><td>6</td></tr></tbody></table>

### Testnet Faucet

{% code overflow="wrap" %}
```
https://rpc-testnet.supra.com/rpc/v1/wallet/faucet/<ADDRESS>
```
{% endcode %}

***

## Supra EVM

<table><thead><tr><th width="209">Network</th><th width="419">Rest API</th><th>Chain ID</th></tr></thead><tbody><tr><td>Mainnet</td><td>TBA</td><td>TBA</td></tr><tr><td>Testnet</td><td>TBA</td><td>TBA</td></tr><tr><td>Stagingnet</td><td>https://rpc-evmstaging.supra.com/rpc/v1/eth</td><td>119</td></tr></tbody></table>

### Stagingnet Faucet

{% code overflow="wrap" %}
```
curl https://rpc-evmstaging.supra.com/rpc/v1/wallet/evm_faucet/<ADDRESS>
```
{% endcode %}
