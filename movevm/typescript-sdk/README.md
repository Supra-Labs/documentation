# TypeScript SDK

The Supra L1 TypeScript SDK provides a convenient way to interact with and perform operations on Supra. It offers a set of utility functions, classes, and types to simplify the integration process and enhance developer productivity.

## Install the TypeScript SDK

```sh
npm install supra-l1-sdk
```

## Import the Package

```typescript
import { HexString, SupraAccount, SupraClient, BCS } from "supra-l1-sdk";
```

***

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td>Guides</td><td></td><td><a href="guides/">guides</a></td></tr><tr><td></td><td>SDK Documentation</td><td></td><td><a href="https://sdk-docs.supra.com/index.html">https://sdk-docs.supra.com/index.html</a></td></tr><tr><td></td><td>SDK Repository</td><td></td><td><a href="https://github.com/Entropy-Foundation/supra-l1-sdk/tree/master">https://github.com/Entropy-Foundation/supra-l1-sdk/tree/master</a></td></tr></tbody></table>

***

## Example

{% stepper %}
{% step %}
### Initialize a new project

{% code overflow="wrap" %}
```
npm init && npm add -D typescript @types/node ts-node && npx tsc --init && mkdir src && > src/quickstart.ts
```
{% endcode %}
{% endstep %}

{% step %}
### Install the TypeScript SDK

```
npm install supra-l1-sdk
```
{% endstep %}

{% step %}
### Import dependencies

```typescript
import {
  HexString,
  SupraAccount,
  SupraClient,
  BCS,
  TxnBuilderTypes,
} from "supra-l1-sdk";
```
{% endstep %}

{% step %}
### Initialize the SupraClient

```typescript
let supraClient = await SupraClient.init(
  "https://rpc-testnet.supra.com/"
);
```
{% endstep %}

{% step %}
### Initialize a SupraAccount

```typescript
let senderAccount = new SupraAccount(
  Uint8Array.from(
    Buffer.from(
      "2b9654793a999d1d487dabbd1b8f194156e15281fa1952af121cc97b27578d89",
      "hex"
    )
  )
);
```
{% endstep %}

{% step %}
### Fund the account with the testnet faucet

```typescript
console.log(
  "Funding Sender With Faucet: ",
  // To Fund Account With Test Supra Tokens
  await supraClient.fundAccountWithFaucet(senderAccount.address())
);
```
{% endstep %}

{% step %}
### Set the receiver address

```typescript
let receiverAddress = new HexString(
  "0xb8922417130785087f9c7926e76542531b703693fdc74c9386b65cf4427f4e80"
);
```
{% endstep %}

{% step %}
### Transfer supra to the receiver address

```typescript
// To Transfer Supra Coin From Sender To Receiver
let txResData = await supraClient.transferSupraCoin(
  senderAccount,
  receiverAddress,
  BigInt(100000000),
  {
    enableTransactionWaitAndSimulationArgs: {
      enableWaitForTransaction: true,
      enableTransactionSimulation: true,
    },
  }
);
console.log("Transfer SupraCoin TxRes: ", txResData);
```
{% endstep %}

{% step %}
### Full quickstart code

{% code title="quickstart.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import {
    HexString,
    SupraAccount,
    SupraClient
  } from "supra-l1-sdk";
  
  (async () => {
  
    // To Create Instance Of Supra Client, But In This Method We Don't Need To Pass ChainId.
    // ChainId Will Be Identified At Instance Creation Time By Making RPC Call.
    let supraClient = await SupraClient.init(
      "https://rpc-autonet.supra.com/"
    );
  
    //Init a SupraAccount from a private key.
    let senderAccount = new SupraAccount(
      Uint8Array.from(
        Buffer.from(
          "2b9654793a999d1d487dabbd1b8f194156e15281fa1952af121cc97b27578d89",
          "hex"
        )
      )
    );

    //Fund the sender account with the testnet faucet
    await supraClient.fundAccountWithFaucet(senderAccount.address())

    //Set the receiver address
    let receiverAddress = new HexString(
      "0xb8922417130785087f9c7926e76542531b703693fdc74c9386b65cf4427f4e80"
    );

    // To Transfer Supra Coin From Sender To Receiver
    let txResData = await supraClient.transferSupraCoin(
      senderAccount,
      receiverAddress,
      BigInt(1000),
      {
        enableTransactionWaitAndSimulationArgs: {
          enableWaitForTransaction: true,
          enableTransactionSimulation: true,
        },
      }
    );

    //Output the transaction data
    console.log("Transfer SupraCoin TxRes: ", txResData);

  })();
```
{% endcode %}
{% endstep %}

{% step %}
### Execute the code: `npx ts-node src/quickstart.ts`
{% endstep %}
{% endstepper %}
