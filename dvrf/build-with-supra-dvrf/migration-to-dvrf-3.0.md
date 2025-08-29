---
description: >-
  This page introduces the new features of Supra dVRF 3.0 and provides migration
  steps. Clients are encouraged to migrate at their convenience before the VRF
  2.0 contracts are phased out soon.
---

# Migration to dVRF 3.0

## Why Migrate to dVRF 3.0?

Moving to VRF 3.0 offers several key advantages over the previous version, especially for users focused on reliability, customization, and future-proofing their integration.

dVRF 3.0 introduces major upgrades focused on reliability, configurability, and developer experience.&#x20;

{% hint style="info" %}
#### **Check all the New Features & Benefits introduced in dVRF 3.0 Below:**
{% endhint %}

<details>

<summary><strong>What’s New in dVRF 3.0?</strong> </summary>

#### Request Retry Mechanism

Failed callback transactions (due to insufficient gas) are automatically retried every 6 hours, for up to 48 hours. Greatly improves reliability without manual resubmission.

#### Custom Callback Gas Settings

Clients can now set: `callbackGasPrice` and `callbackGasLimit`. This gives full control over callback execution costs and helps adapt to varying network conditions.

#### Request & Response Tracking

Every client now has counters to track: Total RNG requests made, and total responses fulfilled.

#### Self-whitelisting

Clients can whitelist themselves, specifying “maxGasPrice” and “maxGasLimit” which acts as default values for their contracts.

#### On-chain Request Validation

A hash of request parameters is stored and validated during callbacks for data integrity: Includes nonce, caller, clientSeed, gas details, etc. It prevents tampering and enhances security.

#### Upgradeable & Migration-Friendly Deposit Contract

The Deposit contract is now upgradeable and backward-compatible with older versions. It supports seamless client migration, dynamic gas balance requirements, auto-whitelisting during migration

####

#### Dynamic Minimum Balance Enforcement

“`minBalanceLimit`” for a client is calculated dynamically as follows:

```solidity
uint128 minBalanceLimit = minRequests * _maxGasPrice * (_maxGasLimit +  verificationGasValue);
```

#### Introduction of “supraMinimumPerTx”

It is the gas used by '`generateRngCallback()`' for transaction initialization and calldata. If a transaction fails due to insufficient funds, “`supraMinimumPerTx`” is deducted from the client.

#### Client removal

`removeClientFromWhitelist(address _clientAddress, bool _forceRemove)` removes the client and their contracts. It transfers back the client fund if no pending requests exist or transfers the client fund to “`supraFund`” if pending requests exist and “`_forceRemove`” is true.

</details>

<details>

<summary><strong>Benefits of dVRF 3.0</strong></summary>

### Improved Reliability with Retry Mechanism

If a random number callback fails due to insufficient gas funds, the RequestRetry mechanism caches it and retries every 6 hours (for up to 48 hours). This significantly reduces the chance of missed callbacks.

### Custom Gas Controls for Callback Transactions&#x20;

Users can now specify both callbackGasPrice and callbackGasLimit, giving more control over costs and ensuring successful delivery during network volatility.

### Enhanced Security & Integrity

On-chain calldata hash verification prevents tampering and validates the request origin, seed, and other key parameters before fulfilling a callback.

### Dynamic Minimum Balance Enforcement

Prevents underfunded requests by enforcing a minimum balance, calculated dynamically.

### Self-whitelisting

Clients can whitelist themselves, specifying “maxGasPrice” and “maxGasLimit” which acts as default values for their contracts

</details>

### Who Needs to Migrate?

You need to migrate to VRF 3.0 if you are currently using VRF 2 and want access to VRF 3.0 features and improvements.

***

## Migration Guide

### Code for existing clients to Migrate

Before migrating, the client must withdraw their existing funds from the old Deposit contract.&#x20;

The client must decide the following values:

1. `maxGasPrice`— the maximum callback gas price they are willing to pay
2. `maxGasLimit`— the maximum callback gas limit with which the callback transaction can get executed.

{% hint style="success" %}
The client can call getMinBalanceLimit(maxGasPrice, maxGasLimit) to compute their minimum balance required:

**`function getMinBalanceLimit(uint128 _maxGasPrice, uint128 _maxGasLimit)`** &#x20;

external view returns (`uint128`);
{% endhint %}

