---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Pull Oracle

DORA V2, our latest version of data feeds protocol, uses a combination of Web2 and Web3 methods to achieve ultra-low latency when sending data from Supra to destination chains. First, Web2 methods are used to retrieve data from Supra, while Web3 smart contracts are utilised for cryptographically verifying and writing it on-chain where it lives on immutable ledgers.

Please refer to the following resources for a better understanding of DORA price feeds.

* [Data Feeds](../) - This explains how Supra calculates the **S-Value** for data feeds.
* [Data Feeds Index](../data-feeds-index.md) - This provides a list of data feeds currently offered by Supra.
* [Available Networks](networks.md) - This lists available networks and Supra contract addresses.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

\
The first step of the process would be to set up your web2 code to interact with DORA's pull oracle and sync with Supra's consumer smart contract address.

{% tabs %}
{% tab title="EVMs" %}
#### _Javascript Implementation_

A code library is designed to connect to a gRPC/Rest server to retrieve proof data, which it then uses to interact with a smart contracts can be downloaded from links provided below. This document provides instructions on setting up and using the library, along with guidelines for customising its components to suit your specific requirements.

* gRPC - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/gRPC/javascript).
* REST - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/rest/javascript).

**Installation**

To use the PullServiceClient library, follow these steps:

1. Clone the repository or download the library's source code.
2.  Install the necessary dependencies by running the following command in your project directory:

    ```bash
    npm install
    ```

#### Usage

The library's primary function retrieves proof data from the gRPC server using specified parameters and subsequently invokes a smart contract function on a blockchain network.

#### Configuration

Before using the library, make sure to set up the configuration in the main.js file:

*   Set the server address: gRPC

    <table><thead><tr><th width="118">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript">mainnet-dora-2.supra.com:443
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">testnet-dora-2.supra.com:443
    </code></pre></td></tr></tbody></table>
* Set the server address: REST

<table><thead><tr><th width="147">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript"><strong>https://rpc-mainnet-dora-2.supra.com
</strong></code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">https://rpc-testnet-dora-2.supra.com
</code></pre></td></tr></tbody></table>

*   Set the pair indexes as an array (below given indexes are examples):

    ```bash
    const pairIndexes = [0, 21, 61, 49];
    ```
*   Set the chain type to EVM:

    ```bash
    const chainType = 'evm';
    ```
*   Configure the RPC URL for the desired blockchain network:

    ```bash
    const web3 = new Web3(new Web3.providers.HttpProvider('<RPC URL>'));
    ```

#### Customization

Users can customize the smart contract interaction under the callContract function. Specifically, you can modify the following components:

1.  **Smart Contract ABI**: Update the path to your smart contract's ABI JSON file:

    ```bash
    const contractAbi = require("../resources/abi.json");
    ```
2.  **Smart Contract Address**: Set the address of your smart contract:

    ```bash
    const contractAddress = '<CONTRACT ADDRESS>';
    ```
3.  **Function Call**: Modify the function call according to your smart contract's methods. For example, if your smart contract has a method named GetPairPrice:

    ```bash
    const txData = contract.methods.GetPairPrice(hex, 0).encodeABI();
    ```
4. **Gas Estimate**: Adjust the gas estimation by calling the desired contract method:

{% code overflow="wrap" %}
````
```bash
const gasEstimate = await contract.methods.GetPairPrice(hex, 0).estimateGas({ from: "<WALLET ADDRESS>" });
```
````
{% endcode %}

5. **Transaction Object**: Customize the transaction object as needed:

{% code overflow="wrap" %}
````
```bash
const transactionObject = {
 from: "<WALLET ADDRESS>",
 to: contractAddress,
 data: txData,
 gas: gasEstimate,
 gasPrice: await web3.eth.getGasPrice() // Set your desired gas price here, e.g: web3.utils.toWei('1000', 'gwei')
};
```
````
{% endcode %}

6. **Private Key Signing**: Sign the transaction with the appropriate private key:

{% code overflow="wrap" %}
````
```bash
const signedTransaction = await web3.eth.accounts.signTransaction(transactionObject, "<PRIVATE KEY>");
```
````
{% endcode %}

#### Running the Application

To run the application, execute the following command:

```bash
node main.js
```

This will initiate the fetching of proof data and allow for interaction with the smart contract based on the provided configuration and customisation.\
\
\
\-----------------------------------------------------------\\

