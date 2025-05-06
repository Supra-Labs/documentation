---
description: Create your first Dapp using StarKey wallet, the official wallet of Supra
---

# Create a dApp with StarKey

## Setup the project

{% stepper %}
{% step %}
#### Initialize the frontend

```
npx create-react-app supra-dapp --template typescript
```
{% endstep %}

{% step %}
#### Navigate into the project directory

```
cd supra-dapp
```
{% endstep %}

{% step %}
#### Install the TypeScript SDK

```
npm install supra-l1-sdk
```
{% endstep %}
{% endstepper %}

## Start building

{% stepper %}
{% step %}
#### Open the `App.tsx` file and update it

Remove the template code and replace it with the code below. This will give us a fresh foundation to work with.

```typescript
function App() {
  return <div></div>;
}
 
export default App;
```
{% endstep %}

{% step %}
#### Import dependencies

We'll be utilizing the react library along with useful functions from the `supra-l1-sdk`.

```typescript
import React, {useEffect, useState} from "react";
import { HexString, TxnBuilderTypes, BCS } from "supra-l1-sdk";
```
{% endstep %}

{% step %}
#### Initialize the `supraProvider` and state hooks

`supraProvider` - used to interact with StarKey wallet.

`isStarKeyInstalled` - boolean value of StarKey extension installation status

`accounts` - Array of connected accounts

`networkData` - Chain ID for the connected network

```typescript
import React, {useEffect, useState} from "react";
import { HexString, TxnBuilderTypes, BCS } from "supra-l1-sdk";

function App() {
  
    let supraProvider: any = typeof window !== "undefined" && (window as any)?.starkey?.supra;
    
    const [isStarkeyInstalled, setIsStarkeyInstalled] = useState<boolean>(!!supraProvider);
    const [accounts, setAccounts] = useState<string[]>([]);
    const [networkData, setNetworkData] = useState<any>();

  return <div></div>;
}
```

All code from this step forward will be included within the `App()` function.
{% endstep %}

{% step %}
#### Check for the injected StarKey object

The following code checks for the injected StarKey object every 500ms for up to 5 seconds. If found, the function will update the StarKey installation status and obtain the connected accounts.

```typescript
  const checkForStarkey = () => {
    const intervalId = setInterval(() => {
      if ((window as any)?.starkey) {
          supraProvider = (window as any)?.starkey.supra;
          clearInterval(intervalId);
          setIsStarkeyInstalled(true);
          updateAccounts().then();
      }
    }, 500);

    setTimeout(() => {
      clearInterval(intervalId);
    }, 5000);
  };
```
{% endstep %}

{% step %}
#### Get the connected accounts

The following code obtains the connected accounts (if any) and stores them.

```typescript
  const updateAccounts = async () => {
    if (supraProvider) {
      try {
        const response_acc = await supraProvider.account();
        if (response_acc.length > 0) {
          setAccounts(response_acc);
        } else {
          setAccounts([]);
        }
      } catch (e) {
        setAccounts([]);
      }
      getNetworkData().then();
    }
  };
```
{% endstep %}

{% step %}
#### Get the connected network data

The following code obtains the network data (chain id) of the connected wallet.

```typescript
  const getNetworkData = async () => {
    if (supraProvider) {
      const data = await supraProvider.getChainId();
      console.log(data);
      if (data) {
          setNetworkData(data)
      }
    }
  };
```
{% endstep %}

{% step %}
#### Initialize additional hooks

The following code will assist in maintaining updated network/account data while also handling events emitted by the injected StarKey object.

```typescript
  useEffect(() => {
    if (supraProvider) {
      supraProvider.on("accountChanged", (acc: string[]) => {
        setAccounts(acc);
      });
      supraProvider.on("networkChanged", (data: any) => {
        setNetworkData(data);
      });
      supraProvider.on("disconnect", () => {
        resetWalletData();
      });
    }
  }, [supraProvider]);
  
  useEffect(() => {
    if (accounts) {
        getNetworkData().then();
    }
  }, [accounts]);
```
{% endstep %}

