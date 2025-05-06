---
description: >-
  Learn how to create, fund, manage, and interact with blockchain accounts using
  the Supra CLI.
---

# Accounts

{% hint style="danger" %}
On May 2nd, 2025 a new version of the CLI was released. The new image (v9.0.12) introduced a number of changes, including an improved profile feature which deprecated the key command of the previous version (v6.3.0).\
\
Please be advised that this page has not yet been updated.
{% endhint %}

## What are Supra CLI Accounts?

Accounts in the Supra CLI are blockchain identities used to interact with the Supra network. They allow users to perform actions such as creating new accounts, funding them with tokens, checking balances, and transferring assets. These accounts serve as the foundation for managing transactions and participating in blockchain activities.

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../network-information.md) page.
{% endhint %}

***

## CLI Commands for Accounts

Here's how to use the Supra CLI commands to work with accounts:

{% hint style="info" %}
Not all CLI commands are listed here. Use the `--help` option to see the full list.
{% endhint %}

## Fund Your Profiles

Fund an account with SUPRA tokens from a faucet URL:

```powershell
supra move account fund-with-faucet [OPTIONS] --url [URL_RPC]
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--sender-account <mark style="color:orange;"><</mark>SENDER\_ACCOUNT<mark style="color:orange;">></mark>: Sender account address
  * \--profile <mark style="color:orange;"><</mark>PROFILE<mark style="color:orange;">></mark>: Profile to use from the CLI config
  * \--url: <mark style="color:orange;"><</mark>URL\_RPC<mark style="color:orange;">>:</mark> Full node on the network
  * \--account <mark style="color:orange;"><</mark>ACCOUNT<mark style="color:orange;">></mark>: Address to fund

{% hint style="info" %}
If the account wasn't previously created, it will be created when being funded
{% endhint %}

**Example:**

```powershell
supra move account fund-with-faucet --profile exampleAddress --url https://rpc-testnet.supra.com
```

**Result:**

```powershell
URL: https://rpc-testnet.supra.com/rpc/v1/wallet/faucet/e5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c
{
  "Accepted": "0x6cac6e55b5c63da2d15871de27894cce627cf342451d167c72f9104267e340ef"
}
```

## Check Profile Balance

Show the account's balance of SUPRA coins:

```powershell
supra move account balance [OPTIONS] --url [URL_RPC]
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--account <mark style="color:orange;"><</mark>ACCOUNT<mark style="color:orange;">></mark>: Address of the account you want to list balance
  * \--profile <mark style="color:orange;"><</mark>PROFILE<mark style="color:orange;">></mark>: Profile to use from the CLI config
  * \--url <mark style="color:orange;"><</mark>URL\_RPC<mark style="color:orange;">></mark>: URL to a full node on the network

**Example:**

```powershell
supra move account balance --profile exampleAddress --url https://rpc-testnet.supra.com
```

**Result:**

```
URL: https://rpc-testnet.supra.com/rpc/v1/accounts/e5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c/reso
urces/0x1::coin::CoinStore%3C0x1::supra_coin::SupraCoin%3E
{"result":[{"coin":{"value":"500000000"},"deposit_events":{"counter":"1","guid":{"id":{"addr":"0xe5992efbb8e2f5978964
335536c334e1d71d2d7afec00fbda0446b893d2b988c","creation_num":"2"}}},"frozen":false,"withdraw_events":{"counter":"0","guid":
{"id":{"addr":"0xe5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c","creation_num":"3"}}}}]}
```

## Transfer $SUPRA

Transfer $SUPRA between accounts

```powershell
supra move account transfer [OPTIONS] --account <ACCOUNT> --amount <AMOUNT> --url [URL_RPC]
```

**Additional Options:**

* **\[**<mark style="color:orange;">OPTIONS</mark>**]**
  * \--account <mark style="color:orange;"><</mark>ACCOUNT<mark style="color:orange;">></mark>: Address of account to send $SUPRA
  * \--url <mark style="color:orange;"><</mark>URL\_RPC<mark style="color:orange;">></mark>: URL to a full node on the network
  * \--amount <mark style="color:orange;"><</mark>AMOUNT<mark style="color:orange;">></mark> : Amount of Quants <mark style="color:orange;">(10^-8 SUPRA)</mark> to transfer

{% hint style="info" %}
This will be used to override associated settings such as the REST URL, the Faucet URL, and the private key arguments.
{% endhint %}

**Example:**

```powershell
supra move account transfer --account 0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d --amount 1000 --url https://rpc-testnet.supra.com
```

**Result:**

