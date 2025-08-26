# Supra Fungible Asset (FA) Module

The Supra Framework's fungible asset module provides a comprehensive standard for creating, managing, and transferring fungible tokens on the Supra. This module enables the creation of fungible assets with any metadata object that is equipped with the `Metadata` resource.&#x20;

## Module Structure

```move
module supra_framework::fungible_asset {
    use supra_framework::aggregator_v2;
    use supra_framework::create_signer;
    use supra_framework::event;
    use supra_framework::function_info;
    use supra_framework::object;
    use std::string;
    use std::features;
    use std::error;
    use std::option;
    use std::signer;
}
```

### Friend Modules

The module declares the following friend relationships:

* `supra_framework::coin` - For coin-to-FA migration support
* `supra_framework::primary_fungible_store` - For primary store management
* `supra_framework::supra_account` - For account integration
* `supra_framework::dispatchable_fungible_asset` - For custom dispatch logic

## Supply and Validation Constrants

```move
const MAX_U128: u128 = 340282366920938463463374607431768211455;
const MAX_NAME_LENGTH: u64 = 32;
const MAX_SYMBOL_LENGTH: u64 = 10;
const MAX_DECIMALS: u8 = 32;
const MAX_URI_LENGTH: u64 = 512;
```

## Core Data Structures

### FungibleAsset

```move
struct FungibleAsset {
    metadata: Object<Metadata>,
    amount: u64,
}
```

Represents a specific amount of fungible asset with type safety guarantees. This is ephemeral and cannot be stored directly - it must be deposited back into a store.

### Metadata

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct Metadata has key, copy, drop {
    name: String,           // Asset name, max 32 characters
    symbol: String,         // Asset symbol, max 10 characters  
    decimals: u8,           // Display decimals, max 32
    icon_uri: String,       // Icon URI, max 512 characters
    project_uri: String,    // Project URI, max 512 characters
}
```

### FungibleStore

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct FungibleStore has key {
    metadata: Object<Metadata>,  // The address of the base metadata object
    balance: u64,                // The balance of the fungible metadata
    frozen: bool,                // If true, only TransferRef can move in/out
}
```

### Supply Management

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct Supply has key {
    current: u128,
    maximum: Option<u128>,  // None = unlimited supply
}

#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct ConcurrentSupply has key {
    current: Aggregator<u128>,
}
```

### Reference Types

```move
struct MintRef has drop, store {
    metadata: Object<Metadata>
}

struct BurnRef has drop, store {
    metadata: Object<Metadata>
}

struct TransferRef has drop, store {
    metadata: Object<Metadata>
}

struct MutateMetadataRef has drop, store {
    metadata: Object<Metadata>
}
```

### **Dispatch Functions**

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct DispatchFunctionStore has key {
    withdraw_function: Option<FunctionInfo>,
    deposit_function: Option<FunctionInfo>,
    derived_balance_function: Option<FunctionInfo>,
}
```

### **Concurrent Balance Tracking**

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct ConcurrentFungibleBalance has key {
    balance: Aggregator<u64>,
}
```

### **Untransferable Assets**

```move
#[resource_group_member(group = supra_framework::object::ObjectGroup)]
struct Untransferable has key {}
```

## Events

```move
#[event]
struct Deposit has drop, store {
    store: address,
    amount: u64,
}

#[event]
struct Withdraw has drop, store {
    store: address,
    amount: u64,
}