#### _Rust Implementation_

The code library being discussed in the section can be downloaded [here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/rust-sdks).\
\
The Rust PullServiceClient is designed to interact with a gRPC server for fetching proof data and using that data to call a smart contract on a blockchain network. This readme provides instructions on how to use the library and customize certain components for your specific use case.

**Prerequisites**

* [Rust](https://www.rust-lang.org/) and [Cargo](https://doc.rust-lang.org/cargo/getting-started/installation.html) installed on your machine.

#### Installation

To use the Rust library for Sui, Aptos and EVM, and follow these steps:

1. Clone the repository or download the library's source code.
2. Navigate to the project directory in your terminal

#### Usage

The Rust library for Sui, Aptos and EVM provides a complete example that fetches proof data from a gRPC server and then calls a contract function on a blockchain network.

#### Configuration

Before using the library, configure the file in example folder:

*   gRPC server address:

    <table><thead><tr><th width="128">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://mainnet-dora-2.supra.com:443".to_string();
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://testnet-dora-2.supra.com:443".to_string();
    </code></pre></td></tr></tbody></table>
*   REST Server address

    <table><thead><tr><th width="128">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-mainnet-dora-2.supra.com".to_string();
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-testnet-dora-2.supra.com".to_string();
    </code></pre></td></tr></tbody></table>
*   Set the pair indexes as an array: ( below mentioned indexes are for an example)

    ```bash
    let pair_indexes = vec![0, 21, 61, 49];
    ```
*   Set the chain type to EVM:

    ```bash
    let chain_type = "evm".to_string();
    ```
*   Set the RPC URL for the desired blockchain network:

    ```bash
    let rpc_url = "<RPC URL>";
    ```

#### Customization

Users can customize the smart contract interaction under the call\_contract function. Specifically, you can modify the following components:

1.  **Private Key**: Set your private key:

    ```bash
    let secret_key = "<PRIVATE KEY>";
    ```
2.  **Contract Address**: Set the address of your smart contract:

    ```bash
    let contract_address = "<CONTRACT ADDRESS>";
    ```
3. **Contract Function Call**: Customize the function call based on your contract methods:

{% code overflow="wrap" %}
````
```bash
let call = sc.get_pair_price(Bytes::from(input.proof_bytes), U256::from(0));
```
````
{% endcode %}

4.  **Smart Contract ABI**: Update the path to your smart contract's ABI JSON file and contract name (EVM only) in `pull_contract.rs`:

    ```bash
     abigen!(
       MockOracleClient,
       "../../resources/abi.json"
     );
    ```

#### Running the Application

Open your terminal and navigate to the project directory.

Run the example using the following command:

**Evm**

```bash
cargo run --example evm_client
```
{% endtab %}

{% tab title="Sui" %}
#### _Javascript Implementation_

A code library is designed to connect to a gRPC/Rest server to retrieve proof data, which it then uses to interact with a smart contracts can be downloaded from links provided below. This document provides instructions on setting up and using the library, along with guidelines for customising its components to suit your specific requirements.

* gRPC - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/gRPC/javascript).
* REST - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/rest/javascript).

**Installation**

To use the PullServiceClient library, follow these steps:

1. Clone the repository or download the library's source code.
2.  Install the necessary dependencies by running the following command in your project directory:

    ```bash
    npm install
    ```

**Usage**

The library's primary function retrieves proof data from the gRPC server using specified parameters and subsequently invokes a smart contract function on a blockchain network.

**Configuration**

*   Set the server address: gRPC

    <table><thead><tr><th width="140">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'mainnet-dora-2.supra.com';
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'testnet-dora-2.supra.com'
    </code></pre></td></tr></tbody></table>
*   Set the server address: REST

    <table><thead><tr><th width="140">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'https://rpc-mainnet-dora-2.supra.com';
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'https://rpc-testnet-dora-2.supra.com';
    </code></pre></td></tr></tbody></table>

    \\
*   Set the pair indexes as an array (below given indexes are examples):

    ```bash
    const pairIndexes = [0, 21, 61, 49];  // this is an example
    ```
*   Set the chain type SUI:

    ```bash
    const chainType = 'sui';
    ```
