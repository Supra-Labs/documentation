# Creating a new on-chain index

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

### Supra Index Contracts

Below contracts facilitate index creation and modification on Supra L1.

<table><thead><tr><th width="174">Supra Network</th><th>Module/ Contract</th></tr></thead><tbody><tr><td>Testnet</td><td>0x5615001f63d3223f194498787647bb6f8d37b8d1e6773c00dcdd894079e56190</td></tr><tr><td>Mainnet</td><td>0xe3948c9e3a24c51c4006ef2acc44606055117d021158f320062df099c4a94150</td></tr></tbody></table>

### Creating an Index on Supra L1

The first step of the process is to decide the assets you want to be included in the index, these can be selected from Supra Data feeds catalog.

Next, you can decide on the index related variables given below:

* **Asset Weight** : This defines how each asset contributes to the calculation of index value. Currently Supra facilitates static weights only.
* **Initial Value**:= This is optional. This is the value your index would start on at the time of creation.

Now, let's move to create your first on-chain index.

#### Indices with no initial Value

The simplest form of creating an index would be without an initial value.\
In this case you need to call the following function,

{% code overflow="wrap" %}
```solidity
public entry fun create_index( owner_signer: &signer, pair_ids: vector<u32>,weights: vector<u32>) 
```
{% endcode %}

* _**pair\_ids**_ is the list of pair indexes from the data sheet provided above.
* _**weights**_ is the list of integers that denotes the corresponding weights of the pair from the list provided in the params pair\_ids.

If we are creating an index with an equally weighted set of 10 data feeds from Supra (denoted by Supra Data feed index number), it would be as follows.

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id "0xe3948c9e3a24c51c4006ef2acc44606055117d021158f320062df099c4a94150::supra_oracle_indices::create_index" --args 'u32:[0, 1, 49, 10, 14, 3, 164, 16, 15, 5]' 'u32:[10, 10, 10, 10, 10, 10, 10, 10, 10, 10]' --profile profile_name --url network_url
```
{% endcode %}

#### Indices with initial value

In this case you need to call the following function

{% code overflow="wrap" %}
```solidity
public entry fun create_index_with_init_value( owner_signer: &signer, pair_ids: vector<u32>,weights: vector<u32>,init_value: u32) 
```
{% endcode %}

* _**pair\_ids**_ is the list of pair indexes from the data sheet provided above.
* _**weights**_ is the list of integers that denotes the corresponding weights of the pair from the list provided in the params pair\_ids.
* _**init\_value**_ is the initial value of the index thats the creator wants to start with.

If we are to try the same example above with an initial value of 100, it would be as follows:.

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id "0xe3948c9e3a24c51c4006ef2acc44606055117d021158f320062df099c4a94150::supra_oracle_indices::create_index_with_init_value" --args 'u32:[0, 1, 49, 10, 14, 3, 164, 16, 15, 5]' 'u32:[10, 10, 10, 10, 10, 10, 10, 10, 10, 10]' u32:100  --profile profile_name --url network_url

```
{% endcode %}

In this case the actual weights are converted to scaled weight according to a scaling factor mentioned below.

`Scaling Factor = init_value / dot_product(init_weights,price_pair_values);`\
\
This conversion is done so that the index\_value can be adjusted with the init\_value.\
\
Upon successful execution of creating an index function you will get this kind of [receipt](https://rpc-testnet.supra.com/rpc/v1/transactions/8d18aefd2c5fff9858f4b2685743802eadde739e4fdccfbfa8b040b8a185c74c) and in the data section you can find the fields like:

```solidity
 "creator": Address Of The Creator Of This Index ,
 "id": Index Identification Number,
 "index_decimal": Index Decimals,
 "index_obj_addr": Index Object Address,
 "init_index_time": Index Initialization Time,
 "init_value": Index Initialization Value,
 "pair_ids": List Of Pairs Present In The Index,
 "scaled_weights": Scaled Weight Of The Corresponding Pairs,
 "scaling_factor": Scaling Factor For This Index,
 "weights": Actual Weights Provided By The Creator
```

NOTE :

1. All the weights and intermediary values are converted to _MAX\_INDEX\_DECIMAL_ decimals before doing any operations on them. This is done to make sure there are very low precision errors.
2. Index values have _MAX\_INDEX\_DECIMAL_ decimal places, so make sure to convert to desired decimals before doing any operations on them.

### Modifying an Index

To update the constituents pairs or or its weight or to modify the the pair list the index creator need to call this function:\\

{% code overflow="wrap" %}
```solidity
public entry fun update_index( owner_signer: &signer,  index_object: Object<Index>,  pair_ids: vector<u32>,  weights: vector<u32> )
```
{% endcode %}

* _**index\_object**_ is the address of the object in which the index details is stored.
* _**pair\_ids**_ is the list of pair indexes from the data sheet provided above.
* _**weights**_ is the list of integers that denotes the corresponding weights of the pair from the list provided in the params pair\_ids.\\

Example;

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id "0xe3948c9e3a24c51c4006ef2acc44606055117d021158f320062df099c4a94150::supra_oracle_indices::update_index" --args address: 0xccb0aaeccff18711969426eb1d422357ca95d46ffce18c376d5a7b521839c93d
'u32:[0, 1, 49, 10, 14, 3, 164, 16, 15, 5]' 'u32:[10, 15, 5, 10, 15, 5, 10, 15, 5, 10]' --profile profile_name --url network_url

```
{% endcode %}

Note:\
The Scaling Factor will remain the same and the new weights will be adjusted to the scaled weights according to the scaling factor calculated at the time of creation

### Deleting an Index

To delete an index, the index creator needs to call this function. Once deleted, the index cannot be restored back.

{% code overflow="wrap" %}
```solidity
public entry fun delete_index( owner_signer: &signer,  index_object: Object<Index> )
```
{% endcode %}

Note: index\_object is the address of the object in which the index details is stored.\
\
Example;

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id "0xe3948c9e3a24c51c4006ef2acc44606055117d021158f320062df099c4a94150::supra_oracle_indices::delete_index" --args address: 0xccb0aaeccff18711969426eb1d422357ca95d46ffce18c376d5a7b521839c93d --profile profile_name --url network_url
```
{% endcode %}