{% step %}
#### Handle wallet connections

The following code will handle the wallet connections, triggered through a button click (which we will setup later) or an emitted event from the StarKey object.

When a wallet is connected, the accounts must be updated. When a wallet is disconnected, we must clear the stored accounts and network data.

```typescript
  const connectWallet = async () => {
    const response = await supraProvider.connect();
    updateAccounts().then();
  };

  const disconnectWallet = async () => {
    if (supraProvider) {
        await supraProvider.disconnect();
    }
    resetWalletData();
  };
  
  const resetWalletData = ()=>{
    setAccounts([]);
    setNetworkData({});
  }
```
{% endstep %}

{% step %}
#### Switch between mainnet and testnet

The following code will be triggered through a button click to prompt the user to switch between mainnet and testnet

```typescript
  const switchToTestnet = async (id:string) => {
    if (supraProvider) {
      await supraProvider.changeNetwork({chainId:"6"});
      await getNetworkData()
    }
  };

  const switchToMainnet = async () => {
    if (supraProvider) {
      await supraProvider.changeNetwork({chainId:"8"});
      await getNetworkData()
    }
  };
```
{% endstep %}

{% step %}
#### Send a transaction

The following code creates a raw payload that calls the `transfer` function of the `supra_account` module. Once the raw transaction payload is created, it uses the `supraProvider.sendTransaction` function to prompted the connect wallet to sign and submit.\
\
It will transfer `1 supra` to the address `0x8de4158b48633d853186d5fc790718e5821d7d3c4855e06bcd97b105389a7d0f` .

```typescript
const sendRawTransaction = async () => {
      const txExpiryTime = (Math.ceil(Date.now() / 1000) + 30) //30 seconds
      /** OptionalTransactionPayloadArgs {
              maxGas?: bigint;
              gasUnitPrice?: bigint;
              txExpiryTime?: bigint;
            }*/
      const optionalTransactionPayloadArgs = {
          txExpiryTime
      }
      const rawTxPayload = [
          accounts[0],
          0,
          "0000000000000000000000000000000000000000000000000000000000000001",
          "supra_account",
          "transfer",
          [],
          [
              new HexString("0x8de4158b48633d853186d5fc790718e5821d7d3c4855e06bcd97b105389a7d0f").toUint8Array(),
              BCS.bcsSerializeUint64(100000000)
          ],
          optionalTransactionPayloadArgs
      ];
      const data = await supraProvider.createRawTransactionData(rawTxPayload);
      if (data) {
          const params = {
              data: data,
              /*from: accounts[0],
              to: "",
              chainId: networkData.chainId,
              value: "100000000",*/
          };
          const txHash = await supraProvider.sendTransaction(params);
      }
  };
```
{% endstep %}

{% step %}
#### Create the buttons displayed on the frontend

The following code renders buttons on the frontend that will interact with the above functions.

```typescript
    return <div>
      <button onClick={connectWallet}>Connect Wallet</button>
      <button onClick={disconnectWallet}>Disconnect Wallet</button>
      <p>Connected Account(s): {JSON.stringify(accounts)}</p>
      <p>Network Data: {JSON.stringify(networkData)}</p>
      <button onClick={switchToTestnet}>Switch to Testnet</button>
      <button onClick={switchToMainnet}>Switch to Mainnet</button>
      <br></br>
      <button onClick={sendRawTransaction}>Send Test Transaction</button>
    </div>;
```
{% endstep %}

{% step %}
#### That's it! Run your project

```
npm start
```
{% endstep %}

{% step %}
#### Final code for `App.tsx`