*   Configure the RPC URL for the desired blockchain network:

    ```bash
    const rpcUrl = suiSdk.getFullnodeUrl('testnet');
    const suiClient = new suiSdk.SuiClient({ url: rpcUrl });
    ```

Before using the library, make sure to set up the configuration in the main.js file:

Users can customize the smart contract interaction under the callContract function. Specifically, you can modify the following components:

1.  **Smart Contract Address**: Set the address of your smart contract:

    ```bash
    const contractAddress = '<CONTRACT ADDRESS>';
    ```
2.  **Function Call**: Modify the function call according to your smart contract's methods. For example, if your smart contract has module named _**pull\_example**_ and a method named _**get\_pair\_price**_:

    ```bash
    const moduleName = "pull_example";
    const functionName = "get_pair_price";
    ```
3.  **Transaction Object**: Customize the transaction object as needed:

    ```bash

    txb.moveCall({ 
        target: `${contractAddress}::${moduleName}::${functionName}`,
        arguments: [
            txb.pure(response.dkg_object),
            txb.pure(response.oracle_holder_object),
            txb.pure(response.bytes_proof, "vector<u8>"),
        ]
    });
    ```
4.  **Private Key Signing**: Sign the transaction with the appropriate private key:

    ```bash
    const raw = suiUtils.fromB64("<PRIVATE KEY BASE64>");
    ```

#### Running the Application

To run the application, execute the following command

```bash
node main.js
```

This will initiate the fetching of proof data and interaction with the smart contract based on the provided configuration.\
\
\-------------------------------------------------------

#### _Rust Implementation_

**Prerequisites**

* [Rust](https://www.rust-lang.org/) and[ Cargo](https://doc.rust-lang.org/cargo/getting-started/installation.html) needs to be installed on your computer.
* The code library being discussed in the section can be downloaded below
  * gRPC: [Download Here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/feat/DoraV2/gRPC/rust-sdks)
  * REST: [Download Here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/feat/DoraV2/rest/rust-sdks)

**Installation**

To use the Rust library for EVM please follow these steps:

1. Clone the repository or download the library's source code.
2. Navigate to the project directory in your terminal.

**Usage**

The Rust library for Sui, Aptos and EVM provides a complete example that retrieves proof data from a gRPC/Rest server and then calls a contract function on a blockchain network.

*   Set the gRPC server address:\\

    <table><thead><tr><th width="128">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">const address = 'mainnet-dora-2.supra.com';
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">const address = 'testnet-dora-2.supra.com';
    </code></pre></td></tr></tbody></table>
*   Set the REST server address:\\

    <table><thead><tr><th width="128">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-mainnet-dora-2.supra.com".to_string();
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-testnet-dora-2.supra.com".to_string();
    </code></pre></td></tr></tbody></table>
* Set the pair indexes as an array: ( below mentioned indexes are for an example)
*   Set the pair indexes as an array: ( below mentioned indexes are for an example)

    ```rust
    let pair_indexes = vec![0, 21, 61, 49];
    ```
*   Set the chain type to Sui:

    ```rust
    let chain_type = "sui".to_string();
    ```
*   Set the RPC URL for the desired blockchain network:

    ```rust
    let rpc_url = "<RPC URL>";
    ```

Before using the library, configure the file in example folder:

#### Customization

Users can customize the smart contract interaction under the call\_contract function. Specifically, you can modify the following components:

1.  **Private Key**: Set your private key:

    ```rust
    let secret_key = "<PRIVATE KEY>";
    ```
2.  **Contract Address**: Set the address of your smart contract:

    ```rust
    let contract_address = "<CONTRACT ADDRESS>";
    ```
3. **Contract Function Call**: Customize the function call based on your contract module and methods::

{% code overflow="wrap" %}
````
```rust
const MODULE: &str = "<CONTRACT MODULE>";
const ENTRY: &str = "<CONTRACT FUNCTION>";
```
````
{% endcode %}

4.  Transaction Object: Customize the transaction object as needed:

    ```rust
    let sui_arg = vec![
        SuiJsonValue::from_str(&payload.dkg_object).unwrap(),
        SuiJsonValue::from_str(&payload.oracle_holder_object).unwrap(),
        SuiJsonValue::from_str(&payload.merkle_root_object).unwrap(),
        SuiJsonValue::from_str(CLOCK).unwrap(),
        SuiJsonValue::from_bcs_bytes(None, &payload.proof_bytes).unwrap(),
    ];

    ```

#### Running the Application

Open your terminal and navigate to the project directory.

Run the example using the following command:

```rust
cargo run --example sui_client
```
{% endtab %}

{% tab title="Aptos" %}
#### _Javascript Implementation_

A code library is designed to connect to a gRPC/Rest server to retrieve proof data, which it then uses to interact with a smart contracts can be downloaded from links provided below. This document provides instructions on setting up and using the library, along with guidelines for customising its components to suit your specific requirements.

* gRPC - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/gRPC/javascript).
* REST - [Download here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/rest/javascript).

