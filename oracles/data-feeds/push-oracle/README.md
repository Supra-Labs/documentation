# Push Oracle

## Quick Start: Supra Price Feeds

Supra's Push oracle publishes price pairs on-chain regularly and at high frequencies, enabling consumers to get near real-time prices for their smart contracts. The Push oracle functions via partnerships Supra has with respective chains to share, ensuring that frequent feed updates are available for these destination chains without interruption. Both the Push model and Pull model (on demand) have the same number of data pairs offered by Supra.

Integrating with Supra price feeds is quick and easy. The price feed value published by Supra is known as an **S-Value** (aka Supra Value). In the following sections, we will demonstrate how to retrieve an **S-Value** using Supra's Push model.

Please refer to the below resources for a better understanding of our price feeds.

* [Data Feeds](../) - This explains how Supra calculates the **S-value** for an asset.
* [Data Feeds Index](../data-feeds-index/) - This provides a list of data pairs currently offered by Supra.
* [Available Networks](networks.md) - This is a list of available networks and Supra contract addresses.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

\
Let's begin!

{% tabs %}
{% tab title="Supra L1 Move" %}
**Step 1: Create the S-value interface**

Import the interface from the DORA Interface ([https://github.com/Entropy-Foundation/dora-interface](https://github.com/Entropy-Foundation/dora-interface)) Git repository, and add the subdirectory Testnet you to use for integration.

**Step 2: Configure the S-value feed dependency**

Create your project and add the below dependencies in your Move.toml

Testnet

{% code overflow="wrap" %}
```solidity
[dependencies]
core = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "supra/testnet/core", rev = "master" }
```
{% endcode %}

Mainnet

{% code overflow="wrap" %}
```solidity
[dependencies]
core = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "supra/mainnet/core", rev = "master" }
```
{% endcode %}

**Step 3: Request S-values for data feeds**

Now you can easily request S-values of any supported data pairs.

* Import : `supra_oracle::supra_oracle_storage`

```rust
use aptos_std::table::{Self, Table};
use supra_oracle::supra_oracle_storage;
```

* Add the following structs

```rust
struct PriceConfiguration has key {
        feeds: Table<u32, PriceStrategy>,
    }

    struct PriceStrategy has store, copy, drop {
        high: u128,
        low: u128,
        start_time: u64,
        end_time: u64,
    }
```

_**PriceConfiguration**_ is the main resource structure for this module. It has a \`feeds\` map that stores the price strategy feeds.\
\
\&#xNAN;_**PriceStrategy**_ represents a strategy feed entry with high, low, start time, and end time.

* Next, add the \`init\_module function below:

{% code overflow="wrap" %}
```rust
fun init_module(owner: &signer) {
        move_to(owner, PriceConfiguration{ feeds:table::new<u32, PriceStrategy>()});
    }
```
{% endcode %}

This initializes the PriceConfiguration with an empty feeds vector map. Then, it shares this resource.

* Add the `set_high_low` function. This function sets the high and low price of a specific pair in the PriceConfiguration. If the pair already exists, it updates the existing PriceStrategy; if not, it inserts a new PriceStrategy.

{% code overflow="wrap" %}
```rust
public entry fun set_high_low(supra_pair_id: u32) acquires PriceConfiguration {
        assert!(supra_oracle_storage::does_pair_exist(supra_pair_id), 0);
        let (current_price, _, _, current_round) = supra_oracle_storage::get_price(supra_pair_id);
        let price_configuration = borrow_global_mut<PriceConfiguration>(@supra_client);
        let price_strategy = table::borrow_mut_with_default(
            &mut price_configuration.feeds,
            supra_pair_id,
            PriceStrategy { high: 0, low: 0, start_time: 0, end_time: 0 }
        );

        if ((price_strategy.high + price_strategy.low) == 0) {
            price_strategy.high = current_price;
            price_strategy.low = current_price;
            price_strategy.start_time = current_round;
            price_strategy.end_time = current_round;
        }
        else {
            if (current_price > price_strategy.high) {
                price_strategy.high = current_price;
            }
            else if (current_price < price_strategy.low) {
                price_strategy.low = current_price;
            };
            price_strategy.end_time = current_round;
        }
    }

```
{% endcode %}

* Add the following `check_price_strategy` function to retrieve Strategic Price of one pair between an interval. This public view function fetches the strategic price for a single pair thats been updated by the above function `set_high_low` .

{% code overflow="wrap" %}
```rust
#[view]
    public fun check_price_strategy(supra_pair_id:u32):PriceStrategy acquires PriceConfiguration {
        *table::borrow(& borrow_global<PriceConfiguration>(@supra_client).feeds,supra_pair_id)
    }
```
{% endcode %}

* To retrieve a S-value for multiple pairs you can design a public view function `get_pair_price_sum` that fetches prices for multiple pairs from the oracle, adds them and returns the sum.

{% code overflow="wrap" %}
```rust
#[view]
    public fun get_pair_price_sum(pairs: vector<u32>):u256 {
        let prices:vector<Price> = supra_oracle_storage::get_prices(pairs);
        let sum:u256 = 0;

        vector::for_each_reverse(prices,|price| {
            let (_,value,_,_,_) = supra_oracle_storage::extract_price(&price);
            sum = sum + (value as u256);
        });
        return sum
    }

```
{% endcode %}

**Derived Pairs - Convert a data pair to any currency with ease**

There have been many requests to provide functionality convert the prices to USD. While we take data directly from exchange quoted pairs mostly in stable coins (USDT/USDC), conversion at our end adds more latency and extra layer of margin for error. (\*The oracle services who provide USD prices are already converting the USDT prices at their backend with previously mentioned disadvantages of that method). Still, for those who are in need we provide that as a custom function to convert and derive any pair using operators of _**Multiplication(parameter=0)**_ or _**Division(Parameter=1)**_.

1.  Import supra\_oracle::supra\_oracle\_storage dependency:

    ```rust
    use supra_oracle::supra_oracle_storage ;
    ```
2.  Next, add the \`init\_module function below:

    ```rust
    fun init_module(owner_signer: &signer) {
       move_to(owner_signer, EmitDerivePair { derived_pair: account::new_event_handle<DerivedPair>(owner_signer) });
    }
    ```

Add the following _`get_update_derived_price`_ function to retrieve S-value for the derived pair. This public entry function fetches the price for a derived pair from the _**supra\_oracle\_storage**_ module and emits an event for the Derived Pair Feed.

```rust
public entry fun get_derived_pair_price(
  _signer: &signer,
  pair_id1: u32,
  pair_id2: u32,
  operation: u8
) acquires EmitDerivePair {
    let (value, decimal, round_difference, round_compare) = supra_oracle_storage::get_derived_price(pair_id1, pair_id2, operation);

  let event_handler = borrow_global_mut<EmitDerivePair>(@client_example);
  let derived_pair = DerivedPair { pair_id1, pair_id2, operation, value, decimal, round_difference, round_compare };
  event::emit_event<DerivedPair>(&mut event_handler.derived_pair, derived_pair);
}
```

From the above code:

* **pair**: a pair index number from which you want to retrieve the price value
* **pairs**: Index numbers of multiple pairs from which you want to retrieve the price value

Tada! You now have a method in your Smart Contract that you can call at any time to retrieve the price of the selected pair.

\\
{% endtab %}

{% tab title="Solidity" %}
**Step 1: Create The S-Value Interface**

In the first step, you need to build data structures to receive the data feeds and functions required to fetch data into configured data structures. You may add the following code to the Solidity smart contract that you wish to retrieve the **S-Value**.

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">pragma solidity 0.8.19;

// depending on the requirement, you may build one or more data structures given below. 

<strong>interface ISupraSValueFeed {
</strong>
// Data structure to hold the pair data
struct priceFeed {
    uint256 round;
<strong>    uint256 decimals;
</strong>    uint256 time;
    uint256 price;
    }


// Data structure to hold the derived/connverted data pairs.  This depends on your requirements.

struct derivedData{
    int256 roundDifference;
    uint256 derivedPrice;
    uint256 decimals;
}


// Below functions enable you to retrieve different flavours of S-Value
// Term "pair ID" and "Pair index" both refer to the same, pair index mentioned in our data pairs list.

// Function to retrieve the data for a single data pair
function getSvalue(uint256 _pairIndex)
    external 
    view
    returns (priceFeed memory);



//Function to fetch the data for a multiple data pairs
function getSvalues(uint256[] memory _pairIndexes)
    external
    view
    returns (priceFeed[] memory);


// Function to convert and derive new data pairs using two pair IDs and a mathematical operator multiplication(*) or division(/).
//** Curreently only available in testnets
function getDerivedSvalue(uint256 pair_id_1,uint256 pair_id_2,
    uint256 operation)
    external
    view
    returns (derivedData memory);



// Function to check  the latest Timestamp on which a data pair is updated. This will help you check the staleness of a data pair before performing an action. 
function getTimestamp(uint256 _tradingPair) 
external
view
returns (uint256);

}
</code></pre>

The above code creates the interface that you will later apply in order to fetch a price from SupraOracles.

**Step 2: Configure The S-Value Feed Address**

Next, in order to retrieve the S-Value, configure the S-Value feed using the Supra Smart Contract address as demonstrated below.\
The Supra contract for each network can be found in our [network addresses](https://github.com/nolan-supra/docs-test/blob/oracles/data-feeds/push-oracle/broken-reference/README.md) list, and the address used in the example below needs to be replaced.

{% code overflow="wrap" %}
```solidity
contract ISupraSValueFeedExample {
    ISupraSValueFeed internal sValueFeed;
    constructor() {
        sValueFeed = ISupraSValueFeed(0xE92D276bBE234869Ecc9b85101F423c6bD26654A);
    }
} 
// used above is a sample address and you should use the correct address of your preffered network 
```
{% endcode %}

**Step 3: Get The S-Value Crypto Price**

Now you can access the S-Values for any of the [trading pairs](../data-feeds-index/) Supra publishes. THe below sample code retrieves S-value for single as well as multiple data pairs for demonstration purposes. You may use it as appropriate.

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">// requesting s-value for a single pair
function getPrice(uint256 _priceIndex)
    external
    view 
    returns (ISupraSValueFeed.priceFeed memory) {
    return sValueFeed.getSvalue(_priceIndex);
}

// requesting s-values for multiple pairs
function getPriceForMultiplePair(uint256[] memory _pairIndexes) 
    external 
    view 
    returns (ISupraSValueFeed.priceFeed[] memory) {
    return sValueFeed.getSvalues(_pairIndexes);
}

// Function to convert and derive a new data pair using two existing pair ids, and a mathematical operator division(1), or multiplication(0).
function getDerivedValueOfPair (uint256 pair_id_1,uint256 pair_id_2,uint256 operation)
<strong>    external
</strong>    view
    returns(ISupraSValueFeed.derivedData memory){
    return sValueFeed.getDerivedSvalue(pair_id_1,pair_id_2,operation);
}

</code></pre>

\
**Recommended Best Practices:**\
\
**Create a function with access control that updates the sValueFeed using the function \_updateSupraSvalueFeed()**\_**.**

This will allow you to update the address of the Supra storage contract after deployment to future-proof your contract. Access control is mandatory to prevent the undesired modification of the address.

```solidity
function updateSupraSvalueFeed(ISupraSValueFeed _newSValueFeed) 
external 
onlyOwner {
sValueFeed = _newSValueFeed;
}
```

**Example Implementation:**

Here's an example of what your implementation should look like:

```solidity
pragma solidity 0.8.19;
import "./ISupraSValueFeed.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract ConsumerContract is Ownable {

ISupraSValueFeed internal sValueFeed;

constructor(ISupraSValueFeed _sValueFeed) {
sValueFeed=_sValueFeed; 
}


function updateSupraSvalueFeed(ISupraSValueFeed _newSValueFeed) 
external 
onlyOwner {
sValueFeed = _newSValueFeed;
}


function getSupraSvalueFeed() external view returns(ISupraSValueFeed){
return sValueFeed;
}


function getPrice(uint256 _priceIndex) 
external
view 
returns (ISupraSValueFeed.priceFeed memory) {
return sValueFeed.getSvalue(_priceIndex);
}


function getPriceForMultiplePair(uint256[] memory _pairIndexes) 
external
view
returns (ISupraSValueFeed.priceFeed[] memory) {
return sValueFeed.getSvalues(_pairIndexes);
}

function getDerivedValueOfPair(uint256 pair_id_1,uint256 pair_id_2,uint256 operation)
external
view 
returns(ISupraSValueFeed.derivedData memory){
return sValueFeed.getDerivedSvalue(pair_id_1,pair_id_2,operation);
}
}
```
{% endtab %}

{% tab title="Sui Move" %}
**Step 1: Create the S-value interface**

Import the interface from the DORA Interface ([https://github.com/Entropy-Foundation/dora-interface](https://github.com/Entropy-Foundation/dora-interface)) Git repository, and add the subdirectory Mainnet or Testnet you would like to use for integration.

**Step 2: Configure the S-value feed dependency**

Create your project and add the below dependencies in your Move.toml

Testnet

{% code overflow="wrap" %}
```solidity
[dependencies]
SupraOracle = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "sui/testnet/supra_holder", rev = "51c1875d86f20abe2a22e8d2ae7a3a2a2a56d5a1" }
```
{% endcode %}

Mainnet

{% code overflow="wrap" %}
```solidity
[dependencies]
SupraOracle = { git = "https://github.com/Entropy-Foundation/dora-interface", subdir = "sui/mainnet/supra_holder", rev = "51c1875d86f20abe2a22e8d2ae7a3a2a2a56d5a1" }
```
{% endcode %}

**Step 3: Request S-values for data feeds**

Now you can easily request S-values of any supported data pairs.

* Import Sui dependency in: `client_example/sources/push_client.move`

```rust
use SupraOracle::SupraSValueFeed::{Self, OracleHolder};
```

* Add the following structs to: `client_example/sources/push_client.move`

```rust
struct ExampleHolder has key, store {
    id: UID,
    feeds: VecMap<u32, ExampleEntry>,
}
struct ExampleEntry has store, copy, drop {
    value: u128,
    decimal: u16,
    timestamp: u128,
    round: u64,
}  
```

ExampleHolder is the main resource struct for this module. It has a unique identifier \`id\` and a \`feeds\` map that stores the price feeds.

ExampleEntry represents a price feed entry with a value, decimal, timestamp, and round.

* Next, add the \`init function below:

```rust
fun init(ctx: &mut TxContext) {
    let resource = ExampleHolder {
        id: object::new(ctx),
        feeds: vec_map::empty<u32, ExampleEntry>(),
    };
    transfer::share_object(resource);
}
```

This initializes the ExampleHolder with a new ID and an empty feeds vector map. Then, it shares this resource.

* Add the \`update\_price\` function. This Updates the price of a specific pair in the ExampleHolder. If the pair already exists, it updates the existing ExampleEntry; if not, it inserts a new ExampleEntry.

{% code overflow="wrap" %}
```rust
fun update_price(resource: &mut ExampleHolder, pair: u32, value: u128, decimal: u16, timestamp: u128, round: u64) {
    let feeds = resource.feeds;
    if (vec_map::contains(&feeds, &pair)) {
        let feed = vec_map::get_mut(&mut resource.feeds, &pair);
        feed.value = value;
        feed.decimal = decimal;
        feed.timestamp = timestamp;
        feed.round = round;
    } else {
        let entry = ExampleEntry { value, decimal, timestamp, round };
        vec_map::insert(&mut resource.feeds, pair, entry);
    };
}
```
{% endcode %}

* Add the following \`get\_update\_price\` function to retrieve S-value for one pair. This is a public entry function that fetches the price for a single pair from the oracle and updates the corresponding entry in the ExampleHolder.

{% code overflow="wrap" %}
```rust
public entry fun get_update_price(
    oracle_holder: &OracleHolder,
    resource: &mut ExampleHolder,
    pair: u32,
    _ctx: &mut TxContext
) {
    let (value, decimal, price, round) = SupraSValueFeed::get_price(oracle_holder, pair);
    update_price(resource, pair, value, decimal, price, round);
}
```
{% endcode %}

* If you want to retrieve a S-value for multiple pairs you need to add \`get\_update\_prices\` which is a public entry function that fetches prices for multiple pairs from the oracle and updates the corresponding entries in the ExampleHolder.

<pre class="language-rust" data-overflow="wrap"><code class="lang-rust">public entry fun get_update_prices(
oracle_holder: &#x26;OracleHolder,
resource: &#x26;mut ExampleHolder,
pairs: vector&#x3C;u32>,
_ctx: &#x26;mut TxContext
    ) {
        let prices = SupraSValueFeed::get_prices(oracle_holder, pairs);
        let n = vector::length(&#x26;prices);
        let i = 0;

        while (i &#x3C; n) {
            let price = vector::borrow(&#x26;prices, i);
            let (pair, value, decimal, timestamp, round) = svalue_feed_holder::extract_price(price);
            update_price(resource, pair, value, decimal, timestamp, round);
            i = i + 1;
           }
<strong>    }    
</strong>

</code></pre>

The typical usage of this module starts with initializing an ExampleHolder using the init function. The ExampleHolder is then updated with price information using the update\_price function, which can be manually called or called through get\_update\_prices or get\_update\_price, which also fetch the price data from the oracle.

Please note that you need an instance of OracleHolder to use get\_update\_prices and get\_update\_price. These are public entry functions that can be called from external modules or scripts.

**Derived Pairs - Convert a data pair to any currency with ease**

There have been many requests to provide functionality convert the prices to USD. While we take data directly from exchange quoted pairs mostly in stable coins (USDT/USDC), conversion at our end adds more latency and extra layer of margin for error. (\*The oracle services who provide USD prices are already converting the USDT prices at their backend with previously mentioned disadvantages of that method). Still, for those who are in need we provide that as a custom function to convert and derive any pair using operators of _**Multiplication(parameter=0)**_ or _**Division(Parameter=1)**_.

1.  Import supra\_holder::derived\_pair dependency in:\
    `client_example/sources/derived_pair_client.move`

    ```rust
    use supra_holder::svalue_feed_holder;
    ```
2. Add the following \`get\_update\_derived\_price\` function to retrieve S-value for derived pair. This is a public entry function that fetches the price for a single derived pair from the oracle and updates the corresponding entry in the ExampleHolder.

{% code overflow="wrap" %}
```solidity
public entry fun get_derived_pair_price(
  pair_id1: u32,
  pair_id2: u32,
  operation: u8
   _ctx: &mut TxContext
) {
    let (value, decimal, round_difference, round_compare) = SupraSValueFeed::get_derived_price(oracle_holder, pair_id1, pair_id2, operation);
  event::emit(DerivedPair { pair_id1, pair_id2, operation, value, decimal, round_difference, round_compare });
}
```
{% endcode %}

\\

Note: You might need to use `--skip-dependency-verification` flag to publish your smart contract

From the above code:

* `OracleHolder`: a resource that contains oracle price
* `pair`: a pair number which you want to retrieve the price value

Tada! You now have a method in your smart contract that you can call at any time to retrieve the prices of selected asset pairs.
{% endtab %}
{% endtabs %}
