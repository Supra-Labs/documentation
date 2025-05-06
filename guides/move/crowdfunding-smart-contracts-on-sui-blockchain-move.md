---
description: A Simple Guide to Supra’s Oracle Price Feed Integration with Move
---

# Crowdfunding Smart Contracts on Sui Blockchain // Move

Welcome to our step-by-step tutorial on creating a crowdfunding smart contract using Supra’s price feed Oracle with the Move programming language on the Sui blockchain. This guide will demonstrate the simplicity of integrating Supra's Oracle into any smart contract, and by the end of this tutorial, you'll be fully equipped to incorporate Supra’s price feed Oracle into your own projects. So, let's jump right in!

Click [here](https://www.youtube.com/watch?v=weYGZyZdmmU\&t=1s) for a video tutorial of this guide.

## Step 1: Setting up Files and Directories

Before we dive into the intricacies of our crowdfunding smart contract, it's essential to lay the groundwork correctly. The first step in this process is to set up your files and directories.

```
crowd_fund/
│
├── sources/
│   └── fund_contract.move
│
└── move.toml
```

In this structure:

* _crowd\_fund/_ is the root directory of our project.
* The _sources/_ directory within _crowd\_fund/_ houses your Move source files. In our case, it contains _fund\_contract.move_ - our crowdfunding smart contract.
* The _move.toml_ file is located in the root of our _crowd\_fund/_ directory. This file is essential for managing your project's dependencies and addresses.

Now that you've correctly set up your project structure, we're ready to proceed to the next step - configuring your _move.toml_ file. This is an integral step as it enables you to manage dependencies and addresses for your crowdfunding smart contract. Let's go ahead and get that set up now!

## Step 2: Setting Up the _move.toml_ File

Before we start writing any code, it's essential to set up our environment correctly. One of the crucial steps in this process is configuring the _move.toml_ file. The _move.toml_ file is like a backstage manager for your project—it organizes and directs different parts of your code so that everything runs smoothly. Let's take a look at what goes into it:

```
[package]
name = "crowd_fund"
version = "0.0.1"

[dependencies]
Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "devnet-v1.2.0" }
SupraOracle = { local = "../supra-svalue-feed-framework" }

[addresses]
crowd_fund =  "0x0"
sui =  "0x2"
```

The _\[package]_ section is where we declare the package name (in our case, "_crowd\_fund_") and the version.

In the _\[dependencies]_ section, we define the external packages our project relies on. For our crowdfunding contract, we need to pull in two dependencies:

* _Sui:_ This is the Sui blockchain's standard library. We are linking to the MystenLabs repository on GitHub, specifically the sui-framework package from the devnet-v1.2.0 revision.
* _SupraOracle:_ This is the SupraOracles SValueFeed framework. Notice that we're referencing it locally, which means you'll need to have it on your machine in the relative path specified ("../supra-svalue-feed-framework"). You can refer to our documentation for guidance on implementing this.

Finally, the _\[addresses]_ section is where we define addresses for modules in our package. This is essential for deploying your contract to the Sui Blockchain. Here we have two addresses:

* _crowd\_fund:_ The address where our crowdfunding contract will reside. It's set to _0x0_, the typical address for main modules in Move.
* _sui:_ This address corresponds to the Sui blockchain's standard library. It's typically set to _0x2._

Now that we have our _move.toml_ file set up, we're ready to dive into the exciting world of crowdfunding smart contracts on the Sui blockchain. Up next, we'll delve into the code of our contract to understand how it all works.

## Step 3: Importing Required Modules

Our code begins with the necessary imports that allow us to utilize various functions and structures within the Sui blockchain.

```rust
module crowd_fund::fund_contract {
  use sui::object::{Self, UID, ID};
  use sui::transfer;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, Coin};
  use sui::balance::{Self, Balance};
  use sui::sui::SUI;
  use sui::event;
  use SupraOracle::SupraSValueFeed::{get_price, OracleHolder};
}
```

The modules we import include essential Sui modules for handling objects, transactions, balances, coins, and events, as well as the price feed Oracle for handling data feeds.

## Step 4: Defining Errors

Having set up the project structure and dependencies, let's now turn our attention to the code itself. The first block of our code establishes a crucial error constant.

```rust
const ENotFundOwner: u64 = 0;
```

This constant `ENotFundOwner` signifies an error state when a user who is not the fund owner tries to perform an operation that only the fund owner should be able to perform (like withdrawing funds). The error is represented by a `u64` integer, which in this case, is set to 0.

Remember, it's good practice to define your error constants at the beginning of your smart contract. This makes it easier to maintain and understand your code.

## Step 5: Defining the Objects and Events

In this section, we define the objects and events that our smart contract will use. Let's break it down:

### The Fund Object

```rust
struct Fund has key {
    id: UID,
    target: u64, // in USD 
    raised: Balance<SUI>,
  }
```

This is the central data structure of our contract. A Fund struct has three fields:

* `id:` a UID (unique identifier) that uniquely identifies a crowdfunding campaign.
* `target:` the fundraising target in USD.
* `raised:` the amount of money that has been raised so far, expressed as a Balance of SUI tokens.

### The Receipt Object

```rust
struct Receipt has key {
    id: UID, 
    amount_donated: u64, // in MIST, 10^-9 of a SUI. One billion MIST equals one SUI. 
}
```

This struct represents a receipt that gets generated whenever a donation is made. It has two fields:

* `id:` a UID that uniquely identifies a receipt.
* `amount_donated:` the amount of SUI tokens that were donated.

### The FundOwnerCap Object

```rust
struct FundOwnerCap has key { 
    id: UID,
    fund_id: ID, 
}
```

This struct represents a special capability granted to the fund creator, which allows them to withdraw funds from the specific crowdfunding campaign that they created. It has two fields:

* `id:` a UID that uniquely identifies this capability.
* `fund_id:` an ID that identifies the specific crowdfunding campaign that this capability pertains to.

### The TargetReached Event

```rust
struct TargetReached has copy, drop {
    raised_amount_sui: u128,
}
```

This event is emitted when a crowdfunding campaign has reached its target. It has a single field, `raised_amount_sui`, which indicates the total amount of funds raised (in SUI tokens) at the time the target was reached.

By defining these objects and events, our smart contract has the building blocks it needs to operate. Now, let's move on to our functions and how they interact with these building blocks.

## Step 6: Defining The Functions

In this section, we define the functions that perform operations in our smart contract. These functions are responsible for creating crowdfunding campaigns, making donations, and withdrawing funds.

### The `create_fund` function

This function is the starting point of our crowdfunding contract. It's responsible for the creation of a new fund.

```rust
public entry fun create_fund(target: u64, ctx: &mut TxContext) {
    let fund_uid = object::new(ctx);
    let fund_id: ID = object::uid_to_inner(&fund_uid);

    let fund = Fund {
        id: fund_uid,
        target,
        raised: balance::zero(),
    };

    // create and send a fund owner capability for the creator
    transfer::transfer(FundOwnerCap {
        id: object::new(ctx),
        fund_id: fund_id,
    }, tx_context::sender(ctx));

    // share the object so anyone can donate
    transfer::share_object(fund);
}
```

The `create_fund` function takes two parameters: the `target` amount for the crowdfunding campaign in USD (type u64) and a mutable reference to the transaction context `ctx` (type TxContext).

The function starts by creating a unique identifier for the fund using the `object::new` function, which is then converted to an ID type. The Fund struct is then instantiated with this ID, the target amount, and an initial raised balance of zero.

Next, the function creates a `FundOwnerCap` object, giving the creator of the fund (who is identified by the `tx_context::sender(ctx)`) the capability to withdraw funds once the target is met. The FundOwnerCap is then transferred to the fund creator.

Finally, the fund is made public by using the `transfer::share_object(fund)` function, allowing anyone to donate to this fund. This is a significant aspect of any crowdfunding platform: it should allow anyone to contribute to a fund, hence the need for making the Fund object publicly accessible.

Now, anyone with a reference to this Fund can call the `donate` function and contribute to the campaign. In the next section, we'll take a closer look at the donate function.

### The `donate` Function

Here's where we see the magic of Supra's price feed Oracle in action! This function allows someone to donate a certain amount of SUI tokens to the fund. This function accepts four parameters: a reference to the OracleHolder (which fetches the current price of SUI in USD), a mutable reference to the Fund object (which receives the donation), the amount to be donated, and a mutable reference to the transaction context ctx.

```rust
  public entry fun donate(oracle_holder: &OracleHolder, fund: &mut Fund, amount: Coin<SUI>, ctx: &mut TxContext) {

    // get the amount being donated in SUI for receipt.
    let amount_donated: u64 = coin::value(&amount);

    // add the amount to the fund's balance
    let coin_balance = coin::into_balance(amount);
    balance::join(&mut fund.raised, coin_balance);

    // get price of sui_usdt using Supra's Oracle SValueFeed
    let (price, _,_,_) = get_price(oracle_holder, 90);

    // adjust price to have the same number of decimal points as SUI
    let adjusted_price = price / 1000000000;

    // get the total raised amount so far in SUI
    let raised_amount_sui = (balance::value(&fund.raised) as u128);

    // get the fund target amount in USD
    let fund_target_usd = (fund.target as u128) * 1000000000; // to align with 9 decimal places

    // check if the fund target in USD has been reached (by the amount donated in SUI)
    if ((raised_amount_sui * adjusted_price) >= fund_target_usd) {
      // emit event that the target has been reached
        event::emit(TargetReached { raised_amount_sui });
    };
      
    // create and send receipt NFT to the donor (for tax purposes :))
    let receipt: Receipt = Receipt {
        id: object::new(ctx), 
        amount_donated,
      };
      
      transfer::transfer(receipt, tx_context::sender(ctx));
  }
```

First, the function captures the `amount_donated` in SUI by calling `coin::value(&amount)`. This will be used to create a receipt for the donation.

Next, the donation amount is added to the total raised balance of the fund using `balance::join`. The `coin::into_balance` function is used to convert the Coin type into Balance.

Then, the function uses the `get_price` function to fetch the current price of SUI in USD. The `get_price` function returns four values (value, decimal, timestamp, round), but we're only interested in the value, so we disregard the rest with (\_, \_, \_, \_). The 90 passed into `get_price` is the price feed ID for the SUI/USDT pair.

The function then calculates the total amount raised so far in SUI and the target amount for the fund in USD.

If the total amount raised in SUI (converted to USD using the price feed) meets or exceeds the target amount in USD, an event `TargetReached` is emitted. This will be broadcast to all nodes in the network, signaling that the crowdfunding goal has been met.

Finally, a Receipt object is created to keep a record of the donation. This receipt, represented as an NFT, is sent to the donor for record-keeping (and perhaps for tax purposes as well).

### The `withdraw_funds` function

The `withdraw_funds` function allows the owner of a fund to withdraw funds from it. This function accepts three parameters: a reference to a FundOwnerCap capability (which authorizes the withdrawal), a mutable reference to the Fund object from which the funds will be withdrawn, and a mutable reference to the transaction context ctx.

```rust
  // withdraw funds from the fund contract, requires a fund owner capability that matches the fund id
  public entry fun withdraw_funds(cap: &FundOwnerCap, fund: &mut Fund, ctx: &mut TxContext) {

    assert!(&cap.fund_id == object::uid_as_inner(&fund.id), ENotFundOwner);

    let amount: u64 = balance::value(&fund.raised);

    let raised: Coin<SUI> = coin::take(&mut fund.raised, amount, ctx);

    transfer::public_transfer(raised, tx_context::sender(ctx));
    
  }
```

First, an assert statement checks that the fund ID associated with the FundOwnerCap capability matches the fund ID of the fund from which the funds are to be withdrawn. If not, it raises an error `ENotFundOwner`, halting the execution of the function.

Next, the function calculates the total balance amount of the fund by calling `balance::value(&fund.raised)`.

Then, the function calls `coin::take` to withdraw the total balance from the fund and convert it back into Coin form. The `coin::take` function mutates the fund's balance, subtracting the withdrawn amount from it.

Finally, the function calls `transfer::public_transfer` to transfer the withdrawn funds to the fund owner, i.e., the sender of the transaction. This completes the withdrawal process. The fund's balance is reduced by the withdrawn amount, and the owner's balance is increased by the same amount.

This `withdraw_funds` function showcases the power of the Sui blockchain's capability-based security model. Only an entity with the right capability can withdraw funds, ensuring secure fund management. Also, with the Move language's resource semantics, funds can't be duplicated or destroyed arbitrarily, ensuring the integrity of the financial transactions.

## Step 7: Conclusion

This tutorial aimed to provide a comprehensive yet easily digestible guide to building a crowdfunding smart contract using Supra’s price feed Oracle in Move on the Sui blockchain. We dissected each part of the contract, from setting up your development environment to defining structures and implementing crucial functionalities.

We also emphasized the simplicity and robustness of integrating Supra’s price feeds into your blockchain applications.

As a developer, understanding and utilizing these concepts not only provides you with the tools to build advanced, secure, and efficient applications but also helps you leverage the powerful features of blockchain technology to its fullest potential. We hope that this guide serves as a solid foundation for your journey into blockchain development and inspires you to explore further and create innovative solutions.

Here is the complete code for the crowdfunding smart contract written in Move for the Sui blockchain, utilizing Supra's price feed Oracle.

```rust
module crowd_fund::fund_contract {
  
  use sui::object::{Self, UID, ID};
  use sui::transfer;
  use sui::tx_context::{Self, TxContext};
  use sui::coin::{Self, Coin};
  use sui::balance::{Self, Balance};
  use sui::sui::SUI;
  use sui::event;
  use SupraOracle::SupraSValueFeed::{get_price, OracleHolder};


  // ====== Errors ======

  const ENotFundOwner: u64 = 0;


  // ====== Objects ======
  
  struct Fund has key {
    id: UID,
    target: u64, // in USD 
    raised: Balance<SUI>,
  }

  struct Receipt has key {
    id: UID, 
    amount_donated: u64, // in MIST - 10^-9 of a SUI. One billion MIST equals one SUI. 
  }

  // Capability that grants a fund creator the right to withdraw funds.
  struct FundOwnerCap has key { 
    id: UID,
    fund_id: ID, 
     }


  // ====== Events ======

  // For when the fund target is reached.
  struct TargetReached has copy, drop {
      raised_amount_sui: u128,
    }


   // ====== Functions ======

  public entry fun create_fund(target: u64, ctx: &mut TxContext) {
    let fund_uid = object::new(ctx);
    let fund_id: ID = object::uid_to_inner(&fund_uid);

    let fund = Fund {
        id: fund_uid,
        target,
        raised: balance::zero(),
    };

    // create and send a fund owner capability for the creator
     transfer::transfer(FundOwnerCap {
          id: object::new(ctx),
          fund_id: fund_id,
        }, tx_context::sender(ctx));

    // share the object so anyone can donate
    transfer::share_object(fund);
  }

  public entry fun donate(oracle_holder: &OracleHolder, fund: &mut Fund, amount: Coin<SUI>, ctx: &mut TxContext) {

    // get the amount being donated in SUI for receipt.
    let amount_donated: u64 = coin::value(&amount);

    // add the amount to the fund's balance
    let coin_balance = coin::into_balance(amount);
    balance::join(&mut fund.raised, coin_balance);

    // get price of sui_usdt using Supra's Oracle SValueFeed
    let (price, _,_,_) = get_price(oracle_holder, 90);

    // adjust price to have the same number of decimal points as SUI
    let adjusted_price = price / 1000000000;

    // get the total raised amount so far in SUI
    let raised_amount_sui = (balance::value(&fund.raised) as u128);

    // get the fund target amount in USD
    let fund_target_usd = (fund.target as u128) * 1000000000; // to align with 9 decimal places

    // check if the fund target in USD has been reached (by the amount donated in SUI)
    if ((raised_amount_sui * adjusted_price) >= fund_target_usd) {
      // emit event that the target has been reached
        event::emit(TargetReached { raised_amount_sui });
    };
      
    // create and send receipt NFT to the donor (for tax purposes :))
    let receipt: Receipt = Receipt {
        id: object::new(ctx), 
        amount_donated,
      };
      
      transfer::transfer(receipt, tx_context::sender(ctx));
  }

  // withdraw funds from the fund contract, requires a fund owner capability that matches the fund id
  public entry fun withdraw_funds(cap: &FundOwnerCap, fund: &mut Fund, ctx: &mut TxContext) {

    assert!(&cap.fund_id == object::uid_as_inner(&fund.id), ENotFundOwner);

    let amount: u64 = balance::value(&fund.raised);

    let raised: Coin<SUI> = coin::take(&mut fund.raised, amount, ctx);

    transfer::public_transfer(raised, tx_context::sender(ctx));
    
  }
}
```

\\