**Installation**

To use the PullServiceClient library, follow these steps:

1. Clone the repository or download the library's source code.
2.  Install the necessary dependencies by running the following command in your project directory:

    ```bash
    npm install
    ```

**Usage**

The library's primary function retrieves proof data from the gRPC server using specified parameters and subsequently invokes a smart contract function on a blockchain network.

**Configuration**

Before using the library, make sure to set up the configuration in the main.js file:

*   Set the server address: gRPC

    <table><thead><tr><th width="140">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'mainnet-dora-2.supra.com'
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'testnet-dora-2.supra.com'
    </code></pre></td></tr></tbody></table>
*   Set the server address: REST

    <table><thead><tr><th width="140">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'https://rpc-mainnet-dora-2.supra.com';
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-javascript"><code class="lang-javascript">const address = 'https://rpc-testnet-dora-2.supra.com';
    </code></pre></td></tr></tbody></table>
*   Set the pair indexes as an array (below given indexes are examples):

    ```javascript
    const pairIndexes = [0, 21, 61, 49];  // this is an example
    ```
*   Set the chain type:

    ```javascript
    const chainType = 'Aptos';
    ```
*   Configure the RPC URL for the desired blockchain network:

    ```javascript
    const provider = new aptos.Provider({ fullnodeUrl: "<RPC URL>" });
    ```

Users can customize the smart contract interaction under the callContract function. Specifically, you can modify the following components:

1.  **Smart Contract Address**: Set the address of your smart contract:

    ```javascript
    const contractAddress = '<CONTRACT ADDRESS>';
    ```
2.  **Function Call**: Modify the function call according to your smart contract's methods. For example, if your smart contract has module named _**pull\_example**_ and a method named _**get\_pair\_price**_:

    ```javascript
    const moduleName = "pull_example";
    const functionName = "get_pair_price";
    ```
3. Retrieve signer Account:

{% code overflow="wrap" %}
````
```javascript
let account = new aptos.AptosAccount(aptos.HexString.ensure("<PRIVATE KEY>").toUint8Array(), walletAddress);
```
````
{% endcode %}

4. **Transaction Object**: Customize the transaction object as needed:

{% code overflow="wrap" %}
````
```javascript
const entryFunctionPayload = new aptos.TxnBuilderTypes.TransactionPayloadEntryFunction(
   aptos.TxnBuilderTypes.EntryFunction.natural(
      `${contractAddress}::${moduleName}`, functionName, [], [
      OracleHolder,
      aptos.BCS.bcsSerializeBytes(response.bytes_proof),
   ]
));
```
````
{% endcode %}

**Running the Application**

To run the application, execute the following command: `node main.js`

This will initiate the fetching of proof data and interaction with the smart contract based on the provided configuration and customization.\
\
\---------------------------------------------------------------

#### _Rust Implementation_