```powershell
account: e5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c
URL: https://rpc-testnet.supra.com/rpc/v1/transactions/submit
Chain_id = 6
Sequence Number = 1
URL: https://rpc-testnet.supra.com/rpc/v1/transactions/8f6607c103763fcc32c79ca60ba58cebac5d00b5b0c34f056087212a263da056
{
  "authenticator": {
    "Move": {
      "Ed25519": {
        "public_key": "0x0cf652de472c6aea9f33442a3fe91a9ce3c7181a3cf1a9119b38c4444fbefd4e",
        "signature": "0xe8c9e6901cae727640a3309187537c56a37cf8e04ef4745e8f69b5e6c4c3969f01f5e38956a06a53f53c9b7a3eb681e27d10bd5e0425a97a3a42020d111bc90b"
      }
    }
  },
  "block_header": {
    "hash": "0x760e49d5731a92875b8046c92f4b77d81ba88e1781975c6061c06beee13cbf34",
    "height": 5454904,
    "timestamp": {
      "microseconds_since_unix_epoch": 1734471953424357,
      "utc_date_time": "2024-12-17T21:45:53.424357+00:00"
    }
  },
  "hash": "0x8f6607c103763fcc32c79ca60ba58cebac5d00b5b0c34f056087212a263da056",
  "header": {
    "chain_id": 6,
    "expiration_timestamp": {
      "microseconds_since_unix_epoch": 1734475552000000,
      "utc_date_time": "2024-12-17T22:45:52+00:00"
    },
    "sender": {
      "Move": "0xe5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c"
    },
    "sequence_number": 1,
    "gas_unit_price": 100,
    "max_gas_amount": 500000
  },
  "payload": {
    "Move": {
      "type": "entry_function_payload",
      "function": "0x1::supra_account::transfer_coins",
      "type_arguments": [
        "0x1::supra_coin::SupraCoin"
      ],
      "arguments": [
        "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d",
        "1000"
      ]
    }
  },
  "output": {
    "Move": {
      "gas_used": 9,
      "events": [
        {
          "guid": {
            "creation_number": "0",
            "account_address": "0x0"
          },
          "sequence_number": "0",
          "type": "0x1::coin::CoinWithdraw",
          "data": {
            "account": "0xe5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c",
            "amount": "1000",
            "coin_type": "0x1::supra_coin::SupraCoin"
          }
        },
        {
          "guid": {
            "creation_number": "3",
            "account_address": "0xe5992efbb8e2f5978964335536c334e1d71d2d7afec00fbda0446b893d2b988c"
          },
          "sequence_number": "1",
          "type": "0x1::coin::WithdrawEvent",
          "data": {
            "amount": "1000"
          }
        },
        {
          "guid": {
            "creation_number": "0",
            "account_address": "0x0"
          },
          "sequence_number": "0",
          "type": "0x1::coin::CoinDeposit",
          "data": {
            "account": "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d",
            "amount": "1000",
            "coin_type": "0x1::supra_coin::SupraCoin"
          }
        },
        {
          "guid": {
            "creation_number": "2",
            "account_address": "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d"
          },
          "sequence_number": "2",
          "type": "0x1::coin::DepositEvent",
          "data": {
            "amount": "1000"
          }
        },
        {
          "guid": {
            "creation_number": "0",
            "account_address": "0x0"
          },
          "sequence_number": "0",
          "type": "0x1::transaction_fee::FeeStatement",
          "data": {
            "execution_gas_units": "4",
            "io_gas_units": "5",
            "storage_fee_quants": "0",
            "storage_fee_refund_quants": "0",
            "total_charge_gas_units": "9"
          }
        }
      ],
      "vm_status": "Executed successfully"
    }
  },
  "status": "Success"
}
```

## Multiple Wallets

Creates a wallet requiring multiple approvals for transactions.

```powershell
supra move account derive-multi-sig-address --owner-address <OWNER_ADDRESS> --sequence-number <SEQUENCE_NUMBER>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--owner-address <mark style="color:orange;"><</mark>OWNER\_ADDRESS<mark style="color:orange;">></mark>: Specifies the blockchain address of the account owner for a given operation.
  * \--sequence-number <mark style="color:orange;"><</mark>SEQUENCE\_NUMBER<mark style="color:orange;">></mark>: Indicates the unique sequence number to track and order transactions for the specified account.

**Example:**

```powershell
supra move account derive-multi-sig-address --owner-address 0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d --sequence-number 2
```

**Result:**

```powershell
833eb6eb23a73f3e343045cd999a8f83c1b188680f0a68353e5c9d52efc03f35
```
