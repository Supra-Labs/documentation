---
description: >-
  Learn how to compile, publish, test, and manage Move packages and smart
  contracts using the Supra CLI.
---

# Tools

{% hint style="danger" %}
On May 2nd, 2025 a new version of the CLI was released. The new image (v9.0.12) introduced a number of changes, including an improved profile feature which deprecated the key command of the previous version (v6.3.0).\
\
Please be advised that this page has not yet been updated.
{% endhint %}

## What are Supra CLI Tools?

Tools in the Supra CLI are a set of commands designed to help developers interact with Move packages and smart contracts on the Supra network. They allow users to perform tasks such as compiling, publishing, testing, and running Move scripts, as well as documenting packages and managing on-chain resources. These tools serve as the foundation for building, deploying, and maintaining blockchain applications efficiently.

***

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../network-information.md) page.
{% endhint %}

## CLI Commands for Tools

Here's how to use the Supra CLI commands to work with tools:

{% hint style="info" %}
Not all CLI commands are listed here. Use the `--help` option to see the full list.
{% endhint %}

## Clean Derived Artifacts

Removes derived artifacts of a Move package:

```powershell
supra move tool clean [OPTIONS]
```

**Additional Options:**

* \--package-dir <mark style="color:orange;"><</mark>PACKAGE\_DIR<mark style="color:orange;">></mark>: Path to a move package (the folder with a Move.toml file)

**Example:**

```powershell
supra move tool clean
```

**Result:**

```powershell
{
  "Result": "succeeded"
}
```

## Compile a Package

Compiles a Move package and returns associated ModuleIds:

```powershell
supra move tool compile --package-dir /supra/configs/move_workspace/<PROJECT_NAME>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--package-dir <mark style="color:orange;"><</mark>PACKAGE\_DIR<mark style="color:orange;">></mark>: Path to a move package (the folder with a Move.toml file)

**Example:**

```powershell
supra move tool compile --package-dir /supra/configs/move_workspace/move
```

**Result:**

```powershell
Compiling, may take a little while to download git dependencies...
FETCHING GIT DEPENDENCY https://github.com/Entropy-Foundation/aptos-core.git
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
INCLUDING DEPENDENCY SupraFramework
BUILDING supra_move

{
  "Result": [
    "ec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d::transfer"
  ]
}
```

## Initialize a Package

Creates a new Move package at a specified location:

```powershell
supra move tool init --name <NAME>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--name <mark style="color:orange;"><</mark>NAME<mark style="color:orange;">></mark>: Name of the new Move package

**Example:**

```powershell
supra move tool move_project --name move
```

**Result:**

```powershell
{
  "Result": "Success"
}
```

## Publish a Smart Contract

Publishes a compiled smart contract to the Supra blockchain:

```powershell
supra move tool publish --package-dir /supra/configs/move_workspace/<PROJECT_NAME> 
--profile <YOUR_PROFILE> --url <RPC_URL>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--package-dir <mark style="color:orange;"><</mark>PACKAGE\_DIR<mark style="color:orange;">></mark>: Path to a move package (the folder with a Move.toml file)
  * \--profile <mark style="color:orange;"><</mark>PROFILE<mark style="color:orange;">></mark>: Profile to use from the CLI config
  * \--url <mark style="color:orange;"><</mark>URL<mark style="color:orange;">></mark>: URL to a full node on the network

**Example:**

```powershell
supra move tool publish --package-dir /supra/configs/move_workspace/move 
--profile exampleAddress --url https://rpc-testnet.supra.com
```

**Result:**

```powershell
account: ec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d
URL: https://rpc-testnet.supra.com/rpc/v1/transactions/submit
Chain_id = 6
Sequence Number = 1
URL: https://rpc-testnet.supra.com/rpc/v1/transactions/e18bdea56737ce52c0b90bcb7f416ca91576ff6d1c75ee8618026fe89d8d797c
{
  "authenticator": {
    "Move": {
      "Ed25519": {
        "public_key": "0x680fffd96e017cc0dacf9350b7258e971e02d27c2e2686483bd2dbee5c13f5f5",
        "signature": "0x97dc4930b11cd8bfc8791a4d96291b18b73c6804644bc046f75f9452838b4becafee676ada60b594155d4e99ac010ed999fced934c39bdc4c2eac7b761247b02"
      }
    }
  },
  "block_header": {
    "hash": "0x8f21036f4de17e1e5e49adb67f4d78adfef637399d06e519f4bf27f143a8eec0",
    "height": 5526696,
    "timestamp": {
      "microseconds_since_unix_epoch": 1734539568750390,
      "utc_date_time": "2024-12-18T16:32:48.750390+00:00"
    }
  },
  "hash": "0xe18bdea56737ce52c0b90bcb7f416ca91576ff6d1c75ee8618026fe89d8d797c",
  "header": {
    "chain_id": 6,
    "expiration_timestamp": {
      "microseconds_since_unix_epoch": 1734539599000000,
      "utc_date_time": "2024-12-18T16:33:19+00:00"
    },
    "sender": {
      "Move": "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d"
    },
    "sequence_number": 1,
    "gas_unit_price": 100,
    "max_gas_amount": 500000
  },
  "payload": {
    "Move": {
      "type": "entry_function_payload",
      "function": "0x1::code::publish_package_txn",
      "type_arguments": [],
      "arguments": [
        "0x0a73757072615f6d6f7665010000000000000000403043453446333545323733354539384338333441384542443034333245393645443835353132364538333035364430393846393832303437423536314132383481021f8b08000000000002ff4d90c16ec3201044ef7c45c4dd06135bb12bf5d043f3033d5a56b5c0125ba9010176d3bf2f344a95e38c76f46667f4a0ae70c1895858f1f07aa071f3013e57b723253b86b8385beca6e635a704b634bb10b3334e848ca075c018314e046fb0fa2f7cbb3b25c26fa8f849aaa3eed4919bbe33b287569d86012474284cd377d80a0e420a7134ad6a8c1220a04744d33783d0342334eed513a6181ead46ab168cf547697b0eb9fbb70bd7895c9654d0734a3ebe3096e5bcc95ab995bddb149cffa9ce6eb31a527e8b814f2e56ca05acf3212501f712ce444ae226f5128abc5f9541987980d8df4ad5bf7e147dee36915f462ea0685d01000001087472616e73666572a6081f8b08000000000002ffad57cd8edb3610be07c83bb00960c88002db6a5314725db4d8a0400fbd748b5e82854049d42e1b5934482a1b27c8bb67863f1225cb8ad7892fb2c8f9f93833df0cb517655b33c23ed0fda1667f94a5644aa5a996b4511593e4d3f367cf9f11f8b58a11d51e24cd2a49f7ec51c877696a170ac19bed8cd4785f97a0c9ef1b26c7abb42844db685cb63bab15f9b36d0acd4543b4201d2cfd001a0756f08ab392d03d6a198147414aa6346f28ea285249b1b7d2a295053c8c5f6bfcd0e6352f086bb43c92aa6d503bcb8f193c222b81bf8a4ba553b2b09a71bf61bd666ebffdf9a760af54dd06b5311d6d2a5688a61cef2e31de5e0ccefeef57cf0bebc64f78eade0446be4fe6af7dbad2f416ffdfc0dfdf226320ee31c7e1c996db194490e211187bae6f47d3c7282653703efbba1c97c87bce1e0d929cd6b4818c8b8ad0c6c7d9eabc7c8b5277832ac0fce36ae6f422a7d1a56869721c26c84277f2e78ee3b547b05fbed510a24c34f5f16e448292e5ed7d9a1e24b74c18d046c32a6cb6bafa65eb6d615ac0181ec186a11212aaba7ca5c52b787491281e58f18ed04a63fa5c165408271a5197ecc8efeb0f9bd893c7be263fc6617e376e79b3192c2776394992e59d077a4a388c0140ccb4c047c0ba71132127f4b3982636426cf3db493ae80763f6dd320d92f7a43d10d7989409ad8b061dd6b9ea746ba69d4c86d98740582fd0df6c3164a28a165622a097f301fd5232aa41d7be67e032b3d9e96d2eb7a42380f3181e7bce6f28f714ef27f6e73124176248aec4904c61c0acfdd570cd69cd3f62ea0c110932d1640e8d0c1147792b9baca08798ec816ff86f89a00332736f90f54016a3fa0cbb645843ffb07bae906f5d47305d03c82c2423100f5b4a86b0b478f01118f718e9cc9c6b32a7c574a9e2996ab8423d391703f23704160801b3020d28479c8350dc8c0d4fa7b043fbf418850c3303636fe750e1eb39449bf53a268b2e952727727e432ec5032fcb6d5850abd51bae40c38c112c15031e250998a739afb93e1291ffcf0aad4e7d41bb16c7cc9758576b53b0aca8071e852708e1dc9ac2519a4ad39a5c6b1f06cd9dcded6539ab50c78eafdd70c80d5aca4c3fb9c4d6448398e90e4fb498741607a585d9a9e991388efa7804a93063345ae0c08cf217b7b6d61e698d80fc607418fc4c4c5f2c43e8cec26454878086bede8433f21a8f3319b8d06ff2ad7e9359bfabd50d9837b7adfeeadc5f438032fa812bb2b7df18e1fdfdf5dab6585c348d6ff2c218dcc75df863d08c4f475d4cbe5e2a3778fb41ce98f663ebc64ef7926277f1a533ba20cd52cb8a7e1766cd98ba8e5897199c0ad62594f1511b06eb22e618956b89f314bfe7e37c2d7daef49ecc79c7dafc8f495ec19518e4a77a18558a49fd4374d10747587864b7236b12937588ef49d64e6b0f6dbe46a39bef613419194d820fbccf5f00cb97e0369d10000000000300000000000000000000000000000000000000000000000000000000000000010b4170746f735374646c696200000000000000000000000000000000000000000000000000000000000000010a4d6f76655374646c696200000000000000000000000000000000000000000000000000000000000000010e53757072614672616d65776f726b00",
        [
          "0xa11ceb0b0600000009010006020604030a16042004052413073743087a4010ba01270ce1012200000101010202050800000300010000040203000100050101000106020301000204030404060c030505000105010301080003060c0503087472616e7366657204636f696e0a73757072615f636f696e0a74776f5f62795f74776f0c766965775f62616c616e6365095375707261436f696e0762616c616e6365ec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d0000000000000000000000000000000000000000000000000000000000000001126170746f733a3a6d657461646174615f7631130000010c766965775f62616c616e63650101000001040001090a000b020a0138000b000b030b013800020101000001030b0038010200"
        ]
      ]
    }
  },
  "output": {
    "Move": {
      "gas_used": 73,
      "events": [
        {
          "guid": {
            "creation_number": "0",
            "account_address": "0x0"
          },
          "sequence_number": "0",
          "type": "0x1::code::PublishPackage",
          "data": {
            "code_address": "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d",
            "is_upgrade": true
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
            "execution_gas_units": "6",
            "io_gas_units": "68",
            "storage_fee_quants": "0",
            "storage_fee_refund_quants": "0",
            "total_charge_gas_units": "73"
          }
        }
      ],
      "vm_status": "Executed successfully"
    }
  },
  "status": "Success"
}

