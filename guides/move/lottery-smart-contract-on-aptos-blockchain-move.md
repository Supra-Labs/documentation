---
description: A Simple Guide to Supra’s VRF Integration with Move on Aptos
---

# Lottery Smart Contract on Aptos Blockchain // Move

Welcome to this step-by-step guide on building a lottery smart contract using Supra's Verifiable Random Function (VRF) on the Aptos blockchain. By integrating the Supra VRF, we ensure that the lottery draw is fair and transparent. At the end of this guide, you'll know how to implement a Supra-powered VRF in your smart contracts for any of your randomization needs. Let's get started!

## Step 1: Organizing Project Structure

Let’s start by organizing the files and directories for our project:

```
game/
│
├── sources/
│   └── GamingContract.move
│
└── Move.toml
```

In this setup:

`game/` is the root directory of our project.

Inside `game/`, we have a `sources/` directory containing the `GamingContract.move` file, which holds our smart contract code.

The Move.toml file is in the root directory and is vital for dependency management and address configuration.

Now that our project structure is in place, it's time to configure the `Move.toml` file.

## Step 2: Configuring the Move.toml File

The `Move.toml` file is crucial as it sets the stage for our project dependencies and addresses.

```
[package]
name = 'gamingContract'
version = '1.0.0'
upgrade_policy = "compatible"

[addresses]
game = <YOUR_ACCOUNT_ADDRESS>

[dependencies]
AptosFramework = { git = "https://github.com/aptos-labs/aptos-core.git", subdir = "aptos-move/framework/aptos-framework/", rev = "eb0144a39ada521d8dee01c9dbd601853d383fb3" }
SupraVrf = { git = "https://github.com/Entropy-Foundation/vrf-interface", subdir = "aptos/testnet", rev = "ff49e3688411d2374f75994a8713f86a5760868a"  }
```

Here’s how each section of the `Move.toml` file contributes to the project setup:

**`[package]` Section**

This is where we declare the identity of our package:

* `name`: `"gamingContract"` is the designated name for our project.
* `version`: `"1.0.0"` indicates the initial release, adhering to semantic versioning principles.
* `upgrade_policy`: `"compatible"` ensures any updates are backward compatible.

**`[addresses]` Section**

In the `[addresses]` section, we specify the address mapping for our module:

* `game`: Replace `<YOUR_ACCOUNT_ADDRESS>` with your own account address in hexadecimal format. This address signifies where the `game` module will be instantiated on the Aptos blockchain.

**`[dependencies]` Section**

Our lottery contract depends on two external packages:

* `AptosFramework`: This is the foundational library for the Aptos blockchain. It provides modules for standard operations like coin management and account functionality. We link directly to the Aptos repository on GitHub, ensuring we have the most recent and stable code at the commit “eb0144a39ada521d8dee01c9dbd601853d383fb3”.
* `SupraVrf`: The VRF from Supra is crucial for our lottery's random number generation. It's also linked via GitHub, pointing to the testnet directory of the Entropy Foundation's VRF interface at a specific commit "ff49e3688411d2374f75994a8713f86a5760868a".

With our `Move.toml`file configured, we've laid the groundwork necessary for our Supra Lottery smart contract. This setup interlinks our project with the essential libraries and sets up the address from which our contract will operate.

In the next steps, we’ll explore how to utilize these dependencies to build the core logic of the lottery system, ensuring fairness with the integration of Supra’s VRF, and eventually deploying it on Aptos. Get ready to turn the concept of a decentralized lottery into a reality!

## Step 3: Importing Required Modules

Our code begins with the necessary imports that allow us to utilize various functions and structures within the Aptos blockchain.

```
module game::GamingContract {
    use std::signer;
    use std::vector;
    use std::string;
    use std::error;
    use std::option::{Self, Option};
    use aptos_framework::account::{Self, SignerCapability};
    use aptos_framework::aptos_account;
    use supra_addr::supra_vrf;
}
```

The first line begins with the definition of our module named `GamingContract` within the `game` package. The module is where we will encapsulate all the logic for our lottery application.

We then import all of the necessary Aptos modules for managing accounts, handling transaction authentication, and dealing with data structures like vectors and strings. Additionally, the inclusion of error-handling mechanisms ensures robustness in contract execution. A crucial aspect of this setup is the integration of the Supra VRF module, which brings in the capability for generating verifiable random numbers.