{% hint style="info" %}
**You can learn more about the** `maxGasPrice` **&** `maxGasLimit`  **functions from** [**Here.**](https://docs.supra.com/oracles/dvrf/vrf-subscription-model#how-to-determine-max-gas-limit-and-max-gas-price-in-vrf-3.0)
{% endhint %}

#### Interface to interact with the Deposit contract

```solidity
interface IDeposit {
   function getMinBalanceLimit(uint128 maxGasPrice, uint128 maxGasLimit) external view returns (uint128);
}
contract MigrateToVRF3 {
   IDeposit public deposit;  // The address of the Deposit contract (VRF 3.0)
   constructor(address _deposit) {
       deposit = IDeposit(_deposit);
   }
   /// @notice Get the minimum balance required.
   function checkMinRequiredBalance(uint128 maxGasPrice, uint128 maxGasLimit) external view returns (uint128) {
       return deposit.getMinBalanceLimit(maxGasPrice, maxGasLimit);
   } }
```

{% hint style="info" %}
Clients need to call `“migrateClient(uint128 _maxGasPrice, uint128 _maxGasLimit) payable`”, specifying the max gas price, max gas limit for their contracts and sending along the ether to deposit. \
\
During the migration process, users must ensure their deposit amount is significantly higher than the minimum balance returned by the function: `deposit.getMinBalanceLimit(maxGasPrice, maxGasLimit)` \
\
The `Minimum Balance Limit` is the threshold below which the client can no longer issue new VRF requests. If the client’s balance drops to or near this limit, VRF functionality will be blocked, potentially affecting operations or availability.\
\
They can only migrate before the end of migration time.

`function migrateClient(uint128 _maxGasPrice, uint128 _maxGasLimit) external payable;`
{% endhint %}

{% hint style="success" %}
Please refer to [**THIS REPO**](https://github.com/Entropy-Foundation/supra-vrf-examples/tree/master/supra_examples) for Sample Contract with VRF3 Implementation.
{% endhint %}

***

## Self-whitelisting Guide

{% tabs %}
{% tab title="Via Deposit ContractFunction" %}
#### **Requirements:**

* You must not already be whitelisted.
* `_maxGasPrice` and `_maxGasLimit` must be greater than 0.

#### Clients can call the “`addClientToWhitelist`” function to whitelist themselves:

```solidity
function addClientToWhitelist(uint128 _maxGasPrice, uint128 _maxGasLimit) 
external;
```
{% endtab %}

{% tab title="Via Subscription Manager" %}
1. Visit [https://supra.com/data/dvrf](https://supra.com/data/dvrf)
2. Select the network from the network list.
3. Click on “Create new subscription” button.
4. Enter the project name, email address, telegram handle, max gas price (in Gwei) and max gas limit.&#x20;
5. Click “Create subscription”
{% endtab %}
{% endtabs %}

### **Whitelist Your Consumer Contracts**

The parameter this function takes is the User’s contract address along with `callbackGasPrice` and `callbackGasLimit` for the contract which should be smaller than the `maxGasPrice`and `maxGasLimit` respectively.

<pre class="language-solidity"><code class="lang-solidity"><strong>addContractToWhitelist(address _contractAddress, uint128 _callbackGasPrice, uint128 _callbackGasLimit)
</strong></code></pre>

{% hint style="info" %}
**You can learn more about the** `callbackGasPrice` and `callbackGasLimit`  **functions from** [**Here.**](https://docs.supra.com/oracles/dvrf/vrf-subscription-model#what-is-the-callback-gas-price)
{% endhint %}

***

## Comparing dVRF 2 vs dVRF 3.0

<table><thead><tr><th width="249">Feature </th><th>dVRF 2</th><th>dVRF 3</th></tr></thead><tbody><tr><td>Client Whitelisting</td><td>Manual only (admin-controlled)</td><td>Self-whitelisting enabled with maxGasPrice and maxGasLimit inputs</td></tr><tr><td>Min Balance Calculation</td><td>Static or fixed value</td><td>Dynamically computed</td></tr><tr><td>Upgradeable Contracts</td><td>Only Generator</td><td>Generator and Deposit</td></tr><tr><td>Custom Gas Config for Callbacks</td><td>Not available</td><td>Fully configurable per client and per contract (callbackGasPrice, callbackGasLimit)</td></tr><tr><td>Fund Locking for Pending Requests</td><td>Funds always withdrawable</td><td>Funds locked if there are pending RNG requests</td></tr><tr><td>Request Retry Mechanism</td><td>Not available</td><td>Failed RNG callbacks due to low balance retried for 48 hours</td></tr><tr><td>Backward Compatibility</td><td>Not applicable</td><td>Generator and Deposit allow compatibility with older structure</td></tr><tr><td>On chain storing calldata hash of request</td><td>Only validated on chain</td><td>Stored on chain and validated also on chain</td></tr></tbody></table>



***

## Functions Introduced in dVRF 3

<table><thead><tr><th width="303.3831787109375">Function Name</th><th width="142.006591796875">Contract</th><th>Utility</th></tr></thead><tbody><tr><td>addClientToWhitelist(uint128, uint128)</td><td>Deposit</td><td>Allows new clients to self-whitelist, specifying their max gas price and gas limit.</td></tr><tr><td>getMinBalanceLimit(uint128, uint128)</td><td>Deposit</td><td>Returns the minimum balance required to maintain based on client’s gas settings. </td></tr><tr><td>addContractToWhitelist(address, uint128, uint128)</td><td>Deposit</td><td>Allows whitelisted clients to add their contract while specifying the callback gas price and callback gas limit.</td></tr><tr><td>withdrawFundClient(uint128)</td><td>Deposit</td><td>Allows whitelisted clients to withdraw their funds if they don’t have any pending requests.</td></tr><tr><td>getContractDetails(address) external view returns (uint128, uint128)</td><td>Deposit</td><td>Returns callback gas price and callback gas limit of a whitelisted contract.</td></tr><tr><td>checkMinBalanceClient(address) public view returns (uint128)</td><td>Deposit</td><td>Returns the minimum balance limit for a given client address.</td></tr><tr><td>checkMaxGasPriceClient(address) public view returns (uint128)</td><td>Deposit</td><td>Returns the 'maxGasPrice' for a given client address.</td></tr><tr><td>checkMaxGasLimitClient(address) public view returns (uint128)</td><td>Deposit</td><td>Returns the 'maxGasLimit' for a given client address.</td></tr><tr><td>updateMaxGasPrice(uint128)</td><td>Deposit</td><td>Allows a whitelisted client to update their maxGasPrice, which impacts the minBalanceLimit.</td></tr><tr><td>updateMaxGasLimit(uint128)</td><td>Deposit</td><td>Allows a whitelisted client to update their maxGasLimit, which affects the minBalanceLimit.</td></tr><tr><td>updateCallbackGasPrice(address, uint128)</td><td>Deposit</td><td>Allows clients to set callbackGasPrice for specific contracts.</td></tr><tr><td>updateCallbackGasLimit(address, uint128)</td><td>Deposit</td><td>Allows clients to set callbackGasLimit for specific contracts.</td></tr></tbody></table>

