# Read on-chain Indices

Reading index values is permissionless in Supra L1. Any user or dApp can calculate and read the latest values of any index available in Supra. While calculations would require gas, reading is free.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

### Step 1: Create The Supra Indices Framework.

Import interface from [https://github.com/Entropy-Foundation/dora-interface](https://github.com/Entropy-Foundation/dora-interface) git repository and add subdirectory mainnet or testnet whatever you would like to use for integration.

### Step 2: Configure The S-Value Feed Dependency

Create your project and add the below dependencies in your Move.toml

Testnet

{% code overflow="wrap" %}
```solidity
[dependencies]
core = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "supra/testnet/core", rev = "master" }
```
{% endcode %}

### Step 3: Example to access and use the S-Value Crypto Price

#### **3.1 Supported Indices**

Now you can simply access the Index-Value of our supported indices.

* Import supra\_oracle::supra\_oracle\_indices

{% code overflow="wrap" %}
```solidity
use aptos_std::table::{Self, Table};
use supra_oracle::supra_oracle_indices;
```
{% endcode %}

* Add the following structs\
  \&#xNAN;_IndicesList_ is the main resource structure for this module. It has a \`list\` map that lists object addresses of all indices.

{% code overflow="wrap" %}
```solidity
struct IndicesList has key {
        list: Table<u64, address>,
    }

```
{% endcode %}

* Next, add the \`init\_module function below:\
  This initializes the IndicesList with an empty list map.

{% code overflow="wrap" %}
```solidity
fun init_module(owner: &signer) {
        move_to(owner, IndicesList{ feeds:table::new<u64, address>()});
    }
```
{% endcode %}

* Add the `store_index_object_address` function. This function stores the index object address that corresponds to the index id.

{% code overflow="wrap" %}
```solidity
public entry fun store_index_object_address(supra_index_id: u64) acquires IndicesList {
        let indices_list = borrow_global_mut<IndicesList>(@supra_client);
        let index_object_address = get_index_address(supra_index_id);
        table::add(&mut indices_list.list,supra_index_id,index_object_address);

    }

```
{% endcode %}

### Calculating the latest value of the index

To calculate and retrieve the value of multiple indices on Supra L1 , the client smart contract module can trigger following function:

{% code overflow="wrap" %}
```solidity
public fun calculate_index_value( index_objects: vector<Object<Index>> ): vector<u256>
```
{% endcode %}

* **index\_objects** : List of index object addresses for which you need to calculate the index values.
* _**Return**_: Vector of Index Values corresponding to the index\_objects

\
NOTE: When this function is triggered the storage is going to get updated\* with the latest calculated index values.

_\*if the most recent timestamp from the constituent pair timestamps is greater than the current index timestamp._

### Reading the index value

#### Method 1 : Without considering the timestamp of the last index update

Returns the Index details with the value that's present in the storage:

{% code overflow="wrap" %}
```solidity
public fun get_index_details(index_object: Object<Index>): Index
```
{% endcode %}

* _**index\_object**_ : Index object address for which you need to fetch the index details

{% code overflow="wrap" %}
```solidity
supra move tool view --function-id "0x1c9decaaa7c8aa1d5cbb7dd009764dfe145891d35586956bdb4ddb888d03a1b3::supra_oracle_indices::get_index_details" --args address: 0xccb0aaeccff18711969426eb1d422357ca95d46ffce18c376d5a7b521839c93d --profile profile_name --url network_url

```
{% endcode %}

* _**Return**_: Vector of Index details for corresponding to the index\_object

{% code overflow="wrap" %}
```solidity
struct Index has key, copy, drop {
   // Index Identification Number
   id: u64,
   // List Of Pairs Present In The Index
   pair_ids: vector<u32>,
   // Index Initialization Value
   init_value: u256,
   // Index Initialization Time
   init_index_time: u64,
   // Last Updated Time Of The Index
   last_update_time: u64,
   // Scaling Factor For This Index
   scaling_factor: u256,
   // Scaled Weight Of The Corresponding Pairs
   scaled_weights: vector<u256>,
   // Index Decimals
   index_decimal: u16,
   // Current Index Value
   index_value: u256
}
```
{% endcode %}

#### Method 2 : Fetch the index value that can only sustain/tolerate a specific stateless period

Check if the index last calculated time falls into the staleness duration then it will simply return the value from the storage else It recalculates the index value, and if the index still falls into the staleness duration it returns either true or false:

```solidity
public fun get_indices_with_staleness_tolerance( index_objects: vector<Object<Index>>,
staleness_tolerances: vector<u64>): (vector<u256>, vector<bool>)
```

Example:

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id "0x1c9decaaa7c8aa1d5cbb7dd009764dfe145891d35586956bdb4ddb888d03a1b3::supra_oracle_indices::get_index_details" --args address: 0xccb0aaeccff18711969426eb1d422357ca95d46ffce18c376d5a7b521839c93d --profile profile_name --url network_url

```
{% endcode %}

* _**Return**_: Vector of Index Values corresponding to the index\_objects, Vector of bool that tells whether the index calculated time falls under the statelessness tolerance or not corresponding to the index\_objects

\
NOTE:\
When this function is triggered the storage is going to get updated\* with the latest calculated index values.\
\&#xNAN;_\*if the most recent timestamp from the constituent pair timestamps is greater than the current index timestamp._
