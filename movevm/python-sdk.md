# Python SDK

### Introduction

The `supra-python-sdk` provides a seamless interface for interacting with the Supra-L1 network. It offers comprehensive support for Move VM operations and transactions, enabling developers to both build on-chain and submit MoveVM-based transactions with ease.

### Key Features

* **Complete Move VM Support** - Full compatibility with Move VM operations
* **On-chain Data Queries** - Efficient methods to query blockchain state
* **Transaction Management** - Submit and manage transactions seamlessly
* **Cryptographic Operations** - Built-in support for ED25519 and multi-signature operations
* **Token Operations** - Native support for token creation and management
* **BCS Serialization** - Efficient Binary Canonical Serialization support

For detailed API documentation of all modules, see the [Official Python SDK Reference](https://supra-python-sdk.docs.supra.com/index.html) and for some more reference examples, you can check [official examples repo here.](https://github.com/Entropy-Foundation/supra-python-sdk/tree/master/examples)

***

### Installation

Install via pip:

```bash
python -m pip install supra-sdk
```

Verify installation:

```python
import supra_sdk
print(supra_sdk.__version__)
```

***

### Quick Start

#### Initialize the Client

```python
import asyncio
from supra_sdk.account import Account
from supra_sdk.clients.rest import SupraClient

async def main():
    # Initialize client with RPC endpoint
    client = SupraClient("https://rpc-testnet.supra.com")
    
    # Create a new account
    account = Account.generate()
    print(f"Address: {account.address()}")
    print(f"Private Key: {account.private_key.hex()}")
    
    # Close client when done
    await client.close()

if __name__ == "__main__":
    asyncio.run(main())
```

#### Request Testnet Tokens

```python
async def get_testnet_tokens():
    client = SupraClient("https://rpc-testnet.supra.com")
    account = Account.generate()
    
    # Request tokens from faucet
    tx_hash = await client.faucet(account.address())
    
    if tx_hash:
        print(f"Faucet successful! Tx: {tx_hash}")
        # Optionally wait for confirmation
        await client.wait_for_faucet(tx_hash)
    
    await client.close()
```

#### Submit Your First Transaction

```python
from supra_sdk.transactions import EntryFunction, TransactionArgument, TransactionPayload
from supra_sdk.type_tag import TypeTag, StructTag
from supra_sdk.bcs import Serializer
from supra_sdk.account import Account
from supra_sdk.clients.rest import SupraClient
from supra_sdk.account_address import AccountAddress

async def transfer_coins():
    client = SupraClient("https://rpc-testnet.supra.com")
    sender = Account.generate()
    recipient = Account.generate()
    
    # Fund sender account
    await client.faucet(sender.address())
    
    # Create transfer payload
    payload = EntryFunction.natural(
        "0x1::coin",
        "transfer",
        [TypeTag(StructTag.from_str("0x1::supra_coin::SupraCoin"))],
        [
            TransactionArgument(recipient.address(), Serializer.struct),
            TransactionArgument(100_000, Serializer.u64),
        ],
    )
    
    # Create and submit signed transaction
    signed_txn = await client.create_signed_transaction(
        sender, 
        TransactionPayload(payload)
    )
    
    result = await client.submit_signed_transaction(signed_txn)
    print(f"Transaction Hash: {result}")
    
    await client.close()
```

***

### API Reference - SupraClient

`SupraClient` is the main interface for interacting with the Supra blockchain.

#### Constructor

```python
SupraClient(base_url: str, client_config: Optional[ClientConfig] = None)
```

**Parameters:**

* `base_url`: RPC endpoint URL
* `client_config`: Optional configuration for the client

**Example:**

```python
client = SupraClient("https://rpc-testnet.supra.com")
```

#### Core Methods

**`faucet(address: AccountAddress, wait_for_faucet: bool = True) -> Optional[str]`**

Request testnet tokens from the faucet.

**Parameters:**

* `address`: Recipient address
* `wait_for_faucet`: Whether to wait for transaction confirmation (default: True)

**Returns:** Transaction hash or None if failed

**Example:**

```python
tx_hash = await client.faucet(account.address())

# Or without waiting
tx_hash = await client.faucet(account.address(), wait_for_faucet=False)
if tx_hash:
    await client.wait_for_faucet(tx_hash)
```

**`wait_for_faucet(tx_hash: str) -> None`**

Wait for faucet transaction to complete.

**Parameters:**

* `tx_hash`: Faucet transaction hash

**`create_signed_transaction(sender: Account, payload: TransactionPayload) -> SignedTransaction`**

Create a signed transaction ready for submission.

**Parameters:**

* `sender`: Account signing the transaction
* `payload`: Transaction payload (EntryFunction, Script, etc.)

**Returns:** Signed transaction object

**Example:**

```python
payload = EntryFunction.natural(...)
signed_txn = await client.create_signed_transaction(account, TransactionPayload(payload))
```

**`submit_signed_transaction(signed_transaction: SignedTransaction) -> str`**

Submit a signed transaction to the blockchain.

**Parameters:**

* `signed_transaction`: Signed transaction object

**Returns:** Transaction hash

***

### API Reference - Account

`Account` class manages cryptographic keys and signing operations.

#### Creating Accounts

```python
from supra_sdk.account import Account

# Generate new random account
account = Account.generate()

# Load from private key hex string
account = Account.load_key("0x1234...private_key_hex")

# Save account to file
account.store("./my_account.json")

# Load account from file
account = Account.load("./my_account.json")
```

***

### API Reference - Token Clients

#### SupraTokenClient

High-level client for fungible token operations.

**Constructor**

```python
from supra_sdk.clients import SupraTokenClient

token_client = SupraTokenClient(supra_client)
```

**Key Methods**

**`create_collection(account: Account, name: str, description: str, uri: str) -> str`**

Create a new token collection.

**Example:**

```python
await token_client.create_collection(
    account,
    "My Collection",
    "Collection description",
    "https://example.com/collection"
)
```

**`create_token(account: Account, collection_name: str, name: str, description: str, supply: int, uri: str, royalty_points_numerator: int) -> str`**

Mint a new token in a collection.

**Example:**

```python
await token_client.create_token(
    account,
    "My Collection",
    "Token #1",
    "First token",
    supply=1,
    uri="https://example.com/token/1",
    royalty_points_numerator=5  # 5% royalty
)
```

**`offer_token(sender: Account, receiver: AccountAddress, creator: AccountAddress, collection_name: str, token_name: str, property_version: int, amount: int) -> str`**

Offer a token to another account.

**`claim_token(receiver: Account, sender: AccountAddress, creator: AccountAddress, collection_name: str, token_name: str, property_version: int) -> str`**

Claim an offered token.

**`get_token_balance(owner: AccountAddress, creator: AccountAddress, collection_name: str, token_name: str, property_version: int) -> int`**

Get token balance for an account.

**`get_collection(creator: AccountAddress, collection_name: str) -> dict`**

Get collection data.

**`get_token_data(creator: AccountAddress, collection_name: str, token_name: str) -> dict`**

Get token metadata.

***

### API Reference - Transactions

#### Creating Transaction Payloads

```python
from supra_sdk.transactions import (
    EntryFunction,
    TransactionArgument,
    TransactionPayload
)
from supra_sdk.type_tag import TypeTag, StructTag
from supra_sdk.bcs import Serializer

# Create an entry function payload
payload = EntryFunction.natural(
    "0x1::coin",  # Module
    "transfer",   # Function
    [TypeTag(StructTag.from_str("0x1::supra_coin::SupraCoin"))],  # Type args
    [
        TransactionArgument(recipient_address, Serializer.struct),
        TransactionArgument(amount, Serializer.u64),
    ]  # Arguments
)

# Wrap in TransactionPayload
tx_payload = TransactionPayload(payload)
```

#### EntryFunction

**`natural(module: str, function: str, ty_args: list[TypeTag], args: list[TransactionArgument]) -> EntryFunction` (static)**

Create an entry function with natural argument encoding.

**Parameters:**

* `module`: Module identifier (e.g., "0x1::coin")
* `function`: Function name
* `ty_args`: Type arguments
* `args`: Function arguments as TransactionArgument objects

#### TransactionArgument

```python
TransactionArgument(value: Any, serializer: Callable)
```

Wraps an argument with its serializer function.

**Common serializers from `Serializer`:**

* `Serializer.u8`, `Serializer.u64`, `Serializer.u128`, `Serializer.u256`
* `Serializer.bool`
* `Serializer.struct` (for addresses and complex types)
* `Serializer.str` (for strings)

***

### API Reference - Type System

#### TypeTag & StructTag

```python
from supra_sdk.type_tag import TypeTag, StructTag

# Parse struct type from string
coin_type = StructTag.from_str("0x1::supra_coin::SupraCoin")

# Use in type arguments
type_arg = TypeTag(coin_type)

# Complex nested types
token_type = StructTag.from_str(
    "0x3::token::Token<0x1::supra_coin::SupraCoin>"
)
```

#### StructTag

**`from_str(type_tag: str) -> StructTag` (static)**

Parse a struct type from string representation.

**Example:**

```python
# Simple type
simple = StructTag.from_str("0x1::coin::Coin")

# Generic type with type parameters
generic = StructTag.from_str("0x1::coin::Coin<0x1::supra_coin::SupraCoin>")
```

***

### API Reference - BCS Serialization

Binary Canonical Serialization (BCS) is the format used by Move VM.

#### Serializer

```python
from supra_sdk.bcs import Serializer

serializer = Serializer()
serializer.u64(12345)
serializer.str("Hello")
serializer.bool(True)

# Get serialized bytes
data = serializer.output()
```

#### Deserializer

```python
from supra_sdk.bcs import Deserializer

deserializer = Deserializer(data)
number = deserializer.u64()
text = deserializer.str()
flag = deserializer.bool()
```

#### Common Serialization Methods

* `u8(value: int)`, `u16(value: int)`, `u32(value: int)`, `u64(value: int)`
* `u128(value: int)`, `u256(value: int)`
* `bool(value: bool)`
* `str(value: str)`
* `struct(value: Serializable)`
* `sequence(values: list, encoder: Callable)`

***

### NFT Collection Example:

This example demonstrates how to create and manage an NFT collection using the Supra Python SDK.

{% hint style="success" %}
Create a common.py file in the same directory as the below file to import RPC\_NODE\_URL.
{% endhint %}

```python
import asyncio
import json
from examples.common import RPC_NODE_URL
from supra_sdk.account import Account
from supra_sdk.clients import SupraTokenClient
from supra_sdk.clients.rest import SupraClient


async def main():
    supra_client = SupraClient(RPC_NODE_URL)
    token_client = SupraTokenClient(supra_client)

    alice = Account.generate()
    bob = Account.generate()

    collection_name = "Alice's"
    token_name = "Alice's first token"
    property_version = 0

    print(f"Alice account address: {alice.address()}")
    print(f"Bob account address: {bob.address()}")

    await supra_client.faucet(alice.address())
    await supra_client.faucet(bob.address())

    print("\n=== Creating Collection and Token ===")
    await token_client.create_collection(
        alice, collection_name, "Alice's simple collection", "https://supra.com"
    )

    await token_client.create_token(
        alice,
        collection_name,
        token_name,
        "Alice's simple token",
        1,
        "https://supra.dev/img/temp.jpeg",
        0,
    )

    collection_data = await token_client.get_collection(
        alice.address(), collection_name
    )
    print(
        f"Alice's collection: {json.dumps(collection_data, indent=4, sort_keys=True)}"
    )
    balance = await token_client.get_token_balance(
        alice.address(), alice.address(), collection_name, token_name, property_version
    )
    print(f"Alice's token balance: {balance}")
    token_data = await token_client.get_token_data(
        alice.address(), collection_name, token_name
    )
    print(f"Alice's token data: {json.dumps(token_data, indent=4, sort_keys=True)}")

    print("\n=== Transferring the token to Bob ===")
    await token_client.offer_token(
        alice,
        bob.address(),
        alice.address(),
        collection_name,
        token_name,
        property_version,
        1,
    )
    await token_client.claim_token(
        bob,
        alice.address(),
        alice.address(),
        collection_name,
        token_name,
        property_version,
    )

    alice_balance = token_client.get_token_balance(
        alice.address(), alice.address(), collection_name, token_name, property_version
    )
    bob_balance = token_client.get_token_balance(
        bob.address(), alice.address(), collection_name, token_name, property_version
    )
    [alice_balance, bob_balance] = await asyncio.gather(*[alice_balance, bob_balance])
    print(f"Alice's token balance: {alice_balance}")
    print(f"Bob's token balance: {bob_balance}")

    print("\n=== Transferring the token back to Alice using MultiAgent ===")
    await token_client.direct_transfer_token(
        bob, alice, alice.address(), collection_name, token_name, 0, 1
    )
    alice_balance = token_client.get_token_balance(
        alice.address(), alice.address(), collection_name, token_name, property_version
    )
    bob_balance = token_client.get_token_balance(
        bob.address(), alice.address(), collection_name, token_name, property_version
    )
    [alice_balance, bob_balance] = await asyncio.gather(*[alice_balance, bob_balance])
    print(f"Alice's token balance: {alice_balance}")
    print(f"Bob's token balance: {bob_balance}")

    await supra_client.close()


if __name__ == "__main__":
    asyncio.run(main())
```

***
