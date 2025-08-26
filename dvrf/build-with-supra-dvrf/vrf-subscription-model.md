---
hidden: true
---

# VRF Subscription FAQ

Supra dVRF is a prepaid subscription model that provides enhanced controls for consumers. This document will walk you through the key features and functionalities of the Supra VRF prepaid subscription.&#x20;

## Whitelisting for dVRF

The subscription model requires users to be registered with a wallet address that can be used as the subscription manager and once it is whitelisted, it can be used to register any number of requester smart contracts to consume random numbers from Supra dVRF.

Currently under VRF 2.1, the whitelisting process is manual and requires users to request whitelisting via this [form](https://forms.gle/WFvpBXg67GmDrokv5) which is then carried out by the Supra team. We are now rolling out VRF 3.0 with self a whitelisting feature. Please refer to the [network list](https://docs.supra.com/oracles/dvrf/networks) to see the version of the dVRF protocol for your chosen network.

## How to determine Max gas limit and Max gas price in VRF 3.0?

Supra VRF subscription operates as a prepaid model, where users can add funds to their subscription account to cover the gas fee for call back (response) transactions. Setting up the right value depends on. your requirement. If speed of the response is a priority over cost, setting high values will help and vice versa.\
These two parameters will also impact the minimum balance that you have to maintain in your subscription account as described later in this page.

### **Max Gas Limit**

“**maxGasLimit**” is the maximum gas limit with which the callback transaction can be executed. This will be the default and maximum call back gas limit applied to all requester smart contracts registered under your subscription. If you need a different call back gas limit for each requester contract you can set "**callbackGasLimit"** parameter at requester contract level, which should be less than or equal to **"maxGasLimit".**&#x20;

### **Max Gas Price**

Maximum gas price is the gas price you are willing to spend for call back transactions of all your requester contracts. If you need a different call back gas price for each requester contract you can set "**callbackGasPrice"** parameter at requester contract level, which should be less than or equal to the **"maxGasPrice".**

## What is the callback gas price?

The callback gas price is set per contract basis which determines the maximum gas price with which the callback transaction gets executed for that particular contract. The callback gas price of a contract is `<= maxGasPrice` of the client.

## What is the callback gas limit?

The callback gas limit is set per contract basis which determines the maximum gas limit with which the callback transaction gets executed for that particular contract. The callback gas limit of a contract is `<= maxGasLimit` of the client.

{% hint style="info" %}
callbackGasLimit of a contract < = maxGasLimit of the subscription\
callbackGasPrice of a contract <=  maxGasprice of the subscription\
\
If you need to increase the callbackGasLimit and callbackGasPrice of a contract,  please make sure maxGasLimit and maxGasprice of the subscription are always higher than the values you plan to have for your contract.
{% endhint %}

### Total Balance

The total balance represents the amount of tokens held by a consumer in their prepaid subscription account.

### **Minimum Balance**

The minimum fund balance is the threshold that consumers must maintain in order to submit new VRF requests and have them accepted for processing. If the total balance falls below the minimum balance, Supra will stop processing new VRF requests. However, it will continue to process already in-progress requests. The minimum Balance for a subscription account is dynamically calculated as follows:

```
minBalanceLimit = minRequests * _maxGasPrice * (_maxGasLimit +  verificationGasValue);
```

where,

* **MinRequests** = Maximum number of requests (already in progress) to be processed after reaching the minimum balance. This is currently set to 30. If any user had more than 30 at the time, additional requests will be on hold for and retried every 6 hours, and, after 48 hours will be dropped.&#x20;
* **verificationGasValue** = Gas consumed by BLS verification and random number generation.
* Clients can fetch their “**minBalanceLimit**” by calling “**getMinBalanceLimit(uint128 \_maxGasPrice, uint128 \_maxGasLimit)”**.

### **Effective Balance**

When the effective balance falls below certain thresholds, email reminders will be sent to prompt users to top up their subscription balance.

```
Effective Balance = Total Balance - Minimum Balance
```

<table><thead><tr><th width="226">Reminder</th><th>Logic</th></tr></thead><tbody><tr><td>First Reminder</td><td>Effective balance &#x3C;= 50% of minimum balance</td></tr><tr><td>Second Reminder</td><td>Effective balance &#x3C;=25% of minimum balance</td></tr><tr><td>Third Reminder</td><td>Effective Balance &#x3C;=10% of minimum balance</td></tr><tr><td>Fourth Reminder</td><td>Effective balance &#x3C;=5% of minimum balance</td></tr><tr><td>Final Reminder</td><td>Effective balance &#x3C;=minimum balance</td></tr></tbody></table>

If you have further questions or need assistance, please feel free to reach out to our support team.