```

## Run a Function

Executes a specified Move function:

```powershell
supra move tool run --function-id '<ADD_YOUR_ADDRESS>::CONTRACT_NAME::FUNCTION_NAME' 
--args [ADD_ARGS] --url <RPC_URL>
```

{% hint style="info" %}
Note: The args will depend on your function.
{% endhint %}

## Run Unit Tests

Executes Move unit tests for a package:

```powershell
supra move tool test --package-dir /supra/configs/move_workspace/<PROJECT_NAME>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>]
  * \--package-dir <mark style="color:orange;"><</mark>PACKAGE\_DIR<mark style="color:orange;">></mark>: Path to a move package (the folder with a Move.toml file)

**Example:**

```powershell
supra move tool test --package-dir /supra/configs/move_workspace/move
```

**Result:**

```powershell
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
INCLUDING DEPENDENCY SupraFramework
BUILDING supra_move
Running Move unit tests
[debug] "Source wallet balance before transfer:"
[debug] 100
[debug] "Destination1 wallet balance before transfer:"
[debug] 0
[debug] "Destination2 wallet balance before transfer:"
[debug] 0
[debug] "Source wallet balance updated after transfer:"
[debug] 0
[debug] "Destination1 wallet balance updated after transfer:"
[debug] 50
[debug] "Destination2 wallet balance updated after transfer:"
[debug] 50
[ PASS    ] 0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d::transfer::test_end_to_end
Test result: OK. Total tests: 1; passed: 1; failed: 0
{
  "Result": "Success"
}
```

## Run a View Function

Executes a read-only Move function:

```powershell
supra move tool view --function-id '<ADDRESS>::CONTRACT_NAME::FUNCTION_NAME’ 
--args [ADD_ARGS] --url <RPC_URL>
```

**Additional Options:**

*   \[<mark style="color:orange;">OPTIONS</mark>]

    * \--function-id <mark style="color:orange;"><</mark>FUNCTION\_ID<mark style="color:orange;">></mark> = Function name as `<ADDRESS>::<MODULE_ID>::<FUNCTION_NAME>`
    * \--args \[<mark style="color:orange;"><</mark>ARGS<mark style="color:orange;">></mark> ...] = TypeTag arguments separated by spaces.

    Supported types \[address, bool, hex, string, u8, u16, u32, u64, u128, u256, raw]

    * \--url <mark style="color:orange;"><</mark>URL<mark style="color:orange;">></mark>: URL to a full node on the network

**Example:**

```powershell
supra move tool view --function-id 0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d::transfer::view_balance 
--args address:0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d 
--url https://rpc-testnet.supra.com
```

**Result:**

```powershell
{"result":["500000000"]}
```