The code library being discussed in this section can be downloaded[ here](https://github.com/Entropy-Foundation/oracle-pull-example/tree/master/rust-sdks).\
\
The Rust PullServiceClient is designed to interact with a gRPC server for fetching proof data and using that data to call a smart contract on a blockchain network. This readme provides instructions on how to use the library and customize certain components for your specific use case.

**Prerequisites**

* [Rust](https://www.rust-lang.org/) and[ Cargo](https://doc.rust-lang.org/cargo/getting-started/installation.html) needs to be installed on your computer.

**Installation**

To use the Rust library for Sui, Aptos and EVM and follow these steps:

1. Clone the repository or download the library's source code.
2. Navigate to the project directory in your terminal

**Usage**

The Rust library for Sui, Aptos and EVM provides a complete example that fetches proof data from a gRPC server and then calls a contract function on a blockchain network.

*   Set the gRPC server address:

    <table><thead><tr><th width="218">Network</th><th>gRPC Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://mainnet-dora-2.supra.com:443".to_string();
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://testnet-dora-2.supra.com:443".to_string();
    </code></pre></td></tr></tbody></table>

    \
    Set the REST server address:

    <table><thead><tr><th width="218">Network</th><th>REST Server Address</th></tr></thead><tbody><tr><td>Mainnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-testnet-dora-2.supra.com".to_string();
    </code></pre></td></tr><tr><td>Testnets</td><td><pre class="language-rust" data-overflow="wrap"><code class="lang-rust">let address = "https:://rpc-testnet-dora-2.supra.com".to_string();
    </code></pre></td></tr></tbody></table>
*   Set the pair indexes as an array: ( below mentioned indexes are for an example)

    ```rust
    let pair_indexes = vec![0, 21, 61, 49];
    ```
*   Set the chain type:

    ```rust
    let chain_type = "aptos".to_string();
    ```
*   Set the RPC URL for the desired blockchain network:

    ```rust
    let rpc_url = "<RPC URL>";
    ```

Before using the library, configure the file in example folder:

#### Customization

Users can customize the smart contract interaction under the call\_contract function. Specifically, you can modify the following components:

1.  **Private Key**: Set your private key:

    ```rust
    let secret_key = "<PRIVATE KEY>";
    ```
2.  **Contract Address**: Set the address of your smart contract:

    ```rust
    let contract_address = "<CONTRACT ADDRESS>";
    ```
3. **Contract Function Call**: Customize the function call based on your contract module and methods::

{% code overflow="wrap" %}
````
```rust
const MODULE: &str = "<CONTRACT MODULE>";
const ENTRY: &str = "<CONTRACT FUNCTION>";
```
````
{% endcode %}

4. Transaction Object: Customize the transaction object as needed:

{% code overflow="wrap" %}
````
```rust
et aptos_arg = TransactionPayload::EntryFunction(EntryFunction::new(
    ModuleId::new(address, Identifier::new(MODULE).unwrap()),
    Identifier::new(ENTRY).unwrap(),
    vec![],
    vec![        bcs::to_bytes(&AccountAddress::from_hex_literal(&payload.oracle_holder_object).unwrap()).unwrap(),
bcs::to_bytes(&payload.bytes_proof).unwrap(),
    ],
));
```
````
{% endcode %}

#### Running the Application

Open your terminal and navigate to the project directory.

Run the example using the following command:

```rust
cargo run --example aptos_client
```
{% endtab %}
{% endtabs %}

Now, let's move on to set up your Web3 components:

{% tabs %}
{% tab title="Solidity(EVM)" %}
**Step 1: Create The S-Value Pull Interface to verify the price data received.**

Add the following code to the solidity smart contract that you wish to retrieve an **S-Value**.

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

interface ISupraOraclePull {

//Below does not have the timestamp or the round.
    struct PriceData {
       
        uint256[] pairs;
         
        // prices[i] is the price of pairs[i]
        uint256[] prices;
         
        // decimals[i] is the decimals of pairs[i]
        uint256[] decimals;
    }

// If timestamp or round is required please use the below
    struct PriceInfo {
        uint256[] pairs;
       
        // prices[i] is the price of pairs[i]
        uint256[] prices;
       
        // timestamp[i] is the timestamp of pairs[i]
        uint256[] timestamp;
       
        // decimals[i] is the decimals of pairs[i]
        uint256[] decimal;
       
        // round[i] is the round of pairs[i]
        uint256[] round;
    }


//Below function requests price data with round
    function verifyOracleProof(bytes calldata _bytesproof) 
    external 
    returns (PriceData memory);
   

//Below function requests price data with round and timestamp
    function verifyOracleProofV2(bytes calldata _bytesProof) 
    external     
    returns (PriceInfo memory);

}

```
{% endcode %}

This creates the interface that you will later apply in order to verify and fetch **S-Values** from Supra's Pull Contract.\\

**Step 2: Configure The S-Value Feed Address**

To verify and fetch the **S-Value** from a Supra Pull smart contract, first find the **S-Value** Pull Contract address for the preferred chain. When you have the address, create an instance of the ISupraOraclePull using the interface we previously defined:\
\
Supra contracts for each network can be found in our [network addresses](https://github.com/nolan-supra/docs-test/blob/oracles/data-feeds/pull-oracle/broken-reference/README.md) list.

```solidity
// Mock contract which can consume oracle pull data
contract MockOracleClient {
    /// @notice The oracle contract
    ISupraOraclePull internal oracle;

    /// @notice Event emitted when a pair price is received
    event PairPrice(uint256 pair, uint256 price, uint256 decimals);

    constructor(address oracle_) {
        oracle = ISupraOraclePull(oracle_);
    }
}
```

**Step 3: Receive and Verify the S-Value**

You can refer to: [GitHub Example](https://github.com/Entropy-Foundation/oracle-pull-example) to get the Proof<\_bytesProof> of S-Values in a Web2 environment.

Next, copy the following code to the smart contract to verify the price data received:

{% code overflow="wrap" %}
```solidity
// Get the price of a pair from oracle data received from supra pull model
  
    function GetPairPrice(bytes calldata _bytesProof, uint256 pair) external                 
    returns(uint256){
        ISupraOraclePull.PriceData memory prices = 
        oracle.verifyOracleProof(_bytesProof);
        uint256 price = 0;
        uint256 decimals = 0;
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            if (prices.pairs[i] == pair) {
                price = prices.prices[i];
                decimals = prices.decimals[i];
                break;
            }
        }
        require(price != 0, "Pair not found");
        return price;
    }


// Get the price of a pair from oracle data with round and  timestamp

    function GetPairPriceV2(bytes calldata _bytesProof, uint256 pair)    
    external                 
    returns(uint256,uint256,uint256){
        ISupraOraclePull.PriceInfo memory prices = 
        oracle.verifyOracleProofV2(_bytesProof);
        uint256 price = 0;
        uint256 decimals = 0;
        uint256 timestamp = 0;
        uint256 round = 0;
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            if (prices.pairs[i] == pair) {
                price = prices.prices[i]; 
                timestamp = prices.timestamp[i];
                round = prices.round[i];
                decimals = prices.decimal[i];
                break;
            }
        }
        require(price != 0, "Pair not found");
        return (price,timestamp,round);
    }

