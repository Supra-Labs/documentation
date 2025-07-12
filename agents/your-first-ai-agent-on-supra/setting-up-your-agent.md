# Setting up your Agent

## Prerequisites

Before getting started, make sure you have the following installed:

* Node.js&#x20;
* [NVM installed](https://github.com/coreybutler/nvm-windows/releases)

## Project Setup

{% stepper %}
{% step %}
#### **Clone This Repo**

```bash
git clone https://github.com/Supra-Labs/Supra-agents-hub.git
```
{% endstep %}

{% step %}
#### **Navigate into Agents sub-dir & Open Target Agent Directory.**

```bash
cd Supra-agents-hub
cd Agents
cd SupraOnChainAgent
```
{% endstep %}

{% step %}
#### **Install Dependencies**

```bash
npm install
```
{% endstep %}

{% step %}
#### **Running Your Agent**

```bash
npx tsx agent.ts
```
{% endstep %}
{% endstepper %}

{% hint style="info" %}
**You should see a welcome message with your generated account address and list of available commands.** \
\
Sample Output below:
{% endhint %}

```bash
$ npx tsx agent.ts

*** Welcome to SupraOnChainAgent ***

Initializing Supra Client...
Connected to network with chain ID: _ChainId { value: 6 }
Loaded existing account from account.json
Your Supra Account Address: 0x26b58df6a3716bc16de96d6aa5fa60c1cf12e0854e8983b4a6d0a4a9548c218d

Available commands:
 - 'what is the balance of my account'     : Fetch account info & balance
 - 'please fund my account'                 : Fund account via faucet
 - 'show me transaction details for <TX_HASH>': Fetch transaction details
 - 'what is my transaction history'         : Retrieve transaction history
 - 'show my account resources'              : Fetch resource data
 - 'help'                                   : Show this help message
 - 'exit'                                   : Quit the agent

SupraOnChainAgent>
```

{% hint style="success" %}
**The agent will carry out the corresponding blockchain operations using the Supra-L1-SDK.**
{% endhint %}