This integration is key to the functionality of our lottery contract, enabling it to operate as a secure and decentralized system. With access to these fundamental Aptos primitives and the advanced random number generation feature provided by Supra VRF, our contract is well-equipped to manage a lottery system efficiently. It lays the groundwork for executing critical operations such as securely holding funds, conducting fair winner selection processes, and facilitating transparent and trustless participation in the lottery.

Next, we will delve into the error codes and data structures that form the backbone of our smart contract's logic.

## Step 4: Defining Errors and Constants

After setting up our project's structure and dependencies, we focus on the core of our lottery smart contract's code. A good starting point is to define error constants, which will help us handle exceptions and ensure our contract behaves as expected.

In our `GamingContract` module, we've established several error constants to guard against various unwanted scenarios:

<pre><code>// Error codes

<strong>const WINNER_ALREADY_SELECTED: u64 = 1;
</strong>const LOTTERY_NOT_EXIST: u64 = 2;
const COIN_AMOUNT_IS_NOT_ENOUGH_TO_PARTICIPATE: u64 = 3;
const YOU_ARE_NOT_LOTTERY_OWNER: u64 = 4;
const E_NO_PARTICIPANTS: u64 = 5;
const E_ALREADY_PARTICIPATED: u64 = 6;

// Other Constants

const ENTRY_COIN: u64 = 100000000; // 1 Aptos
const RESOURCE_SEED: vector&#x3C;u8> = b"Lottery"; // This could be any seed
</code></pre>

**Error Constants Explained:**

* ```
  const WINNER_ALREADY_SELECTED: u64 = 1;
  ```

This error code is used to indicate that a winner has already been selected for the lottery.

* ```
  const LOTTERY_NOT_EXIST: u64 = 2;
  ```

This constant is used when a transaction refers to a non-existent lottery, perhaps due to an incorrect address or uninitialized contract.

* ```
  const COIN_AMOUNT_IS_NOT_ENOUGH_TO_PARTICIPATE: u64 = 3;
  ```

This constant is triggered when a participant attempts to enter the lottery with an amount lower than the required entry fee.

* ```
  const YOU_ARE_NOT_LOTTERY_OWNER: u64 = 4;
  ```

This is used to restrict certain actions to the lottery owner, preventing unauthorized users from performing administrative tasks.

* ```
  const E_NO_PARTICIPANTS: u64 = 5;
  ```

Indicates that there are no participants in the lottery, an essential check for the functionality of the game.

* ```
  const E_ALREADY_PARTICIPATED: u64 = 6;
  ```

To ensure fairness and to prevent double entries, this error will be raised if an address that has already entered the lottery tries to participate again in the same drawing.

**Other Constants Explained:**

* ```
  const ENTRY_COIN: u64 = 100000000; // 1 Aptos
  ```

This represents the entry fee required to participate in the lottery, set to the equivalent of one Aptos token, and is used in the `participate` function to ensure that participants contribute a minimum amount to enter the lottery.

* ```
  const RESOURCE_SEED: vector<u8> = b"Lottery"; // This could be any seed
  ```

This is a unique identifier used for creating a resource account specific to the lottery.

Defining error constants at the beginning of your smart contract is a best practice we should maintain. It brings clarity and makes your code easier to maintain. Clear error handling also improves security and the overall user experience by providing specific feedback on what went wrong during the contract's execution.

With our error codes in place, we can now proceed to build out the data structures and functions that will utilize these constants to ensure our lottery contract operates seamlessly and securely.

Next up, we will define the core structure to start giving shape to our decentralized application.

## Step 5: Defining The Struct

In this section, we will define the data structure that forms the backbone of our lottery contract.

### The `Lottery` Struct

```
    struct Lottery has key {
        participants: vector<address>,
        winner: Option<address>,
        amount: u64,
        request_nonce: Option<u64>,
        random_number: Option<vector<u64>>,
        signer_cap: SignerCapability,
    }
```

Here's what each field represents:

1. **`participants: vector<address>`**: This field holds a dynamic array (vector) of participant addresses. It records all the addresses that have entered the lottery, thus maintaining a list of all participants. This is crucial for tracking entries and ensuring that the lottery operates with a clear record of its participants.
2. **`winner: Option<address>`**: The `winner` field utilizes the `Option` type to potentially hold the address of the lottery winner. This field is optional because, at various stages of the lottery, there might not be a winner determined yet. The use of `Option` allows for a flexible structure that accommodates the presence or absence of a winner.
3. **`amount: u64`**: This field represents the total amount of funds (in Aptos tokens) accumulated in the lottery. As participants enter the lottery, their contributions are added to this total, effectively creating a prize pool for the eventual winner.
4. **`request_nonce: Option<u64>`**: Used in conjunction with Supra's VRF, the `request_nonce` field stores a nonce value related to the random number generation request. This nonce is critical for correlating the request with its response, ensuring the integrity and traceability of the random number generation process.
5. **`random_number: Option<vector<u64>>`**: This field stores the random number(s) generated by the VRF, wrapped in an `Option` type. Similar to the `winner` field, this flexibility is necessary as the random number is not immediately available at the start of the lottery and is only generated during the course of the lottery's execution.
6. **`signer_cap: SignerCapability`**: A crucial aspect of the `Lottery` struct, the `signer_cap` field holds a `SignerCapability`. This capability is integral for authorizing certain actions within the contract, particularly those that require verification of the lottery's ownership or management rights, such as transferring the prize amount to the winner.

In summary, the `Lottery` struct is a comprehensive and thoughtfully structured data model that effectively captures the state and operations of the lottery. It is engineered to handle participant management, winner determination, prize accumulation, and integration with external systems like Supra's VRF, all while maintaining the necessary security and integrity for a blockchain-based application.

As we proceed with the development of the `GamingContract`, this struct will be pivotal in orchestrating the various functionalities of our decentralized lottery system.

In the next step, we will delve into the functions that interact with the `Lottery` struct, providing the functionality needed to operate the lottery.

## Step 6: Defining The Functions

In this section, we define the functions that perform operations in our smart contract.

### The `init_module` Function

This function is the entry point for setting up the lottery game's operational environment. Here is a breakdown of what this function does:

```
    fun init_module(owner_signer: &signer) {
        let (resource_signer, signer_cap) = account::create_resource_account(owner_signer, RESOURCE_SEED);
        let lottery = Lottery {
            participants: vector::empty<address>(),
            winner: option::none(),
            amount: 0,
            request_nonce: option::none(),
            random_number: option::none(),
            signer_cap,
        };
        move_to(&resource_signer, lottery);
    }
```

The `init_module` function is designed to establish the foundational elements for the lottery to operate. This function begins by invoking `account::create_resource_account` with the `owner_signer` and a unique `RESOURCE_SEED`. This action is crucial as it leads to the creation of a resource account specifically dedicated to the lottery, differentiating it from the owner's personal account for enhanced security and organization. Alongside this, a `SignerCapability` is obtained, which is instrumental in authorizing future operations related to this account.

The heart of this function lies in initializing the `Lottery` struct. This initialization process involves setting up a variety of key components of the lottery: an empty vector for `participants` indicating the lottery is open for entries, an option type for the `winner` signifying that no winner has been selected yet, a zero initial `amount` for the prize pool, and option types for both `request_nonce` and `random_number` to support future VRF operations. These components collectively form the state of the lottery, capturing every essential aspect from participants to the prize amount and preparing the system for the integration of random number generation.

Once the `Lottery` struct is fully initialized with these default values, it is moved into global storage under the resource account's address. This step is significant as it preserves the lottery state across the blockchain, allowing it to be accessed and modified in subsequent transactions. This storage under a resource account ensures that the lottery's operational data is kept separate and secure, essential for maintaining the integrity and reliability of the lottery system.

Overall, the `init_module` function in the `GamingContract` is the entry point that sets up the lottery system for success. By carefully creating a dedicated resource account and initializing the lottery's core state, the function paves the way for a secure, transparent, and efficient operation of the lottery, thus enabling a fair and decentralized gaming experience.

The smart contract is now primed to enroll participants and manage the lottery events.

### The `get_resource_address` Function

```
    #[view]
    fun get_resource_address(): address {
        account::create_resource_address(&@game, RESOURCE_SEED)
    }
```

The `get_resource_address` function is marked with the `#[view]` attribute, indicating that it's a read-only function used to fetch data without altering the blockchain's state. This function plays a pivotal role in retrieving the address of the resource account created for managing the lottery. It employs the `account::create_resource_address` method, combining the unique identifier of our game contract (`@game`) with the predefined `RESOURCE_SEED`. This combination ensures that the returned address is exclusively associated with our specific lottery game, maintaining a clear separation of concerns and enhancing security.

