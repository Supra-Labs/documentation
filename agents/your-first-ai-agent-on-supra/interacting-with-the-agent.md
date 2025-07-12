# Interacting with the Agent

{% stepper %}
{% step %}
#### **Wallet Creation**

```bash
$ npx ts-node agent.ts

*** Welcome to SupraOnChainAgent ***

Initializing Supra Client...
Connected to network with chain ID: _ChainId { value: 8 }
Loaded existing account from account.json
Your Supra Account Address: 0x26b58df6a3716bc16de96d6aa5fa60c1cf12e0854e8983b4a6d0a4a9548c218d
```
{% endstep %}

{% step %}
#### **Fetching the Wallet Balance**

{% hint style="info" %}
**Make sure you fund your wallet via Faucet else it won't be showing registered on chain.**
{% endhint %}

```bash
Supra OnChain Agent> what is the balance of my account

--- Account Info ---
Address          : 0x26b58df6a3716bc16de96d6aa5fa60c1cf12e0854e8983b4a6d0a4a9548c218d
Sequence Number  : 0n
Authentication Key: 0x26b58df6a3716bc16de96d6aa5fa60c1cf12e0854e8983b4a6d0a4a9548c218d
Balance          : 1000000000
---------------------
```
{% endstep %}

{% step %}
#### **Funding Wallet.**

```bash
Supra OnChain Agent> please fund my account
Requesting funds via faucet...
Faucet Response: {
  status: 'Success',
  transactionHash: '0x866984a1ae3b3225379b0aa77f83f25a169faef6c605d22da8df8c51ab196a0f'
}
Updated Account Balance: 1500000000
```
{% endstep %}

{% step %}
#### **Transaction History of an account.**

```bash
Supra OnChain Agent> what is my transaction history

--- Transaction History ---

// Your History

---------------------------
```
{% endstep %}

{% step %}
#### **Fetching Transaction details based on its TXN Hash.**

```bash
Supra OnChain Agent> show me transaction details for 0x0eddc65189edae437e326908f9039f046801eec8db8a08e5b01b0a75b7dfd46a

--- Transaction Details ---
{
  "txHash": "0x0eddc65189edae437e326908f9039f046801eec8db8a08e5b01b0a75b7dfd46a",
  "sender": "0x6f12b0fe05ba8796c0455981c88c99a11539ef205723a1740fc7dd72cb98520f",
  "sequenceNumber": 1553378,
  "maxGasAmount": 500000,
  "gasUnitPrice": 100,
  "gasUsed": 35,
  "transactionCost": 3500,
  "txExpirationTimestamp": 1747814148000000,
  "txConfirmationTime": 1747813849978867,
  "status": "Success",
  "events": [
    {
      "guid": {
        "creation_number": "0",
        "account_address": "0x0"
      },
      "sequence_number": "0",
      "type": "0x1::coin::CoinWithdraw",
      "data": {
        "account": "0x6f12b0fe05ba8796c0455981c88c99a11539ef205723a1740fc7dd72cb98520f",
        "amount": "518227",
        "coin_type": "0x8ede5b689d5ac487c3ee48ceabe28ae061be74071c86ffe523b7f42acda2fcb7::test_usdc::TestUSDC"
      }
    }
```
{% endstep %}

{% step %}
#### **Looking for Resources in your account**

```bash
Supra OnChain Agent> show my account resources

--- Account Resources ---
{
  "resource": [
    [
      "0x0000000000000000000000000000000000000000000000000000000000000001::account::Account",
      {
        "address": "0000000000000000000000000000000000000000000000000000000000000001",
        "module": "account",
        "name": "Account",
        "type_args": []
      }
    ],
    [
      "0x0000000000000000000000000000000000000000000000000000000000000001::coin::CoinStore<0000000000000000000000000000000000000000000000000000000000000001::supra_coin::SupraCoin>",
      {
        "address": "0000000000000000000000000000000000000000000000000000000000000001",
        "module": "coin",
        "name": "CoinStore",
        "type_args": [
          {
            "struct": {
              "address": "0000000000000000000000000000000000000000000000000000000000000001",
              "module": "supra_coin",
              "name": "SupraCoin",
              "type_args": []
            }
          }
        ]
      }
    ]
  ],
  "cursor": "0026b58df6a3716bc16de96d6aa5fa60c1cf12e0854e8983b4a6d0a4a9548c218d6801000000000000000000000000000000000000000000000000000000000000000104636f696e09436f696e53746f7265010700000000000000000000000000000000000000000000000000000000000000010a73757072615f636f696e095375707261436f696e0000"
}
-------------------------
```
{% endstep %}
{% endstepper %}

## Conclusion

Congratulations on building your first Supra Agent! This guide serves as a starting point in your journey into the Supra ecosystem.&#x20;

### Want to create more AI Agents on Supra?&#x20;

Our Agents Hub is live with multiple AI Agent templates to help you get started.

<table data-view="cards"><thead><tr><th></th><th data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td>Link to Agents Hub</td><td><a href="https://github.com/Supra-Labs/Supra-agents-hub">https://github.com/Supra-Labs/Supra-agents-hub</a></td><td><a href="../.gitbook/assets/GqZ7O28WYAAQWvK.jpeg">GqZ7O28WYAAQWvK.jpeg</a></td></tr></tbody></table>