#[event]
struct Frozen has drop, store {
    store: address,
    frozen: bool,
}
```

## Utility Functions

```move
inline fun default_to_concurrent_fungible_supply(): bool
```

#### Returns whether concurrent fungible supply is enabled by default.

```move
inline fun allow_upgrade_to_concurrent_fungible_balance(): bool
```

#### Returns whether upgrading to concurrent fungible balance is allowed.

```move
inline fun default_to_concurrent_fungible_balance(): bool
```

#### Returns whether concurrent fungible balance is enabled by default.

```move
#[view]
public fun store_exists(store: address): bool
```

#### Returns whether the provided address has a store initialized.

```move
inline fun store_exists_inline(store: address): bool
```

#### Inline version of store existence check for internal use.

```move
inline fun concurrent_fungible_balance_exists_inline(store: address): bool
```

#### Returns whether the provided address has concurrent fungible balance initialized.

```move
#[view]
public fun is_balance_at_least<T: key>(store: Object<T>, amount: u64): bool
```

#### Checks whether the balance of a store is >= amount.

```move
public(friend) fun is_address_balance_at_least(store_addr: address, amount: u64): bool
```

#### Friend function to check if address balance is at least the specified amount.

## Core Functions

### Asset Creation

#### **add\_fungibility**

```move
public fun add_fungibility(
    constructor_ref: &ConstructorRef,
    maximum_supply: Option<u128>,
    name: String,
    symbol: String,
    decimals: u8,
    icon_uri: String,
    project_uri: String,
): Object<Metadata>
```

Creates a fungible asset by adding metadata and supply tracking to an existing object. This returns the capabilities to mint, burn, and transfer.

**Parameters:**

* `constructor_ref` - Object constructor reference
* `maximum_supply` - Maximum supply behavior:
  * `option::none()` - Unlimited supply monitoring
  * `option::some(max)` - Fixed supply with `max` as maximum
* `name` - Asset name (≤32 characters)
* `symbol` - Asset symbol (≤10 characters)
* `decimals` - Decimal places (≤32)
* `icon_uri` - Icon URI (≤512 characters)
* `project_uri` - Project URI (≤512 characters)

{% hint style="info" %}
**Important**: The object must be non-deletable. This function initializes either `Supply` or `ConcurrentSupply`&#x20;

based on feature flags.
{% endhint %}

### Reference Generation

#### **generate\_mint\_ref**

```move
public fun generate_mint_ref(constructor_ref: &ConstructorRef): MintRef
```

Creates a mint reference that can be used to mint fungible assets. Can only be called at object creation time.

#### **generate\_burn\_ref**

```move
public fun generate_burn_ref(constructor_ref: &ConstructorRef): BurnRef
```

Creates a burn reference that can be used to burn fungible assets. Can only be called at object creation time.

#### **generate\_transfer\_ref**

```move
public fun generate_transfer_ref(constructor_ref: &ConstructorRef): TransferRef
```

Creates a transfer reference for freeze/unfreeze/transfer operations. Can only be called at object creation time.

#### **generate\_mutate\_metadata\_ref**

```move
public fun generate_mutate_metadata_ref(constructor_ref: &ConstructorRef): MutateMetadataRef
```

Creates a metadata mutation reference. Can only be called at object creation time.

### Store Management

#### **create\_store**

```move
public fun create_store<T: key>(
    constructor_ref: &ConstructorRef,
    metadata: Object<T>,
): Object<FungibleStore>
```

Creates a store for holding fungible assets of a specific type. Applications can use this to create multiple stores for isolating fungible assets for different purposes.

#### **remove\_store**

```move
public fun remove_store(delete_ref: &DeleteRef)
```

Removes an empty store. The store must be completely empty prior to removal.

### Asset Operations

#### **mint**

```move
public fun mint(ref: &MintRef, amount: u64): FungibleAsset
```

Mints the specified amount of fungible asset.

#### **mint\_internal**

```move
public(friend) fun mint_internal(metadata: Object<Metadata>, amount: u64): FungibleAsset
```

Friend function for minting - can only be called by coin.move for migration.

#### **mint\_to**

```move
public fun mint_to<T: key>(ref: &MintRef, store: Object<T>, amount: u64)
```

Mints the specified amount directly to a destination store.

#### **burn**

```move
public fun burn(ref: &BurnRef, fa: FungibleAsset)
```

Burns a fungible asset, removing it from circulation.

#### **burn\_internal**

```move
public(friend) fun burn_internal(fa: FungibleAsset): u64
```

Friend function for burning - can only be called by coin.move for migration.

#### **burn\_from**

```move
public fun burn_from<T: key>(ref: &BurnRef, store: Object<T>, amount: u64)
```

Burns the specified amount from the given store.

#### **address\_burn\_from**

```move
public(friend) fun address_burn_from(ref: &BurnRef, store_addr: address, amount: u64)
```

Friend function to burn from a specific address.

### Transfer Operations

#### **transfer**

```move
public entry fun transfer<T: key>(
    sender: &signer,
    from: Object<T>,
    to: Object<T>,
    amount: u64,
)
```

Transfers an amount of fungible asset from one store to another. The sender must own the source store.

#### **withdraw**

```move
public fun withdraw<T: key>(
    owner: &signer,
    store: Object<T>,
    amount: u64,
): FungibleAsset
```

Withdraws an amount of fungible asset from a store by the owner.

#### **deposit**

```move
public fun deposit<T: key>(store: Object<T>, fa: FungibleAsset)
```

Deposits a fungible asset into a store.

### Sanity Check Functions

#### **withdraw\_sanity\_check**

```move
public(friend) fun withdraw_sanity_check<T: key>(
    owner: &signer,
    store: Object<T>,
    abort_on_dispatch: bool,
)
```

Checks the permissions and conditions for withdraw operations, including:

* Owner verification
* Dispatch function validation
* Frozen state checking

#### **deposit\_sanity\_check**

```move
public fun deposit_sanity_check<T: key>(
    store: Object<T>,
    abort_on_dispatch: bool
)
```

Validates conditions for deposit operations, including:

* Dispatch function validation
* Frozen state checking

### Internal Operations

#### **deposit\_internal**

```move
public(friend) fun deposit_internal(store_addr: address, fa: FungibleAsset)
```

Internal function for depositing fungible assets with event emission.

#### **withdraw\_internal**

```move
public(friend) fun withdraw_internal(store_addr: address, amount: u64): FungibleAsset
```

Internal function for withdrawing fungible assets with event emission.

### Reference-Based Operations

### **withdraw\_with\_ref**

```move
public fun withdraw_with_ref<T: key>(
    ref: &TransferRef,
    store: Object<T>,
    amount: u64
): FungibleAsset
```

Withdraws fungible assets using a TransferRef, ignoring frozen status.

### **deposit\_with\_ref**

```move
public fun deposit_with_ref<T: key>(
    ref: &TransferRef,
    store: Object<T>,
    fa: FungibleAsset
)
```

Deposits fungible assets using a TransferRef, ignoring frozen status.

#### **transfer\_with\_ref**

```move
public fun transfer_with_ref<T: key>(
    transfer_ref: &TransferRef,
    from: Object<T>,
    to: Object<T>,
    amount: u64,
)
```

Transfers fungible assets with TransferRef even if stores are frozen.

### Asset Manipulation

#### **extract**

```move
public fun extract(
    fungible_asset: &mut FungibleAsset,
    amount: u64,
): FungibleAsset
```

Extracts a given amount from a fungible asset and returns a new one.

#### **merge**

```move
public fun merge(
    dst_fungible_asset: &mut FungibleAsset,
    src_fungible_asset: FungibleAsset
)
```

Merges two fungible assets. The destination asset will have the sum of both amounts.

#### **zero**

```move
public fun zero<T: key>(metadata: Object<T>): FungibleAsset
```

Creates a fungible asset with zero amount. Useful for starting computations.

#### **destroy\_zero**

```move
public fun destroy_zero(fungible_asset: FungibleAsset)
```

Destroys an empty fungible asset.

### Metadata Accessor Functions

#### **metadata\_from\_asset**

```move
public fun metadata_from_asset(fa: &FungibleAsset): Object<Metadata>
```

Returns the underlying metadata object from a fungible asset.

#### **store\_metadata**

```move
#[view]
public fun store_metadata<T: key>(store: Object<T>): Object<Metadata>
```

Returns the underlying metadata object from a store.

#### **amount**

```move
public fun amount(fa: &FungibleAsset): u64
```

Returns the amount of a given fungible asset.

#### **asset\_metadata**

```move
public fun asset_metadata(fa: &FungibleAsset): Object<Metadata>
```

Returns the metadata object from a fungible asset.

#### **mint\_ref\_metadata**

```move
public fun mint_ref_metadata(ref: &MintRef): Object<Metadata>
```

Gets the underlying metadata object from a MintRef.

#### **transfer\_ref\_metadata**

```move
public fun transfer_ref_metadata(ref: &TransferRef): Object<Metadata>
```

Gets the underlying metadata object from a TransferRef.

#### **burn\_ref\_metadata**

```move
public fun burn_ref_metadata(ref: &BurnRef): Object<Metadata>
```

Gets the underlying metadata object from a BurnRef.

#### **object\_from\_metadata\_ref**

```move
public fun object_from_metadata_ref(ref: &MutateMetadataRef): Object<Metadata>
```

Gets the underlying metadata object from a MutateMetadataRef.

### **View Functions**

```move
#[view]
public fun supply<T: key>(metadata: Object<T>): Option<u128>
```

#### Returns the current supply from the metadata object.

```move
#[view]
public fun maximum<T: key>(metadata: Object<T>): Option<u128>
```

#### Returns the maximum supply. Returns none if unlimited.

```move
#[view]
public fun name<T: key>(metadata: Object<T>): String
```

#### Returns the name of the fungible asset.

```move
#[view]
public fun symbol<T: key>(metadata: Object<T>): String
```

#### Returns the symbol of the fungible asset.

```move
#[view]
public fun decimals<T: key>(metadata: Object<T>): u8
```

#### Returns the decimals from the metadata object.

```move
#[view]
public fun icon_uri<T: key>(metadata: Object<T>): String
```

#### Returns the icon URI from the metadata object.

```move
#[view]
public fun project_uri<T: key>(metadata: Object<T>): String
```

#### Returns the project URI from the metadata object.

```move
#[view]
public fun metadata<T: key>(metadata: Object<T>): Metadata
```

#### Returns the complete metadata struct from the metadata object.

```move
#[view]
public fun balance<T: key>(store: Object<T>): u64
```

#### Returns the balance of a given store.

```move
#[view]
public fun is_frozen<T: key>(store: Object<T>): bool
```

#### Returns whether a store is frozen. Defaults to false if store doesn't exist.

## Migration Support

Supra provides migration support from the legacy coin standard through friend functions:

```move
// Friend functions for coin migration
public(friend) fun mint_internal(metadata: Object<Metadata>, amount: u64): FungibleAsset
public(friend) fun burn_internal(fa: FungibleAsset): u64
```

{% hint style="danger" %}
These functions are restricted to the `supra_framework::coin` module for seamless migration.
{% endhint %}

## Testing Support

The module includes comprehensive test functions for development:

```move
#[test_only]
public fun create_test_token(creator: &signer): (ConstructorRef, Object<TestToken>)

#[test_only]
public fun init_test_metadata(constructor_ref: &ConstructorRef): (MintRef, TransferRef, BurnRef, MutateMetadataRef)

#[test_only]
public fun create_fungible_asset(creator: &signer): (MintRef, TransferRef, BurnRef, MutateMetadataRef, Object<Metadata>)

#[test_only]
public fun create_test_store<T: key>(owner: &signer, metadata: Object<T>): Object<FungibleStore>
```

