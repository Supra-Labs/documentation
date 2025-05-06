---
description: A walkthrough of integrating with our pull oracle using JavaScript for EVM.
---

# Pull Oracle Walkthrough

Supra’s pull oracle offers low latency, on-demand price data that can be configured uniquely for each use case.

In this model, developers/projects (You!) are responsible for writing the price data on-chain. While this does mean that you will be responsible for the associated gas fees, you unlock the ability to obtain on-demand price data that fit your needs perfectly. This means that you can obtain data on any timer or deviation threshold that you desire.

This is accomplished by running a web2 application that is responsible for requesting the price data from Supra’s gRPC server and then writing it to your smart contract on your destination chain.

To clarify, integrating with Supra’s pull oracle requires two things.

1. A deployed smart contract
2. A web 2 application

<figure><img src="https://github.com/nolan-supra/docs-test/blob/guides/.gitbook/assets/diagram.png" alt=""><figcaption><p>Web2 application requests from supra gRPC server and delivers the data to chain where it is validated, stored, and returned to the calling function.</p></figcaption></figure>

## Creating your Smart Contract

Price data is sent to your Smart Contract as bytes. The bytes contain the proof data needed to verify the prices. So, we'll use the `ISupraOraclePull` interface to verify and extract the price data from the bytes.\
\
We'll explain how to send the data to your contract in the next section. For now, let's focus on the smart contract.

### Step 1: Add the `ISupraOraclePull` interface.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

interface ISupraOraclePull {

    //Verified price data
    struct PriceData {
        // List of pairs
        uint256[] pairs;
        // List of prices
        // prices[i] is the price of pairs[i]
        uint256[] prices;
        // List of decimals
        // decimals[i] is the decimals of pairs[i]
        uint256[] decimals;
    }


    function verifyOracleProof(bytes calldata _bytesproof) 
        external 
        returns (PriceData memory);

}

// Mock contract which can consume oracle pull data
contract MockOracleClient {

}
```

### Step 2: Create the constructor and configure the `ISupraOraclePull` interface.

Here, we'll initialize the `ISupraOraclePull` interface within the constructor by passing the address of the Supra Pull contract. You can find the Supra Pull contract address for your desired network on our [available networks page (for pull oracle)](https://github.com/nolan-supra/docs-test/blob/guides/oracles/data-feeds/pull-oracle/networks.md).

```solidity
// Mock contract which can consume oracle pull data
contract MockOracleClient {
    // The oracle contract
    ISupraOraclePull internal oracle;

    constructor(address oracle_) {
        oracle = ISupraOraclePull(oracle_);
    }
}
```

### Step 3: Create the function that will be responsible for receiving the price data proof

This function will use the `verifyOracleProof()` function of the `ISupraOraclePull` interface to extract the price data from the bytes. The price data is returned as the struct, `PriceData`. Refer to the `ISupraOraclePull` interface for more insight into the struct.

```solidity
// Extract price data from the bytes/proof data
    function deliverPriceData(bytes calldata _bytesProof) external {
    
        ISupraOraclePull.PriceData memory prices = 
        oracle.verifyOracleProof(_bytesProof);
        
        //Iterate over all the extracted prices.
        //Do something with them!
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            //prices.pairs[i] - The pair ID at the current position. 
            //prices.prices[i] - The price of the pair ID at the current position.
            //prices.decimals[i] - The decimal places of the pair ID at the current position.
        }

    }
```

### Step 4: Create a function to update the Pull Oracle address.

This will allow you to update the address of the Supra Pull contract after deployment, allowing you to future proof your contract.

```solidity
function updatePullAddress(address oracle_) 
    external {
        oracle = ISupraOraclePull(oracle_);
    }
```

### Step 5: Add access control & Final Code!

We want to restrict who can call our `updatePullAddress()` function and most likely the `deliverPriceData()` function too.\
\
To do this, we'll use OpenZeppelin's `Ownable` contract and make use of the `onlyOwner` modifier.

```solidity
/// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

import "@openzeppelin/contracts/access/Ownable.sol";

interface ISupraOraclePull {

    //Verified price data
    struct PriceData {
        // List of pairs
        uint256[] pairs;
        // List of prices
        // prices[i] is the price of pairs[i]
        uint256[] prices;
        // List of decimals
        // decimals[i] is the decimals of pairs[i]
        uint256[] decimals;
    }