```typescript
import React, {useEffect, useState} from "react";
import { HexString, TxnBuilderTypes, BCS } from "supra-l1-sdk";


function App() {

  let supraProvider: any = typeof window !== "undefined" && (window as any)?.starkey?.supra;
  
  const [isStarkeyInstalled, setIsStarkeyInstalled] = useState<boolean>(!!supraProvider);
  const [accounts, setAccounts] = useState<string[]>([]);
  const [networkData, setNetworkData] = useState<any>();

  const checkForStarkey = () => {
    const intervalId = setInterval(() => {
      if ((window as any)?.starkey) {
          supraProvider = (window as any)?.starkey.supra;
          clearInterval(intervalId);
          setIsStarkeyInstalled(true);
          updateAccounts().then();
      }
    }, 500);

    setTimeout(() => {
      clearInterval(intervalId);
    }, 5000);
  };

  const getNetworkData = async () => {
    if (supraProvider) {
      const data = await supraProvider.getChainId();
      console.log(data);
      if (data) {
          setNetworkData(data)
      }
    }
  };

  const updateAccounts = async () => {
    if (supraProvider) {
      try {
        const response_acc = await supraProvider.account();
        if (response_acc.length > 0) {
          setAccounts(response_acc);
        } else {
          setAccounts([]);
        }
      } catch (e) {
        setAccounts([]);
      }
      getNetworkData().then();
    }
  };


  useEffect(() => {
    if (supraProvider) {
      supraProvider.on("accountChanged", (acc: string[]) => {
        setAccounts(acc);
      });
      supraProvider.on("networkChanged", (data: any) => {
        setNetworkData(data);
      });
      supraProvider.on("disconnect", () => {
        resetWalletData();
      });
    }
  }, [supraProvider]);
  
  useEffect(() => {
    if (accounts) {
        getNetworkData().then();
    }
  }, [accounts]);


  
  const resetWalletData = ()=>{
    setAccounts([]);
    setNetworkData({});
  }

  const connectWallet = async () => {
    const response = await supraProvider.connect();
    updateAccounts().then();
  };



  const disconnectWallet = async () => {
    if (supraProvider) {
        await supraProvider.disconnect();
    }
    resetWalletData();
  };

  const switchToTestnet = async () => {
    if (supraProvider) {
        await supraProvider.changeNetwork({chainId:"6"});
        await getNetworkData()
    }
};
const switchToMainnet = async () => {
  if (supraProvider) {
      await supraProvider.changeNetwork({chainId:"8"});
      await getNetworkData()
  }
};

  const sendRawTransaction = async () => {
      const txExpiryTime = (Math.ceil(Date.now() / 1000) + 30) //30 seconds
      /** OptionalTransactionPayloadArgs {
              maxGas?: bigint;
              gasUnitPrice?: bigint;
              txExpiryTime?: bigint;
            }*/
      const optionalTransactionPayloadArgs = {
          txExpiryTime
      }
      const rawTxPayload = [
          accounts[0],
          0,
          "0000000000000000000000000000000000000000000000000000000000000001",
          "supra_account",
          "transfer",
          [],
          [
              new HexString("0x8de4158b48633d853186d5fc790718e5821d7d3c4855e06bcd97b105389a7d0f").toUint8Array(),
              BCS.bcsSerializeUint64(100000000)
          ],
          optionalTransactionPayloadArgs
      ];
      const data = await supraProvider.createRawTransactionData(rawTxPayload);
      if (data) {
          const params = {
              data: data,
              /*from: accounts[0],
              to: "",
              chainId: networkData.chainId,
              value: "100000000",*/
          };
          const txHash = await supraProvider.sendTransaction(params);
      }
  };


    return <div>
      <button onClick={connectWallet}>Connect Wallet</button>
      <button onClick={disconnectWallet}>Disconnect Wallet</button>
      <p>Connected Account(s): {JSON.stringify(accounts)}</p>
      <p>Network Data: {JSON.stringify(networkData)}</p>
      <button onClick={switchToTestnet}>Switch to Testnet</button>
      <button onClick={switchToMainnet}>Switch to Mainnet</button>
      <br></br>
      <button onClick={sendRawTransaction}>Send Test Transaction</button>
    </div>;
  }
 
export default App;
```
{% endstep %}
{% endstepper %}
