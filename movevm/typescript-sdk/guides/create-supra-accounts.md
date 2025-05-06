---
description: How to create new and import existing accounts.
---

# Create Supra Accounts

## [`SupraAccount`](https://sdk-docs.supra.com/classes/SupraAccount.html#constructor) class constructor

```typescript
new SupraAccount();
new SupraAccount(Buffer.from("YOUR_PRIVATE_KEY","hex"));
```

Create a new supra account or create one from the provided private key.\
If no `privateKeyBytes` parameter is provided, a new account will be generated.

**Parameters**

<table><thead><tr><th width="397">Name</th><th width="246">Type</th><th data-type="checkbox">Optional</th></tr></thead><tbody><tr><td>privateKeyBytes</td><td><code>Uint8Array</code></td><td>true</td></tr><tr><td>address</td><td><code>MaybeHexString</code></td><td>true</td></tr></tbody></table>

**Returns** [SupraAccount](https://sdk-docs.supra.com/classes/SupraAccount.html)

***

## [`fromDerivePath`](https://sdk-docs.supra.com/classes/SupraAccount.html#fromDerivePath) function

Create a new SupraAccount from mnemonic.

```typescript
SupraAccount.fromDerivePath(path, mnemonic);
```

**Parameters**

<table><thead><tr><th width="398">Name</th><th width="248">Type</th><th data-type="checkbox">Optional</th></tr></thead><tbody><tr><td>path</td><td>string</td><td>false</td></tr><tr><td>mnemonics</td><td>string</td><td>false</td></tr></tbody></table>

**Returns** [SupraAccount](https://sdk-docs.supra.com/classes/SupraAccount.html)

***

## Example

The SupraAccount class allows you to generate new accounts, import private key, or derive from mnemonics.

{% stepper %}
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
#### Create `supra_account_example.ts` and import dependencies

{% code title="supra_account_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import { SupraAccount} from "supra-l1-sdk";
```
{% endcode %}
{% endstep %}

{% step %}
#### Generate a new account

```typescript
const newAccount = new SupraAccount();
```
{% endstep %}

{% step %}
#### Generate an account from private key

```typescript
const accountFromPk = new SupraAccount(
    Buffer.from("YOUR_PRIVATE_KEY", "hex")
);
```
{% endstep %}

{% step %}
#### Generate an account from mnemonics

```typescript
const path = "m/44'/637'/0'/0'/0'";
const mnemonic = "";
const accountFromMnemonics = SupraAccount.fromDerivePath(path, mnemonic);
```
{% endstep %}

{% step %}
#### Complete code

{% code title="supra_account_example.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import { SupraAccount } from "supra-l1-sdk";

async function main(){

    const newAccount = new SupraAccount();
    console.log("newAccount: ", newAccount.address());

    const accountFromPk = new SupraAccount(Buffer.from("YOUR_PRIVATE_KEY", "hex"));
    console.log("accountFromPk Address: ", accountFromPk.address());

    const path = "m/44'/637'/0'/0'/0'";
    const mnemonic = "";
    const accountFromMnemonics = SupraAccount.fromDerivePath(path, mnemonic);
    console.log("accountFromMnemonics: ", accountFromMnemonics.address());

}

main();
```
{% endcode %}
{% endstep %}

{% step %}
#### Execute code `npx ts-node supra_account_example.ts`
{% endstep %}
{% endstepper %}