### The `participate` Function

Now let's move on to the `participate` function so that our users can engage with the lottery. Marked as a public entry, this function is callable by external transactions and is a key user interaction point. It's designed to allow users to enter the lottery by submitting a threshold amount of tokens.

```
    public entry fun participate(user: &signer, amount: u64) acquires Lottery {
        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        assert!(option::is_none(&lottery.winner), error::invalid_state(WINNER_ALREADY_SELECTED));

        assert!(!vector::contains(&lottery.participants,  &signer::address_of(user)), error::already_exists(E_ALREADY_PARTICIPATED));

        // check amount should be more than entry fee
        assert!(amount > ENTRY_COIN, error::permission_denied(COIN_AMOUNT_IS_NOT_ENOUGH_TO_PARTICIPATE));
        aptos_account::transfer(user, lottery_resource_address, amount);

        vector::push_back(&mut lottery.participants, signer::address_of(user));
        lottery.amount = lottery.amount + amount;
    }
```

When a user invokes `participate`, they must provide their signer reference and the amount they wish to enter into the lottery. The function kicks off by calling the `get_resource_address` to fetch the lottery's resource account address. An `assert` statement checks for the existence of the Lottery struct at this address, throwing an error if it's not found, which is a critical validation step ensuring that the lottery is correctly initialized and operational.

The function then retrieves a mutable reference to the lottery's state using `borrow_global_mut`. This step is crucial as it allows the function to update the lottery's participant list and the total amount staked in the lottery, reflecting these changes on the blockchain.

Next, the function performs several checks: it first ensures that a winner has not already been selected by examining the `winner` field in the `Lottery` struct. It then checks if the user has already participated in the lottery by searching for their address in the `participants` vector. If the user is already a participant, it aborts and raises an error to prevent duplicate entries.

Assuming the user is a new participant and the staked amount exceeds the required entry fee (`ENTRY_COIN`), the function proceeds to transfer the specified amount from the user's account to the lottery's resource account using `aptos_account::transfer`. This transfer is a fundamental operation as it moves the user's stake into the lottery's pool, incrementing the total prize amount.

Finally, the user's address is added to the lottery's `participant` list, and their staked amount is added to the lottery's total pool. This update is vital as it accurately reflects the current state of the lottery, including the updated participant list and the new total amount, in preparation for the eventual lottery drawing.

In essence, the `participate` function encapsulates the critical logic for user entries into the lottery. It manages financial transactions and maintains the integrity of the lottery's state, ensuring that every entry is recorded and that the total prize pool accurately represents the staked assets of participants. This function is key to providing a transparent, fair, and engaging user experience in the decentralized lottery system.

### The `generate_random_number` Function

The `generate_random_number` function is the gateway to determining the winner of the lottery. It is a critical piece that interacts with Supra’s VRF to ensure the winner is chosen without bias or tampering.

```
    public entry fun generate_random_number(owner_signer: &signer) acquires Lottery {
        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        assert!(signer::address_of(owner_signer) == @game, error::unauthenticated(YOU_ARE_NOT_LOTTERY_OWNER));

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        assert!(!vector::is_empty(&lottery.participants), error::unavailable(E_NO_PARTICIPANTS));

        let callback_module = string::utf8(b"GamingContract");
        let callback_function = string::utf8(b"pick_winner"); // function name
        let rng_count: u8 = 1; // how many random number you want to generate
        let client_seed: u64 = 0; // client seed using as seed to generate random. if you don't want to use then just assign 0
        let num_confirmations: u64 = 1; // how many confirmation required for random number

        let nonce = supra_vrf::rng_request(owner_signer, @game, callback_module, callback_function,  rng_count, client_seed, num_confirmations);
        lottery.request_nonce = option::some(nonce);
    }
```

When invoked, this function commences by retrieving the address of the lottery's resource account through the `get_resource_address` function. A vital check follows to ensure that the `Lottery` struct exists at this address, using an `assert` statement to validate its presence. This step is crucial for confirming that the lottery is properly initialized and active.

Another layer of security is added by verifying the caller's authority. The function checks whether the `owner_signer` address matches the address associated with the game contract (`@game`). This verification is imperative to ensure that only the authorized owner of the lottery can trigger the random number generation process, thereby safeguarding against unauthorized access or manipulation.