```
{% endcode %}

Thats it. Done!

Now you are ready to consume fast, low latency, and highly accurate data from Supra's Pull oracle.

**Recommended Best Practices**

Create a function with access control that updates the oracle using the function: _`updatePullAddress()`_

This will allow you to update the address of the Supra Pull contract after deployment, allowing you to future proof your contract. Access control is mandatory to prevent the undesired modification of the address.

```solidity
 function updatePullAddress(SupraOraclePull oracle_) 
    external 
    onlyOwner {
        oracle = oracle_;
    }
```

**Example Implementation**

Here's an example of what your implementation should look like:

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.24;

import "@openzeppelin/contracts/access/Ownable.sol";

interface ISupraOraclePull {
    struct PriceData {
        uint256[] pairs;
        uint256[] prices;
        uint256[] decimals;
    }
    
  
    struct PriceInfo {
        uint256[] pairs;
        uint256[] prices;
        uint256[] timestamp;
        uint256[] decimal;
        uint256[] round;
    }


//Below function(DORA1) verify the price and throws error if the proof is invalid. _bytesproof is the oracle proof to extract the pairs from Last Updated PriceData struct that does not contain timestamp and round.

    function verifyOracleProof(bytes calldata _bytesproof) 
    external 
    returns (PriceData memory);


   
// Below function (DORA2) verify the price and throws error if the proof is invalid. _bytesproof is the oracle proof to extract the pairs from Last Updated PriceData struct that does contain timestamp and round.  Stale prices can be determined using unixtimestamp.

    function verifyOracleProofV2(bytes calldata _bytesproof) 
    external     
    returns (PriceInfo memory);

}



// Mock contract which can consume oracle pull data
contract MockOracleClient is Ownable {
    ISupraOraclePull public oracle;

// Event emitted when a pair price is received
    event PairPrice(uint256 pair, uint256 price, uint256 decimals);

    constructor(ISupraOraclePull oracle_) Ownable(msg.sender){
        oracle = oracle_;
    }

//Get the price of a pair from oracle data without round and timestamp

    function GetPairPrice(bytes calldata _bytesProof, uint256 pair) external                 
    returns(uint256){
        ISupraOraclePull.PriceData memory prices = 
        oracle.verifyOracleProof(_bytesProof);
        uint256 price = 0;
        uint256 decimals = 0;
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            if (prices.pairs[i] == pair) {
                price = prices.prices[i];
                decimals = prices.decimals[i];
                break;
            }
        }
        require(price != 0, "Pair not found");
        return price;
    }

//Get the price of a pair from oracle data with round and  timestamp

    function GetPairPriceV2(bytes calldata _bytesProof, uint256 pair)    
    external                 
    returns(uint256,uint256,uint256){
        ISupraOraclePull.PriceInfo memory prices = 
        oracle.verifyOracleProofV2(_bytesProof);
        uint256 price = 0;
        uint256 decimals = 0;
        uint256 timestamp = 0;
        uint256 round = 0;
        for (uint256 i = 0; i < prices.pairs.length; i++) {
            if (prices.pairs[i] == pair) {
                price = prices.prices[i]; 
                timestamp = prices.timestamp[i];
                round = prices.round[i];
                decimals = prices.decimal[i];
                break;
            }
        }
        require(price != 0, "Pair not found");
        return (price,timestamp,round);
    }

    function updatePullAddress(ISupraOraclePull oracle_) 
    external 
    onlyOwner {
        oracle = oracle_;
    }
}

```
{% endcode %}
{% endtab %}

