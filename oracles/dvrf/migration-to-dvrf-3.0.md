# Migration to dVRF 3.0

This page introduces the new features of Supra dVRF 3.0 and provides migration steps. Clients are encouraged to migrate at their convenience before the VRF 2.0 contracts are phased out in few weeks. \\

### **Changes Introduced in VRF 3.0**

1. Self-whitelisting will be available through the [Subscription Manager UI](https://supra.com/data/dvrf) (selected networks only) and via [deposit contract function](https://docs.supra.com/oracles/dvrf/v2-guide#step-1-create-the-supra-router-contract-interface-1).
2. Subscribing to new requester contracts, removing existing contracts from the subscription, and depositing funds will be available via the front end.
3. Real time transaction updates and Event history updates will be available via the front end.
4. Introduction of requester contract level user-defined parameters to manage Gas Limit and Gas Price for the call back transaction.\
   \
   **Max Gas Limit:** Maximum units of gas you are willing to spend on call back transaction. This is applicable to all requester contracts under your subscription\
   \
   **Callback Gas Limit**: Maximum units of gas you are willing to spend on call back transaction for a particular requester contract. This will be capped at Max Gas Limit above.\
   \
   **Max Gas Price:** The maximum gas price you are willing to spend for a callback transaction in Gwei. This is applicable to all requester contracts under your subscription. Higher values will ensure faster response times, but if speed is not a concern it can be set at a lower value.\
   \
   **Callback Gas Price**: Maximum gas price you are willing to spend on call back transaction for a particular requester contract. This will be capped at Max Gas price above.
5. Dynamic calculation method for Minimum Fund Balance will be based on the Max Gas Limit and Max Gas Price. Read [subscription FAQ](https://docs.supra.com/oracles/dvrf/vrf-subscription-model) for more info.&#x20;

### **How can existing clients migrate?**

Clients need to call **`migrateClient(uint128 _maxGasPrice, uint128 _maxGasLimit) payable`**, specifying the maximum gas price and maximum gas limit for their contracts, and send the required gas tokens to fund the deposit contract.\
This will migrate your existing subscription to the VRF 3.0 deposit contract. During migration, the callback gas price and callback gas limit will be set to the Max Gas price and Max Gas Limit for all registered contracts. These limits can be modified at any time.

### **How existing clients are impacted?**

There will be a 15-20 minute downtime during the deployment which will be communicated well in advance. We will operate both VRF 2.1 and VRF 3.0 in parallel for a period of time in which you can chose to complete the migration.&#x20;

### **How to fund VRF 3.0?**

Clients must withdraw their remaining funds from the VRF 2.1 deposit contract after migration and manually fund the new deposit contract..
