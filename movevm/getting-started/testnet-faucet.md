---
description: Obtain testnet tokens for development.
hidden: true
---

# Testnet Faucet

{% stepper %}
{% step %}
#### Using Supra CLI

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../network-information.md) page.
{% endhint %}

Option 1: Using account profile.

```
supra move account fund-with-faucet --profile <PROFILE_NAME> --url <RPC_URL>
```

Option 2: Using account address.

```
supra move account fund-with-faucet --account <ADDRESS> --url <RPC_URL>
```

Example Output:

```json
{
  "Accepted": "TXN-HASH"
}
```
{% endstep %}

{% step %}
#### Using GET request

Make a GET call to the faucet endpoint by inserting your address into the following URL.

```
https://rpc-testnet.supra.com/rpc/v1/wallet/faucet/<YOUR_ACCOUNT_ADDRESS>
```

Example Output:

```json
{
    "Accepted": "TXN-HASH"
}
```
{% endstep %}

{% step %}
#### Using [StarKey Wallet](https://www.starkey.app)

* Select Testnet from the network dropdown.
* Select the Supra token from the asset list.
* Press the "Collect" faucet button.
{% endstep %}
{% endstepper %}