{% tab title="Move (Sui)" %}
**Step 1: Create The S-Value Interface**

Import interface from [https://github.com/Entropy-Foundation/dora-interface](https://github.com/Entropy-Foundation/dora-interface) git repository and add subdirectory mainnet or testnet for integration.

**Step 2: Configure The S-Value Feed Dependency**

Create your project and add the below dependencies in your Move.toml

\
**Testnet**

{% code overflow="wrap" %}
```rust
[dependencies]
SupraOracle = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "sui/testnet/supra_holder", rev = "testnet" }
```
{% endcode %}

**Mainnet**

{% code overflow="wrap" %}
```rust
[dependencies]
SupraOracle = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "sui/mainnet/supra_holder", rev = "master" }
```
{% endcode %}

**Step 3: Receive and Verify the S-Value**

Import supra\_holder contracts dependency in: _client\_example/sources/pull\_client.move_

```rust
use SupraOracle::SupraSValueFeed::OracleHolder;
use SupraOracle::price_data_pull_v2;
```

Here's an example of what your implementation should look like.

```rust
module client_example::pull_example_v2 {

    use std::vector;
    use sui::tx_context::TxContext;
    use sui::event;
    use supra_validator::validator_v2::DkgState;
    use SupraOracle::SupraSValueFeed::OracleHolder;
    use SupraOracle::price_data_pull_v2;

    /// Price pair data list
    struct PricePair has copy, drop, store  {
        pair: u32,
        price: u128,
        decimal: u16,
        round: u64,
    }

    struct EmitPrice has copy, drop {
        price_pairs: vector<PricePair>
    }

    /// Get pair price data with verify signature
    entry fun get_pair_price_v2(
        dkg_state: &DkgState,
        oracle_holder: &mut OracleHolder,
        bytes: vector<u8>
        _ctx: &mut TxContext,
    ) {
        let price_datas = price_data_pull_v2::verify_oracle_proof(dkg_state, oracle_holder, bytes, _ctx);

        let price_pairs = vector::empty<PricePair>();
        while (!vector::is_empty(&price_datas)) {
            let price_data = vector::pop_back(&mut price_datas);
            let (cc_pair_index, cc_price, cc_decimal, cc_round) = price_data_pull_v2::price_data_split(&price_data);
            vector::push_back(&mut price_pairs, PricePair { pair: cc_pair_index, price: cc_price, decimal: cc_decimal, round: cc_round });
        };

        event::emit(EmitPrice { price_pairs });
}

```

Thats it. Done!

Now you are ready to consume fast, and low latency data from Supra pull oracle.
{% endtab %}

{% tab title="Move(Aptos)" %}
**Step 1: Create The S-Value Interface**

Import interface from [https://github.com/Entropy-Foundation/dora-interface](https://github.com/Entropy-Foundation/dora-interface) git repository and add subdirectory mainnet or testnet for integration.

**Step 2: Configure The S-Value Feed Dependency**

Create your project and add the below dependencies in your Move.toml

**Testnet**

{% code overflow="wrap" %}
```solidity
[dependencies]
supra_holder = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "aptos/testnet/supra_holder", rev = "51c1875d86f20abe2a22e8d2ae7a3a2a2a56d5a1" 
```
{% endcode %}

\
**Mainnet**

{% code overflow="wrap" %}
```solidity
TBU 
```
{% endcode %}

**Step 3: Receive and Verify the S-Value**

Import supra\_holder contracts dependency in: `client_example/sources/pull_client.move`

```solidity
use supra_holder::price_data_pull;
```

Here's an example of what your implementation should look like.

```rust
module client_example::pull_example {

    use std::vector;
    use aptos_framework::account;
    use aptos_framework::event;
    use supra_holder::price_data_pull;

    /// Price pair data list
    struct PricePair has copy, drop, store  {
        pair: u32,
        price: u128,
        decimal: u16,
        round: u64,
    }

    /// Emit price pair struct
    struct EmitPrice has key {
        price_pairs: event::EventHandle<vector<PricePair>>
    }

    /// Its Initial function which will be executed automatically while deployed packages
    fun init_module(owner_signer: &signer) {
        move_to(owner_signer, EmitPrice { price_pairs: account::new_event_handle<vector<PricePair>>(owner_signer) });
    }

    /// Get pair price data with verify signature
    entry fun get_pair_price(
        account: &signer,
        dkg_state_addr: address,
        oracle_holder_addr: address,

        vote_smr_block_round: vector<vector<u8>>,
        vote_smr_block_timestamp: vector<vector<u8>>,
        vote_smr_block_author: vector<vector<u8>>,
        vote_smr_block_qc_hash: vector<vector<u8>>,
        vote_smr_block_batch_hashes: vector<vector<vector<u8>>>,
        vote_round: vector<u64>,

        min_batch_protocol: vector<vector<u8>>,
        min_batch_txn_hashes: vector<vector<vector<u8>>>,

        min_txn_cluster_hashes: vector<vector<vector<u8>>>,
        min_txn_sender: vector<vector<u8>>,
        min_txn_protocol: vector<vector<u8>>,
        min_txn_tx_sub_type: vector<u8>,

        scc_data_hash: vector<vector<u8>>,
        scc_pair: vector<vector<u32>>,
        scc_prices: vector<vector<u128>>,
        scc_timestamp: vector<vector<u128>>,
        scc_decimals: vector<vector<u16>>,
        scc_qc: vector<vector<u8>>,
        scc_round: vector<u64>,
        scc_id: vector<vector<u8>>,
        scc_member_index: vector<u64>,
        scc_committee_index: vector<u64>,

        batch_idx: vector<u64>,
        txn_idx: vector<u64>,
        cluster_idx: vector<u64>,
        sig: vector<vector<u8>>,
        pair_mask: vector<vector<bool>>,
    ) acquires EmitPrice {

        let price_datas = price_data_pull::verify_oracle_proof(account,dkg_state_addr,oracle_holder_addr,
            vote_smr_block_round, vote_smr_block_timestamp, vote_smr_block_author, vote_smr_block_qc_hash, vote_smr_block_batch_hashes, vote_round,
            min_batch_protocol, min_batch_txn_hashes,
            min_txn_cluster_hashes, min_txn_sender, min_txn_protocol, min_txn_tx_sub_type,
            scc_data_hash, scc_pair, scc_prices, scc_timestamp, scc_decimals, scc_qc, scc_round, scc_id, scc_member_index, scc_committee_index,
            batch_idx, txn_idx, cluster_idx, sig, pair_mask
        );

        let price_pairs = vector::empty<PricePair>();
        while (!vector::is_empty(&price_datas)) {

            let price_data = vector::pop_back(&mut price_datas);
            let (cc_pair_index, cc_price, cc_decimal, cc_round) = price_data_pull::price_data_split(&price_data);
            vector::push_back(&mut price_pairs, PricePair { pair: cc_pair_index, price: cc_price, decimal: cc_decimal, round: cc_round });
        };

        let event_handler = borrow_global_mut<EmitPrice>(@client_example);
        event::emit_event<vector<PricePair>>(&mut event_handler.price_pairs, price_pairs);
    }
}


```

Thats it. Done!

Now you are ready to consume fast, and low latency data from Supra pull oracle.
{% endtab %}
{% endtabs %}