    function verifyOracleProof(bytes calldata _bytesproof) external  returns (PriceData memory);

}

// Mock contract which can consume oracle pull data
contract MockOracleClient is Ownable{
    // The oracle contract
    ISupraOraclePull internal oracle;

    constructor(address oracle_) Ownable(msg.sender){
        oracle = ISupraOraclePull(oracle_);
    }

    // Extract price data from the bytes/proof data
    function deliverPriceData(bytes calldata _bytesProof) external onlyOwner {
    
        ISupraOraclePull.PriceData memory prices = oracle.verifyOracleProof(_bytesProof);
        
        //Iterate over all the extracted prices.
        //Do something with them!
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            //prices.pairs[i] - The pair ID at the current position. 
            //prices.prices[i] - The price of the pair ID at the current position.
            //prices.decimals[i] - The decimal places of the pair ID at the current position.
        }

    }

    function updatePullAddress(address oracle_) 
    external onlyOwner {
        oracle = ISupraOraclePull(oracle_);
    }
}
```

That's it for our smart contract.

## Creating your web2 application

You can view example code for this section in [this repo here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/javascript).

Your web2 application is responsible for requesting Supra's gRPC server to obtain the proof bytes and then sending those bytes to your deployed contract's `deliverPriceData()` function.\
\
To accomplish this, we will use our provided `pullServiceClient` library along with Web3.js.

The code for this portion of the guide is available here. Please download/clone the repo before continuing.

Within the `main.js` file, you will notice a `settings` section at the top along with 3 functions: `main`, `callContract`, `outputPriceData`

* `main` - Responsible for requesting the price/proof data from Supra's gRPC server.
* `callContract` - Responsible for calling your designated smart contract function with the price/proof data.
* `outputPriceData` - Optional code, responsible for extracting the price data within your web2 app for output. Note, this is for use in your web2 application only and should not be used on-chain.

### Step 1: Install dependencies

1. After downloading/cloning the repo, open your terminal and navigate to the client folder within the project directory.
2. Execute the following command to install the project dependencies: `npm install`

### Step 2: Configure your settings.

Located within the top of the `main.js` file of the client directory, update the settings to match your project/setup.

Settings are split into two categories. The first category relates to the Supra gRPC server settings that will be used when requesting the price/proof bytes from the gRPC server. The second category relates to your client settings and will be used in sending the price/proof bytes to your contract on your destination chain.

Supra gRPC Server Settings:

* `gRPCAddress` - This is the [address of the Supra gRPC server](https://github.com/nolan-supra/docs-test/blob/guides/oracles/data-feeds/pull-oracle/README.md).
* `pairIndexes` - An array of the [pair indexes](https://github.com/nolan-supra/docs-test/blob/guides/oracles/data-feeds/data-feeds-index.md) that you would like to request/use.
* `chainType` - The chain type that your contract is deployed on. (EVM, Sui, Aptos)

Client Settings:

* `rpcUrl` - A valid RPC URL for your destination chain.
* `contractAbi` - The ABI of your deployed smart contract that will receive the price data.
* `contractAddress` - The address of your deployed smart contract (from this guide).
* `walletAddress` - The wallet address that will be responsible for sending the transaction.
* `privateKey` - The private key of the above wallet address.

### Step 3: Set the function that will receive the price/proof bytes.

Located on lines `56` and `57` of the `main.js` file, you will see the code designating the smart contract function that we will be sending the data to.

In this guide, this is the `deliverPriceData()` function. If you have modified the name in your smart contract to something else, please update these two lines accordingly.

```javascript
const txData = contract.methods.deliverPriceData(hex).encodeABI(); // function from you contract eg: deliverPriceData 
const gasEstimate = await contract.methods.deliverPriceData(hex).estimateGas({from: walletAddress});
```

### Step 4: Execute the code!

Once you have configured the above settings, you can execute the code with the following command: `node main.js`

## Closing thoughts

By reviewing and modifying the web2 application, you can introduce your own timer for updates and even add some form of deviation threshold. For example, we could call the `main` function once every minute. We could monitor the price data and only update it once it has changed from the value currently stored on chain. Or we could even monitor the price data for a specific change in percentage to trigger an update.

This type of freedom opens the door for use cases requiring more frequent updates/fresher data than is provided by traditional push models. It's up to you to decide how you want to use it.

Happy building!
