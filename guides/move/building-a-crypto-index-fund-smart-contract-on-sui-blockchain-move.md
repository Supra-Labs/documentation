---
description: >-
  A Comprehensive Guide to Utilizing Supra's Oracle Price Feed Integration with
  Move
---

# Building a Crypto Index Fund Smart Contract on Sui Blockchain // Move

Welcome to our comprehensive guide on building a crypto index fund smart contract using Supra's price feed Oracle with the Move programming language on the Sui Blockchain. In this guide, we will walk you through the process of creating an index fund that tracks the value of a basket of crypto assets with equal weighting.

We’ll start by demonstrating how to integrate Supra's Oracle with your dApp to retrieve real-time prices for the crypto assets and calculate investment proportions. By the end of this tutorial, you will have a solid understanding of how to incorporate Supra's Oracle into your own projects.

Let's dive in!

Click [here](https://www.youtube.com/watch?v=x1UEw60ZrQI\&t=7s) for a video tutorial of this guide.

## Step 1: Setting up Files and Directories

Before we delve into the details, it's essential to establish the proper groundwork. The first step in this process is to organize and structure your project files and directories.

```
crypto_index_fund/
│
├── sources/
│   └── index_fund.move
│
└── move.toml
```

In the _crypto\_index\_fund/_ directory:

* The _sources/_ directory contains the _index\_fund.move_ file, which houses our index fund smart contract implementation.
* The _move.toml_ file is located in the root of the _crypto\_index\_fund/_ directory. This file plays a vital role in managing project dependencies and addresses.

With the project structure properly set up, we can now proceed to the next step: configuring the _move.toml_ file. This step is crucial as it allows you to manage dependencies and addresses for your index fund smart contract. Let's move forward and set up the _move.toml_ file accordingly.

## Step 2: Setting Up the _move.toml_ File

Now that we have our project structure in place, let's move on to the next step: configuring the _move.toml_ file. This file plays a crucial role in managing dependencies and addresses for our index fund smart contract.

Here is the content of the _move.toml_ file:

```
[package]
name = "crypto_index_fund"
version = "0.0.1"

[dependencies]
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "devnet-v1.2.0" }
SupraOracle = { local = "../supra-svalue-feed-framework" }

[addresses]
crypto_index_fund = "0x0"
sui =  "0x2"
```

Let's break down the different sections of the _move.toml_ file:

* _\[package]_: This section defines the package information, including the name and version of our project.
* _\[dependencies]_: Here, we specify the dependencies required for our project. In this case, we have two dependencies: _Sui_ and _SupraOracle_. The Sui dependency is sourced from the Sui Blockchain's standard library. We are linking to the MystenLabs repository on GitHub, specifically the sui-framework package from the devnet-v1.2.0 revision. The SupraOracle dependency is a local package that houses the SValueFeed framework. Notice that we're referencing it locally, which means you'll need to have it on your machine in the relative path specified ("../supra-svalue-feed-framework"). You can refer to our documentation for guidance on implementing this.
* _\[addresses]_: This section allows us to define addresses associated with our smart contract. In our case, we have set the address for _crypto\_index\_fund_ to _0x0_, the typical address for main modules in Move, and the address for _sui,_ which is typically set to _0x2_.

By properly configuring the _move.toml_ file, we ensure that our project has the necessary dependencies and addresses set up correctly.

In the next step, we will start diving into the implementation details of our smart contract by exploring the _index\_fund.move_ file located in the _sources/_ directory. Let's proceed with the exciting part of writing our smart contract code!

## Step 3: Importing Required Modules

Our code begins with the necessary imports that allow us to utilize various functions and structures within the Sui blockchain.

```rust
module crypto_index_fund::index_fund {
  use sui::object::{Self, UID};
  use sui::transfer;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, Coin};
  use sui::balance::{Self, Balance};
  use sui::sui::SUI;
  use sui::vec_map::{Self, VecMap};
  use sui::table::{Self, Table};
  use std::vector;
  use SupraOracle::SupraSValueFeed::{get_price, get_prices, extract_price, OracleHolder, Price};
}
```

These imports provide access to features such as object manipulation, asset transfers, transaction context, coin handling, balance management, data structures, and Supra's Oracle functions for price data retrieval. With these imports, we have a solid foundation to begin implementing our index fund smart contract on the Sui Blockchain.

## Step 4: Defining Constant Variables

```rust
  const BTC_INDEX: u32 = 0;
  const ETH_INDEX: u32 = 1;
  const XRP_INDEX: u32 = 14;
  const ADA_INDEX: u32 = 16;
  const MATIC_INDEX: u32 = 20;
```

In this step, we define the indices of the crypto assets that we will query the oracle for. The indices provided are for popular assets like Bitcoin (BTC), Ethereum (ETH), XRP, Cardano (ADA), and Polygon (MATIC). However, you are free to customize these indices to include your preferred assets, such as Layer-1 protocols, gaming tokens, DeFi tokens, and more. You can check out all of our index pairs in our documentation. By adjusting these indices, you can tailor the index fund to match your investment preferences.

## Step 5: Defining the Objects

In this step, we define two essential structs for our index fund: _IndexFundToken_ and _IndexFund._

### IndexFundToken

```rust
 struct IndexFundToken has key, store {
    id: UID, 
    crypto_assets: Table<u32, u128>, 
  }
```

The _IndexFundToken_ struct represents a token that tracks the value of a basket of crypto assets with equal weighting. It consists of two fields: _id_ of type UID, which uniquely identifies the token, and _crypto\_assets_ of type Table\<u32, u128>, which stores the amounts of respective assets held by the token. The _u32_ values in the table represent the asset indices, while the _u128_ values represent the corresponding asset amounts.

### IndexFund

```rust
  struct IndexFund has key, store {
    id: UID, 
    balance: Balance<SUI>,
    pairs: vector<u32>,
  }
```

The _IndexFund_ struct manages the balance and asset pairs for the index fund. It has three fields: _id_ of type UID, which uniquely identifies the fund, _balance_ of type Balance\<SUI>, which stores the total amount of SUI deposited into the fund, and _pairs_ of type vector\<u32>, which represents the crypto assets that the fund will invest in. You can customize the asset pairs based on your preferences.

These structs lay the foundation for managing the index fund and tracking the values of different assets within it. Let's move on to the next step, where we'll initialize the fund and set up its initial state.

## Step 6: Initializing the Index Fund

In this step, we define the _init_ function, which runs once when the package is published. Its purpose is to set up the initial state of the index fund.

```rust
fun init(ctx: &mut TxContext) {
    // vector of crypto pairs to query the oracle for
    let pairs: vector<u32> = vector[BTC_INDEX, ETH_INDEX, XRP_INDEX, ADA_INDEX, MATIC_INDEX];

    // initialize the fund balance to 0
    let index_fund = IndexFund {
      id: object::new(ctx),
      balance: balance::zero(),
      pairs,
    };
    transfer::share_object(index_fund);
  }
```

Inside the _init_ function, we create a vector called pairs to store the indices of the crypto pairs that we want to query the oracle for.

Next, we initialize the fund balance to 0 by creating an IndexFund instance named _index\_fund_. This instance has three fields: _id_, which is assigned the value of _object::new(ctx)_ to generate a unique identifier, _balance_, which is set to _balance::zero()_ to initialize it as zero, and _pairs_, which is assigned the pairs vector we defined earlier.

Finally, we use the _transfer::share\_object_ function to share the _index\_fund_ object, making it accessible for other functions to interact with.

By running the _init_ function, we set up the initial state of the index fund, including the crypto pairs to query the oracle for and initializing the fund balance to 0. In the next step, we'll explore the function for depositing an investment into the index fund.

## Step 7: Depositing an Investment

In this step, we'll explore the _deposit\_investment_ function, which allows users to deposit SUI and mint a new _IndexFundToken_.

```rust
  public entry fun deposit_investment(oracle_holder: &OracleHolder, index_fund: &mut IndexFund, deposit_amount: Coin<SUI>, ctx: &mut TxContext) {

    // convert coin into balance 
    let deposit_balance_sui: Balance<SUI> = coin::into_balance(deposit_amount);
    // get deposit_amount in sui and then convert to u128 for math operations
    let deposit_amount_sui: u128 = (balance::value(&deposit_balance_sui) as u128);

    // add deposit balance to fund balance
    balance::join(&mut index_fund.balance, deposit_balance_sui);

    // query oracle for SUI_USD price 
    let (sui_usd_price,_,_,_) = get_price(oracle_holder, 90);

    let adjusted_sui_usd_price: u128 = convert_to_9_decimal_places(sui_usd_price);

    // calculate deposit amount in USD 
    let deposit_amount_usd: u128 = adjusted_sui_usd_price * deposit_amount_sui; 

    // divide by 5 to get investment amount per crypto as it is equally weighted
    let investment_amount_per_crypto: u128 = deposit_amount_usd / 5;

    // query oracle for all 5 crypto assets in fund 
    let price_holder: VecMap<u32, u128> = get_crypto_prices(oracle_holder, index_fund.pairs);
    
    // calculate investment proportions for each and then divide by the price of each crypto in USD
    let btc: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &BTC_INDEX);
    let eth: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &ETH_INDEX);
    let xrp: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &XRP_INDEX);
    let ada: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &ADA_INDEX);
    let matic: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &MATIC_INDEX);

    let crypto_assets: Table<u32, u128> = table::new<u32, u128>(ctx);

    table::add(&mut crypto_assets, BTC_INDEX, btc);
    table::add(&mut crypto_assets, ETH_INDEX, eth);
    table::add(&mut crypto_assets, XRP_INDEX, xrp);
    table::add(&mut crypto_assets, ADA_INDEX, ada);
    table::add(&mut crypto_assets, MATIC_INDEX, matic);

    // mint NFT and send to user
    let index_token = IndexFundToken {
      id: object::new(ctx),
      crypto_assets,
    };

    transfer::public_transfer(index_token, tx_context::sender(ctx));

  }
```

The function takes several parameters: _oracle\_holder_, _index\_fund_, _deposit\_amount_, and _ctx_.

First, we convert the _deposit\_amount_ from a coin into a balance by using _coin::into\_balance_, assigning it to _deposit\_balance\_sui_. Then, we convert the _deposit\_amount\_sui_ from the balance into a _u128_ value for mathematical operations.

Next, we add the _deposit\_balance\_sui_ to the fund balance by using _balance::join_ and passing in the mutable reference to _index\_fund.balance_.

We query the oracle for the SUI/USD price by calling _get\_price_ with _oracle\_holder_ and _90_ as parameters. We store the result in _sui\_usd\_price_ and adjust it to 9 decimal places by calling our helper function _convert\_to\_9\_decimal\_places_ and assigning the result to _adjusted\_sui\_usd\_price_.

To calculate the deposit amount in USD, we multiply _adjusted\_sui\_usd\_price_ by _deposit\_amount\_sui_, storing the result in _deposit\_amount\_usd_.

Next, we divide _deposit\_amount\_usd_ by 5 to determine the investment amount per crypto asset, as they are equally weighted. The result is stored in _investment\_amount\_per\_crypto_.

We query the oracle for all five crypto assets in the fund by calling _get\_crypto\_prices_ with _oracle\_holder_ and _index\_fund.pairs_ as parameters. The result is stored in _price\_holder_, a VecMap that maps crypto asset indices to their respective prices.

We calculate the investment proportions for each crypto asset by dividing _investment\_amount\_per\_crypto_ by the price of each crypto asset in USD, retrieved from _price\_holder_. The results are stored in _btc_, _eth_, _xrp_, _ada_, and _matic_.

To represent the crypto assets and their respective amounts, we create a _crypto\_assets_ table using _table::new\<u32, u128>(ctx)_. We then add the crypto assets and their amounts to the table using _table::add_.

Finally, we mint a new _IndexFundToken_ by creating an instance of the IndexFundToken struct. We assign a new object identifier to _id_ using _object::new(ctx)_, and set _crypto\_assets_ to the crypto\_assets table we created. The minted token is transferred to the sender using _transfer::public\_transfer_.

By calling the _deposit\_investment_ function, users can deposit SUI and mint a new _IndexFundToken_ that represents their investment in the index fund. In the next step, we'll explore the function for withdrawing an investment and burning the corresponding _IndexFundToken_.

## Step 8: Withdrawing an Investment

In this step, we'll explore the _withdraw\_investment_ function, which allows users to withdraw their investment and burn the corresponding _IndexFundToken_.

```rust
  public entry fun withdraw_investment(oracle_holder: &OracleHolder, index_fund: &mut IndexFund, index_token: IndexFundToken, ctx: &mut TxContext) {
  
    // query oracle for all 5 cryptos in fund
    let price_holder: VecMap<u32, u128> = get_crypto_prices(oracle_holder, index_fund.pairs);
    
    // calculate the total USD value of the IndexFundToken

    let btc_usd_value: u128 = *table::borrow(&index_token.crypto_assets, BTC_INDEX) * *vec_map::get(&price_holder, &BTC_INDEX);
    let eth_usd_value: u128 = *table::borrow(&index_token.crypto_assets, ETH_INDEX) * *vec_map::get(&price_holder, &ETH_INDEX);
    let xrp_usd_value: u128 = *table::borrow(&index_token.crypto_assets, XRP_INDEX) * *vec_map::get(&price_holder, &XRP_INDEX);
    let ada_usd_value: u128 = *table::borrow(&index_token.crypto_assets, ADA_INDEX) * *vec_map::get(&price_holder, &ADA_INDEX);
    let matic_usd_value: u128 = *table::borrow(&index_token.crypto_assets, MATIC_INDEX) * *vec_map::get(&price_holder, &MATIC_INDEX);

    let total_usd_value: u128 = btc_usd_value + eth_usd_value + xrp_usd_value + ada_usd_value + matic_usd_value;

    // query oracle for SUI_USD price 
    let (sui_usd_price,_,_,_) = get_price(oracle_holder, 90);

    let adjusted_sui_usd_price: u128 = convert_to_9_decimal_places(sui_usd_price);

    // convert total USD value to SUI 
    let total_sui: u64 = ((total_usd_value / adjusted_sui_usd_price) as u64); 

    // subtract the withdrawal amount from the fund balance
    let index_token_balance: Balance<SUI> = balance::split(&mut index_fund.balance, total_sui);

    // convert balance into coin for transfer
    let total_coin_sui: Coin<SUI> = coin::from_balance(index_token_balance, ctx);

    // transfer the total value in SUI back to the sender
    transfer::public_transfer(total_coin_sui, tx_context::sender(ctx));

    // burn the IndexFundToken
    let IndexFundToken { id,  crypto_assets } = index_token;
    table::drop(crypto_assets);
    object::delete(id);
  }
```

The function takes several parameters: _oracle\_holder_, _index\_fund_, _index\_token_, and _ctx_.

First, we query the oracle for all five crypto assets in the fund by calling _get\_crypto\_prices_ with _oracle\_holder_ and _index\_fund.pairs_ as parameters. The result is stored in _price\_holder_, a VecMap that maps crypto asset indices to their respective prices.

We calculate the total USD value of the _IndexFundToken_ by multiplying the amount of each crypto asset in the _index\_token.crypto\_assets_ table with their respective prices retrieved from _price\_holder_. The results are stored in _btc\_usd\_value_, _eth\_usd\_value_, _xrp\_usd\_value_, _ada\_usd\_value_, and _matic\_usd\_value_. We then sum these values to obtain the _total\_usd\_value_.

Next, we query the oracle for the SUI/USD price by calling _get\_price_ with _oracle\_holder_ and _90_ as parameters. We store the result in _sui\_usd\_price_ and adjust it to 9 decimal places by calling _convert\_to\_9\_decimal\_places_ and assigning the result to _adjusted\_sui\_usd\_price_.

To convert the total USD value to SUI, we divide _total\_usd\_value_ by _adjusted\_sui\_usd\_price_ and cast the result as a _u64_ to obtain the _total\_sui_.

We subtract the _total\_sui_ from the fund balance by using _balance::split_ and passing in the mutable reference to _index\_fund.balance_. The resulting balance is assigned to _index\_token\_balance_, which represents the withdrawn amount.

To transfer the withdrawn value back to the sender, we convert _index\_token\_balance_ into a Coin\<SUI> using _coin::from\_balance_ and _ctx_, and assign it to _total\_coin\_sui_. Then, we use _transfer::public\_transfer_ to transfer _total\_coin\_sui_ to the sender.

Lastly, we burn the _IndexFundToken_ by destructuring _index\_token_ into _id_ and _crypto\_assets_. We drop the _crypto\_assets_ table using _table::drop_ and delete the _id_ object using _object::delete_.

By calling the _withdraw\_investment_ function, users can withdraw their investment, receive the corresponding SUI value, and burn the _IndexFundToken_. In the next step, we'll discuss a helper function that retrieves the prices of multiple crypto assets from the oracle.

## Step 9: Retrieving Multiple Crypto Prices from the Oracle

In this step, we introduce a helper function called _get\_crypto\_prices_ that retrieves the prices of multiple crypto assets from the oracle.

```rust
fun get_crypto_prices(oracle_holder: &OracleHolder, pairs: vector<u32>): VecMap<u32, u128> {

    let crypto_prices: vector<Price> = get_prices(oracle_holder, pairs);

    let price_holder: VecMap<u32, u128> = vec_map::empty<u32, u128>(); 

    let length: u64 = vector::length(&crypto_prices);
    let idx: u64 = 0;

    while (idx < length) {
      let price = vector::borrow(&crypto_prices, idx);
      let (pair, value, _decimal, _timestamp, _round) = extract_price(price);
      let adjusted_value: u128 = convert_to_9_decimal_places(value);
      vec_map::insert(&mut price_holder, pair, adjusted_value);
      idx = idx + 1;
    };
    price_holder
}
```

The function takes two parameters: _oracle\_holder_, which is the oracle instance, and _pairs_, a vector of _u32_ values representing the indices of the crypto assets to query.

Inside the function, we call _get\_prices_ with _oracle\_holder_ and _pairs_ as arguments, which returns a vector of _Price_ objects representing the prices of the specified crypto assets. We assign this vector to _crypto\_prices_.

We initialize an empty VecMap\<u32, u128> called _price\_holder_, which will store the crypto asset indices mapped to their adjusted prices.

Next, we determine the length of _crypto\_prices_ using _vector::length_ and assign it to length. We also initialize an index variable, _idx_, to keep track of the current iteration.

We enter a while loop that iterates as long as _idx_ is less than _length_. Within each iteration, we retrieve the _Price_ object at the current index using _vector::borrow_ and assign it to _price_.

Using destructuring, we extract the _pair_ (crypto asset index), _value_ (price value), and other unused variables from price.

To ensure consistency, we call the _convert\_to\_9\_decimal\_places_ helper function to adjust the value to 9 decimal places and assign the result to _adjusted\_value_.

Finally, we use _vec\_map::insert_ to insert the pair and _adjusted\_value_ into the _price\_holder_ map, mapping the crypto asset index to its adjusted price.

The _get\_crypto\_prices_ function returns the _price\_holder_ map, which contains the adjusted prices of the requested crypto assets.

By utilizing the _get\_crypto\_prices_ helper function, you can easily retrieve the prices of multiple crypto assets from the Oracle and incorporate them into your index fund calculations.

## Step 10: Conclusion

Congratulations! You've reached the end of our step-by-step tutorial on creating a crypto index fund smart contract using Supra's price feed Oracle with the Move programming language on the Sui blockchain. Throughout this guide, we've covered essential concepts such as setting up the project structure, importing required modules, initializing the index fund, depositing investments, withdrawing investments, and retrieving crypto prices from the oracle.

By following these steps, you now have a solid foundation for building your own index fund smart contracts and integrating Supra's Oracle for accurate price data. The Move programming language provides a secure and efficient environment for developing blockchain applications, and Supra's Oracle offers reliable and up-to-date price feed services.

Remember, this tutorial serves as a starting point, and you can customize and expand upon the provided code to suit your specific needs. Explore additional functionalities, add error handling, implement security measures, and consider integrating other modules to enhance your smart contract.

Happy coding and building on the Sui Blockchain with Supra's Oracle!

Here is the full code:

```rust
module crypto_index_fund::index_fund {

  use sui::object::{Self, UID};
  use sui::transfer;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, Coin};
  use sui::balance::{Self, Balance};
  use sui::sui::SUI;
  use sui::vec_map::{Self, VecMap};
  use sui::table::{Self, Table};
  use std::vector;
  use SupraOracle::SupraSValueFeed::{get_price, get_prices, extract_price, OracleHolder, Price};


  // 0, 1, 14, 16, 20 - Indices of crypto assets to query the oracle for. Feel free to customize with your preferred assets, such as layer 1 protocols, gaming tokens, DeFi tokens, etc.

  const BTC_INDEX: u32 = 0;
  const ETH_INDEX: u32 = 1;
  const XRP_INDEX: u32 = 14;
  const ADA_INDEX: u32 = 16;
  const MATIC_INDEX: u32 = 20;


  // Struct to represent an IndexFundToken that tracks the value of a basket of crypto assets with equal weighting. Each u32 value is the index of the asset and each u128 value represents the amount of that respective asset held by the IndexFundToken.

  struct IndexFundToken has key, store {
    id: UID, 
    crypto_assets: Table<u32, u128>, 
  }

  // Struct to represent an IndexFund that manages the balance and asset pairs for the index fund. The balance is the total amount of SUI deposited into the fund. The pairs are the crypto assets that the fund will invest in - which can be customized to your liking.

  struct IndexFund has key, store {
    id: UID, 
    balance: Balance<SUI>,
    pairs: vector<u32>,
  }

  // The init function runs once when the package is published. It sets up the initial state of the index fund, including the crypto pairs to query the oracle for and initializes the fund balance to 0.

  fun init(ctx: &mut TxContext) {
    // vector of crypto pairs to query the oracle for
    let pairs: vector<u32> = vector[BTC_INDEX, ETH_INDEX, XRP_INDEX, ADA_INDEX, MATIC_INDEX];
    // initialize the fund balance to 0
    let index_fund = IndexFund {
      id: object::new(ctx),
      balance: balance::zero(),
      pairs,
    };
    transfer::share_object(index_fund);
  }

  // Function that deposits SUI and mints a new IndexFundToken 
  public entry fun deposit_investment(oracle_holder: &OracleHolder, index_fund: &mut IndexFund, deposit_amount: Coin<SUI>, ctx: &mut TxContext) {

    // convert coin into balance 
    let deposit_balance_sui: Balance<SUI> = coin::into_balance(deposit_amount);
    // get deposit_amount in sui and then convert to u128 for math operations
    let deposit_amount_sui: u128 = (balance::value(&deposit_balance_sui) as u128);

    // add deposit balance to fund balance
    balance::join(&mut index_fund.balance, deposit_balance_sui);

    // query oracle for SUI_USD price 
    let (sui_usd_price,_,_,_) = get_price(oracle_holder, 90);

    let adjusted_sui_usd_price: u128 = convert_to_9_decimal_places(sui_usd_price);

    // calculate deposit amount in USD 
    let deposit_amount_usd: u128 = adjusted_sui_usd_price * deposit_amount_sui; 

    // divide by 5 to get investment amount per crypto as it is equally weighted
    let investment_amount_per_crypto: u128 = deposit_amount_usd / 5;

    // query oracle for all 5 crypto assets in fund 
    let price_holder: VecMap<u32, u128> = get_crypto_prices(oracle_holder, index_fund.pairs);
    
    // calculate investment proportions for each and then divide by the price of each crypto in USD
    let btc: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &BTC_INDEX);
    let eth: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &ETH_INDEX);
    let xrp: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &XRP_INDEX);
    let ada: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &ADA_INDEX);
    let matic: u128 = investment_amount_per_crypto / *vec_map::get(&price_holder, &MATIC_INDEX);

    let crypto_assets: Table<u32, u128> = table::new<u32, u128>(ctx);

    table::add(&mut crypto_assets, BTC_INDEX, btc);
    table::add(&mut crypto_assets, ETH_INDEX, eth);
    table::add(&mut crypto_assets, XRP_INDEX, xrp);
    table::add(&mut crypto_assets, ADA_INDEX, ada);
    table::add(&mut crypto_assets, MATIC_INDEX, matic);

    // mint NFT and send to user
    let index_token = IndexFundToken {
      id: object::new(ctx),
      crypto_assets,
    };

    transfer::public_transfer(index_token, tx_context::sender(ctx));

  }

  // Function to withdraw the user's investment and burn the IndexFundToken
  public entry fun withdraw_investment(oracle_holder: &OracleHolder, index_fund: &mut IndexFund, index_token: IndexFundToken, ctx: &mut TxContext) {
  
    // query oracle for all 5 cryptos in fund
    let price_holder: VecMap<u32, u128> = get_crypto_prices(oracle_holder, index_fund.pairs);
    
    // calculate the total USD value of the IndexFundToken

    let btc_usd_value: u128 = *table::borrow(&index_token.crypto_assets, BTC_INDEX) * *vec_map::get(&price_holder, &BTC_INDEX);
    let eth_usd_value: u128 = *table::borrow(&index_token.crypto_assets, ETH_INDEX) * *vec_map::get(&price_holder, &ETH_INDEX);
    let xrp_usd_value: u128 = *table::borrow(&index_token.crypto_assets, XRP_INDEX) * *vec_map::get(&price_holder, &XRP_INDEX);
    let ada_usd_value: u128 = *table::borrow(&index_token.crypto_assets, ADA_INDEX) * *vec_map::get(&price_holder, &ADA_INDEX);
    let matic_usd_value: u128 = *table::borrow(&index_token.crypto_assets, MATIC_INDEX) * *vec_map::get(&price_holder, &MATIC_INDEX);

    let total_usd_value: u128 = btc_usd_value + eth_usd_value + xrp_usd_value + ada_usd_value + matic_usd_value;

    // query oracle for SUI_USD price 
    let (sui_usd_price,_,_,_) = get_price(oracle_holder, 90);

    let adjusted_sui_usd_price: u128 = convert_to_9_decimal_places(sui_usd_price);

    // convert total USD value to SUI - add 9 decimal places
    let total_sui: u64 = ((total_usd_value / adjusted_sui_usd_price) as u64); 

    // subtract the withdrawal amount from the fund balance
    let index_token_balance: Balance<SUI> = balance::split(&mut index_fund.balance, total_sui);

    // convert balance into coin for transfer
    let total_coin_sui: Coin<SUI> = coin::from_balance(index_token_balance, ctx);

    // transfer the total value in SUI back to the sender
    transfer::public_transfer(total_coin_sui, tx_context::sender(ctx));

    // burn the IndexFundToken
    let IndexFundToken { id,  crypto_assets } = index_token;
    table::drop(crypto_assets);
    object::delete(id);
  }


// helper function to get multiple crypto prices from the oracle
fun get_crypto_prices(oracle_holder: &OracleHolder, pairs: vector<u32>): VecMap<u32, u128> {

    let crypto_prices: vector<Price> = get_prices(oracle_holder, pairs);

    let price_holder: VecMap<u32, u128> = vec_map::empty<u32, u128>(); 

    let length: u64 = vector::length(&crypto_prices);
    let idx: u64 = 0;

    while (idx < length) {
      let price = vector::borrow(&crypto_prices, idx);
      let (pair, value, _decimal, _timestamp, _round) = extract_price(price);
      let adjusted_value: u128 = convert_to_9_decimal_places(value);
      vec_map::insert(&mut price_holder, pair, adjusted_value);
      idx = idx + 1;
    };
    price_holder
}

// Helper function to convert a u128 value from 18 decimal places to 9 decimal places
fun convert_to_9_decimal_places(value: u128): u128 {
  value / 1000000000
}
}

```

\\

\\

\\

\
\\