Upon passing these security checks, the function proceeds to borrow a mutable reference to the lottery's state. This action is pivotal as it allows the function to update the lottery's state with the random number once generated.

The function sets up for the VRF request by defining the callback module and function names, crucial for the VRF service to know where to send the generated random number. It also specifies the number of random numbers to generate (`rng_count`), the client seed (which could be set to any value or left as zero), and the number of confirmations required for the random number.

A `nonce`, serving as a unique identifier for the random number request, is then generated via the `supra_vrf::rng_request`. This `nonce` ensures that each random number generation request is distinct and traceable, a key feature for maintaining transparency and integrity in the lottery process.

Finally, the `nonce` is stored in the `Lottery` struct under `request_nonce`. This storage is critical as it links the lottery's current state to the specific VRF request, setting the stage for the subsequent winner selection once the random number is received.

In summary, the `generate_random_number` function in the revised code encapsulates the essential steps for initiating a verifiable and unbiased winner selection process in the lottery. By meticulously setting up the VRF request and ensuring stringent security checks, the function upholds the principles of fairness and transparency, crucial for the legitimacy and trustworthiness of the decentralized lottery.

### The `pick_winner` Function

Now, let's talk about the `pick_winner` function.

```
    public entry fun pick_winner(
        nonce: u64,
        message: vector<u8>,
        signature: vector<u8>,
        caller_address: address,
        rng_count: u8,
        client_seed: u64,
    ) acquires Lottery {

        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        let verified_num = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        lottery.random_number = option::some(verified_num);

        let random_number = *vector::borrow(&verified_num, 0);
        let random_index = random_number % vector::length(&lottery.participants);
        let winner_address = *vector::borrow(&lottery.participants, random_index);
        lottery.winner = option::some(winner_address);

        let resource_signer = account::create_signer_with_capability(&lottery.signer_cap);
        aptos_account::transfer(&resource_signer, winner_address, lottery.amount);
    }

```

The `pick_winner` function is a public entry point designed to be called as a callback by the VRF service once the random number generation process is completed. It plays a pivotal role in determining the lottery winner in a fair and transparent manner. This function takes several parameters, including the `nonce`, `message`, `signature`, `caller_address`, `rng_count`, and `client_seed`, all essential for verifying the authenticity and randomness of the number provided by Supra's VRF service.

Upon invocation, the function initiates its process by first retrieving the address of the lottery's resource account. It then asserts the existence of the `Lottery` struct at this address, ensuring that the lottery is properly set up and operational.

The core of this function lies in its interaction with the `supra_vrf::verify_callback` method. This method is used to authenticate the received random number by verifying its signature against the provided `nonce` and other parameters. This verification step is crucial as it guarantees the randomness and integrity of the number, ensuring the lottery's fairness.

Once the random number is verified and deemed authentic, the function proceeds to determine the winner. It first extracts the random number from the verified array and calculates its index within the range of the participant list. This index is then used to pinpoint the winner's address from the list of `participants` in the `Lottery` struct. By assigning the winner based on a random and verified number, the function ensures that every participant has an equal chance of winning, maintaining the lottery's unbiased nature.

Following the determination of the winner, the function executes a key transaction: transferring the total accumulated amount in the lottery to the winner's address. This is facilitated by the `aptos_account::transfer` method, which moves the prize amount from the resource account to the winner.

In summary, the `pick_winner` function encapsulates the essential elements of a transparent and fair lottery drawing. By leveraging the Supra VRF for random number generation and ensuring robust on-chain verification, the function guarantees an unbiased selection of the winner. This approach not only upholds the integrity of the lottery but also instills trust and reliability in the decentralized lottery system, making it a secure and attractive platform for participants.

## Step 7: Conclusion

In this guide, we created a transparent, secure, and fair lottery system on the blockchain, harnessing the capabilities of the Aptos blockchain and Supra’s VRF. We've meticulously gone through the structure of the `GamingContract` smart contract, elucidating on how it utilizes the Move programming language to establish a reliable lottery mechanism.

We've seen how the contract initializes, accepts participants, generates random numbers via Supra’s VRF service, and ultimately picks a winner in a trustless manner. The importance of verifiable randomness in the context of a blockchain-based lottery cannot be overstated—it is the cornerstone of trust and fairness in the game.

The full code for the Gaming Contract is provided below:

```
module game::GamingContract {
    use std::signer;
    use std::vector;
    use std::string;
    use std::error;
    use std::option::{Self, Option};
    use aptos_framework::account::{Self, SignerCapability};
    use aptos_framework::aptos_account;
    use supra_addr::supra_vrf;

    const ENTRY_COIN: u64 = 100000000; // 1 Aptos
    const WINNER_ALREADY_SELECTED: u64 = 1;
    const LOTTERY_NOT_EXIST: u64 = 2;
    const COIN_AMOUNT_IS_NOT_ENOUGH_TO_PARTICIPATE: u64 = 3;
    const YOU_ARE_NOT_LOTTERY_OWNER: u64 = 4;
    const E_NO_PARTICIPANTS: u64 = 5;
    const E_ALREADY_PARTICIPATED: u64 = 6;

    const RESOURCE_SEED: vector<u8> = b"Lottery"; // This could be any seed

    struct Lottery has key {
        participants: vector<address>,
        winner: Option<address>,
        amount: u64,
        request_nonce: Option<u64>,
        random_number: Option<vector<u64>>,
        signer_cap: SignerCapability,
    }


    fun init_module(owner_signer: &signer) {
        let (resource_signer, signer_cap) = account::create_resource_account(owner_signer, RESOURCE_SEED);
        let lottery = Lottery {
            participants: vector::empty<address>(),
            winner: option::none(),
            amount: 0,
            request_nonce: option::none(),
            random_number: option::none(),
            signer_cap,
        };
        move_to(&resource_signer, lottery);
    }

    #[view]
    /// Get resource account address
    fun get_resource_address(): address {
        account::create_resource_address(&@game, RESOURCE_SEED)
    }

    /// this entry function is for player registration
    entry fun participate(user: &signer, amount: u64) acquires Lottery {
        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        assert!(option::is_none(&lottery.winner), error::invalid_state(WINNER_ALREADY_SELECTED));

        assert!(!vector::contains(&lottery.participants,  &signer::address_of(user)), error::already_exists(E_ALREADY_PARTICIPATED));

        // check amount should be more than entry fee
        assert!(amount > ENTRY_COIN, error::permission_denied(COIN_AMOUNT_IS_NOT_ENOUGH_TO_PARTICIPATE));
        aptos_account::transfer(user, lottery_resource_address, amount);

        vector::push_back(&mut lottery.participants, signer::address_of(user));
        lottery.amount = lottery.amount + amount;
    }

    /// generate random number
    public entry fun generate_random_number(owner_signer: &signer) acquires Lottery {
        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        assert!(signer::address_of(owner_signer) == @game, error::unauthenticated(YOU_ARE_NOT_LOTTERY_OWNER));

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        assert!(!vector::is_empty(&lottery.participants), error::unavailable(E_NO_PARTICIPANTS));

        let callback_module = string::utf8(b"GamingContract");
        let callback_function = string::utf8(b"pick_winner"); // function name
        let rng_count: u8 = 1; // how many random number you want to generate
        let client_seed: u64 = 0; // client seed using as seed to generate random. if you don't want to use then just assign 0
        let num_confirmations: u64 = 1; // how many confirmation required for random number

        let nonce = supra_vrf::rng_request(owner_signer, @game, callback_module, callback_function,  rng_count, client_seed, num_confirmations);
        lottery.request_nonce = option::some(nonce);
    }

    /// supra vrf calls this function
    public entry fun pick_winner(
        nonce: u64,
        message: vector<u8>,
        signature: vector<u8>,
        caller_address: address,
        rng_count: u8,
        client_seed: u64,
    ) acquires Lottery {

        let lottery_resource_address = get_resource_address();
        assert!(exists<Lottery>(lottery_resource_address), error::not_found(LOTTERY_NOT_EXIST));

        let verified_num = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);

        let lottery = borrow_global_mut<Lottery>(lottery_resource_address);
        lottery.random_number = option::some(verified_num);

        let random_number = *vector::borrow(&verified_num, 0);
        let random_index = random_number % vector::length(&lottery.participants);
        let winner_address = *vector::borrow(&lottery.participants, random_index);
        lottery.winner = option::some(winner_address);

        let resource_signer = account::create_signer_with_capability(&lottery.signer_cap);
        aptos_account::transfer(&resource_signer, winner_address, lottery.amount);
    }
}



```
