---
description: >-
  This guide uses Ethers.js to monitor the Event of a smart contract. Each time
  the event occurs we'll be able to perform some arbitrary action.
hidden: true
---

# Monitoring Smart Contract Events with Ethers.js

{% hint style="danger" %}
Please note that this guide uses an outdated version of the PUSH model.

This guide will be updated soon.
{% endhint %}

## Intro

Have you ever wanted to monitor the events of a smart contract from an off-chain application? Well, you're in luck! The [Ethers.js](https://docs.ethers.org/v5/) library allows us to interact with the Ethereum blockchain in just a few lines of code.

In this guide, we’ll be using [Node.js](https://nodejs.org/en/) and [Ethers](https://docs.ethers.org/v5/) to listen to the SupraOracles Price Feed smart contract on [Ethereum Goerli testnet](https://ethereum.org/en/developers/docs/networks/#goerli) to tell us every time the S-Value is updated. Then, we’ll call the `checkPrice()` function of the contract to find the updated value of any pair and output it to the console each time the event occurs.

## Getting Started

First things first, [initialize a new project](https://docs.npmjs.com/cli/v9/commands/npm-init) through your preferred IDE's terminal. We’ll be passing the `-y` argument to automatically accept the default values for the initialization process.

```shell
npm init -y
```

Next, install [Ethers](https://www.npmjs.com/package/ethers) v5.7.2 with the [following line of code](https://docs.npmjs.com/cli/v9/commands/npm-install):

```shell
npm install ethers ethers@5.7.2
```

## Time to Code

Now we can start coding. Create a new file called “index.js” to hold our code and then import Ethers.

```javascript
const ethers = require('ethers')
```

### Provider and Contract objects

To interact with contracts using ethers, there are a few things we need. We’ll need a connection to the network ([provider object](https://docs.ethers.org/v5/api/providers/)) and a way to interact with the contract ([contract object](https://docs.ethers.org/v5/api/contract/)).

Creating the [provider object](https://docs.ethers.org/v5/api/providers/) is easy and is the first step in this process. Within the quotations, make sure you add your own RPC URL for the Goerli testnet as you pass it to the `JsonRpcProvider` constructor. You can read more on the [JsonRpcProvider here in Ethers documentation](https://docs.ethers.org/v5/api/providers/jsonrpc-provider/).

```javascript
const monitorFeed = async function() {
    // Add your RPC URL for Goerli ETH between the ''
    const provider = new ethers.providers.JsonRpcProvider('')
}

```

Next, we need to create the [contract object](https://docs.ethers.org/v5/api/contract/). To do this, we’ll need two (2) more things: The contract address and the [contract ABI](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html). We can find both of these on the [SupraOracles Price Feed documentation](https://supraoracles.com/docs/get-started). For convenience, here is the ABI and the address for the contract on Goerli ETH.

```javascript
    const abi = [{ "inputs": [ { "internalType": "string", "name": "marketPair", "type": "string" } ], "name": "checkPrice", "outputs": [ { "internalType": "int256", "name": "price", "type": "int256" }, { "internalType": "uint256", "name": "timestamp", "type": "uint256" } ], "stateMutability": "view", "type": "function" } ]
    const address = '0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7'
```

With that in place, we can initialize the contract object by passing the contract address, ABI, and our provider to the contract constructor.

```javascript
const monitorFeed = async function() {
    // Add your RPC URL for Goerli ETH between the ''
    const provider = new ethers.providers.JsonRpcProvider('')
    const abi = [{ "inputs": [ { "internalType": "string", "name": "marketPair", "type": "string" } ], "name": "checkPrice", "outputs": [ { "internalType": "int256", "name": "price", "type": "int256" }, { "internalType": "uint256", "name": "timestamp", "type": "uint256" } ], "stateMutability": "view", "type": "function" } ]
    const address = '0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7'
    const sValueFeed = new ethers.Contract(address, abi, provider)
}
```

### Contract Listener

The `sValueFeed` contract object allows us to access a number of things. Check out the [Ethers documentation](https://docs.ethers.org/v5/api/contract/contract/) for more information. For this guide, we’ll be using the [contract listeners](https://docs.ethers.org/v5/api/contract/contract/#Contract--events). This works by specifying the event that we wish to listen for as well as the action to take once the event occurs.

To specify the event, let’s take a trip over to [Goerli Etherscan and click on the "Events" for the SupraOracles Price Feed contract](https://goerli.etherscan.io/address/0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7). To start a listener, we can use a string for the name of the event or the value of the method ID.

<figure><img src="https://github.com/nolan-supra/docs-test/blob/guides/.gitbook/assets/etherscan.png" alt=""><figcaption></figcaption></figure>

We can see that this method is called each time the price is updated. So, we’ll go ahead and pass that method ID to the [contract listener](https://docs.ethers.org/v5/api/contract/contract/#Contract-listeners) as the first parameter. The second parameter tells our listener what to do with the data that it grabs when the event occurs. For now, we’ll output the data to the console to see what it gives us.

```javascript
sValueFeed.on(0x808612c7, async data =>{
	console.log(data)
});
```

Here's our code up to this point:

```javascript
const ethers = require('ethers');
 
const monitorFeed = async function() {
    // Add your RPC URL for Goerli ETH between the ''
    const provider = new ethers.providers.JsonRpcProvider('')
    const abi = [{ "inputs": [ { "internalType": "string", "name": "marketPair", "type": "string" } ], "name": "checkPrice", "outputs": [ { "internalType": "int256", "name": "price", "type": "int256" }, { "internalType": "uint256", "name": "timestamp", "type": "uint256" } ], "stateMutability": "view", "type": "function" } ]
    const address = '0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7'
    const sValueFeed = new ethers.Contract(address, abi, provider)
    console.log("Starting S-Value: " + (await sValueFeed.checkPrice('eth_usdt')).price)
 
    sValueFeed.on(0x808612c7, async data =>{
        console.log(data)
    });
 
}
 
monitorFeed()
```

Let's go ahead and run this with the following command in the terminal:

```shell
node index.js
```

This might take a few minutes. While you wait, now is a great time to stretch and grab some water! If you’re curious, feel free to [refresh the Etherscan page](https://goerli.etherscan.io/address/0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7). Once a new transaction pops up triggering the event we are listening to, you will see the following output in your terminal:

```json
{
    blockNumber: 8252876,
    blockHash: '0xf0ce1461fc83e80676ad534a89ec4a7e9ef060d3eabf572e168924861325ea08',
    transactionIndex: 41,
    removed: false,
    address: '0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7',
    data: '0x0000000000000000000000000000000000000000000000000000000063b4df50',
    topics: [
      '0x66cbca4f3c64fecf1dcb9ce094abcf7f68c3450a1d4e3a8e917dd621edb4ebe0'
    ],
    transactionHash: '0x506502ab029fc55510102a4dd863b250e59de1bd8710362956bc675d5cf45f93',
    logIndex: 77,
    removeListener: [Function (anonymous)],
    getBlock: [Function (anonymous)],
    getTransaction: [Function (anonymous)],
    getTransactionReceipt: [Function (anonymous)]
}

```

What is this? This is the transaction data stored in the data variable from the listener that we made. To get something like the `transactionHash` out of this, all we have to do is use the following:

```javascript
data.transactionHash
```

We’ll go ahead and modify this now. For this guide, all we want to do is output the transaction hash each time the event is triggered. Then, we’re going to call the `checkPrice()` function of the `sValueFeed` contract to get the newly updated S-Value for any price pair. Check out the [SupraOracles documentation here](https://supraoracles.com/docs/get-started/) for more information on what the `checkPrice()` function does.

## Final Code

```javascript
const ethers = require('ethers');
 
const monitorFeed = async function() {
    // Add your RPC URL for Goerli ETH between the ''
    const provider = new ethers.providers.JsonRpcProvider('')
    const abi = [{ "inputs": [ { "internalType": "string", "name": "marketPair", "type": "string" } ], "name": "checkPrice", "outputs": [ { "internalType": "int256", "name": "price", "type": "int256" }, { "internalType": "uint256", "name": "timestamp", "type": "uint256" } ], "stateMutability": "view", "type": "function" } ]
    const address = '0x25DfdeD39bf0A4043259081Dc1a31580eC196ee7'
    const sValueFeed = new ethers.Contract(address, abi, provider)
 
    sValueFeed.on(0x808612c7, async data =>{
        console.log("S-Value Updated at TXN: " + data.transactionHash)
        console.log("New S-Value: " + (await sValueFeed.checkPrice('eth_usdt')).price)
    });
 
}
 
monitorFeed()

```

Feel free to run this code again with the following line:

```shell
node index.js
```

This time around, you’ll see the following output:

```javascript
S-Value Updated at TXN: 0x80e0b9b112e061429b7513ad449d0125e4a894ffe81a19f839db80db1b70b279
New S-Value: 12240451125
```

## Conclusion

To summarize, we use a provider object, contract object, and contract listener to monitor smart contract events. Every time it's triggered, we perform some action. In our case, we output the transaction hash of the new event and then grab the newly updated S-Value.

That’s it - we just wrote some code to monitor smart contract events using Node.js and Ethers. One more tool added to our tool belt.
