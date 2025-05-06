---
description: How to publish a package using the TypeScript SDK.
---

# Publish a Package

## [`publishPackage`](https://sdk-docs.supra.com/classes/SupraClient.html#publishPackage) function

```typescript
await supraClient.publishPackage(senderAccount, packageMetadata, modulesCode)
```

**Parameters**

| Name                    | Type                                                                                            |
| ----------------------- | ----------------------------------------------------------------------------------------------- |
| senderAccount           | [`SupraAccount`](https://sdk-docs.supra.com/classes/SupraAccount.html)                          |
| packageMetadata         | `Uint8Array`                                                                                    |
| modulesCode             | `Uint8Array[]`                                                                                  |
| optionalTransactionArgs | [`OptionalTransactionArgs`](https://sdk-docs.supra.com/interfaces/OptionalTransactionArgs.html) |

**Returns**

| Name                          | Type                                                                                  |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| Promise\<TransactionResponse> | [TransactionResponse](https://sdk-docs.supra.com/interfaces/TransactionResponse.html) |

{% code title="TransactionResponse" %}
```json
{
  txHash: '',
  result: ''
}
```
{% endcode %}

***

## Example

The `publishPackage` function requires the package metadata and module bytecode as the second and third parameter. These are obtained through the `build-publish-payload` command of the Supra CLI. Once obtained, parse the JSON file for the data and pass it to the `publishPackage` function.

{% stepper %}
{% step %}
#### Build the publish payload with the Supra CLI

{% code overflow="wrap" %}
```
supra move tool build-publish-payload --package-dir /supra/configs/move_workspace/{YOUR_PROJECT_NAME} --json-output-file ./configs/move_workspace/{YOUR_PROJECT_NAME}/publish-payload.json
```
{% endcode %}
{% endstep %}

{% step %}
#### Initialize a new project

{% code overflow="wrap" %}
```typescript
npm init && npm add -D typescript @types/node ts-node && npx tsc --init
```
{% endcode %}
{% endstep %}

{% step %}
#### Install Supra's TypeScript SDK

```
npm install supra-l1-sdk
```
{% endstep %}

{% step %}
#### Create `publish_example.ts` and import dependencies

{% code title="publish_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import fs from "fs";
import { HexString, SupraAccount, SupraClient } from "supra-l1-sdk";
```
{% endcode %}
{% endstep %}

{% step %}
#### Parse the JSON file and extract data

{% code title="publish_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
function getPackageData(filePath: string) {
    const jsonData = JSON.parse(fs.readFileSync(filePath, "utf8"));
    const packageMetadata = new HexString(jsonData.args[0].value).toUint8Array();
    const modulesCode = [];

    for(let e of jsonData.args[1].value){
      modulesCode.push(new HexString(e).toUint8Array())
    }
  
    return { packageMetadata, modulesCode };
}
```
{% endcode %}
{% endstep %}

{% step %}
#### Call the `publishPackage` function

{% code title="publish_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
async function main(){

    const { packageMetadata, modulesCode } = getPackageData("YOUR_PATH_TO_JSON_FILE");

    let supraClient = await SupraClient.init(
        "https://rpc-testnet.supra.com/"
    );

    let senderAccount = new SupraAccount(
        new HexString("YOUR_PRIVATE_KEY").toUint8Array()
    );

    const publishTxn = await supraClient.publishPackage(senderAccount, packageMetadata, modulesCode)

    console.log(publishTxn);

}
```
{% endcode %}
{% endstep %}

{% step %}
#### Complete code

{% code title="publish_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import fs from "fs";
import { HexString, SupraAccount, SupraClient } from "supra-l1-sdk";


function getPackageData(filePath: string) {
    const jsonData = JSON.parse(fs.readFileSync(filePath, "utf8"));
    const packageMetadata = new HexString(jsonData.args[0].value).toUint8Array();
    const modulesCode = [];

    for(let e of jsonData.args[1].value){
      modulesCode.push(new HexString(e).toUint8Array())
    }
  
    return { packageMetadata, modulesCode };
}

async function main(){

    const { packageMetadata, modulesCode } = getPackageData("YOUR_PATH_TO_JSON_FILE");

    let supraClient = await SupraClient.init(
        "https://rpc-testnet.supra.com/"
    );

    let senderAccount = new SupraAccount(
        new HexString("YOUR_PRIVATE_KEY").toUint8Array()
    );

    const publishTxn = await supraClient.publishPackage(senderAccount, packageMetadata, modulesCode)

    console.log(publishTxn);

}

main()
```
{% endcode %}
{% endstep %}

{% step %}
#### Execute code `npx ts-node publish_example.ts`
{% endstep %}
{% endstepper %}
