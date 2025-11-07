# Legacy VRF - V2 Developer Guide

Supra dVRF requires a whitelisted subscription to the service with a customer-controlled wallet address to act as the main reference. Once your wallet is whitelisted, you can use it to whitelist any number of  consumer contracts and top up the deposit balance maintained with Supra in order to pay for the gas fees of callback (response) transactions.

* Please refer to the [VRF subscription FAQ](../build-third-party-evm-networks/vrf-subscription-model.md) page for a better understanding of how it works.
* Please refer to the [Network Addresses](../learn-supra-dvrf/networks.md) page for Supra dVRF version and contract addresses.&#x20;

The following guide explains the steps a user has to follow in order to request random numbers.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

{% tabs %}
{% tab title="Supra  Move" %}
{% hint style="info" %}
In Supra network, You will interact with a single Supra Smart contract to connect and consume Supra VRF.
{% endhint %}

**Whitelisting on  SUPRA L1 is still required for VRF 2.0** Send us a request via this [form](https://forms.gle/WFvpBXg67GmDrokv5) to get your wallet registered with Supra

#### Step 1: Create the Supra dVRF Interface[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-1-create-the-supra-router-contract-interface) <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

Import interface from [https://github.com/Entropy-Foundation/vrf-interface](https://github.com/Entropy-Foundation/vrf-interface) git repository and add subdirectory mainnet or testnet whatever you would like to use for integration.

#### Step 2: Configure Supra dVRF

Testnet

<pre class="language-toml" data-overflow="wrap"><code class="lang-toml"><strong>[dependencies.SupraVrf]
</strong>git = "https://github.com/Entropy-Foundation/vrf-interface"
subdir = "supra/testnet"
rev = "master"                                   
</code></pre>

Mainnet

{% code overflow="wrap" %}
```toml
[dependencies.SupraVrf]
git = "https://github.com/Entropy-Foundation/vrf-interface"
subdir = "supra/mainnet"
rev = "master"
```
{% endcode %}

#### Step 3: Use the VRF service and request a Random Number[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-3-use-the-vrf-service-and-request-for-a-random-number)

In this step, we will use the “**rng\_request**” function of the SupraContract to create a request for random numbers.

* **\_sender**- as an signer parameter, here the requester contract will have to pass his own signature.
* **\_callback\_address** - a address parameter, here the requester contract will have to pass the callback contrat address which will receive the callback.
* **\_callback\_module** - a string parameter, here the requester contract will have to pass the module name which will receive the callback.
* **\_callback\_function** - a string parameter, here the requester contract will have to pass the function name which will receive the callback. We will see an example later in the document.
* **\_rng\_count** - an integer u8 parameter, it is for the number of random numbers a particular requester wants to generate. Currently, we can generate a maximum of 255 random numbers per request.
* **\_client\_seed** - an integer u64 parameter that could be provided by the client (defaults to 0). This is for additional unpredictability. The source of the seed can be a UUID of 64 bits. This can also be from a centralized source.
* **\_num\_confirmations** - an integer u64 parameter that specifies the number of block confirmations needed before supra VRF can generate the random number.

{% code overflow="wrap" %}
```solidity
public entry fun rng_request(sender: &signer, rng_count: u8, client_seed: u64, num_confirmations: u64) {
    create_empty_struct(sender);
    let callback_address = @example;
    let callback_module = string::utf8(b"example_module");
    let callback_function = string::utf8(b"distribute");
    let _rng = supra_vrf::rng_request(sender, callback_address, callback_module, callback_function, rng_count, client_seed, num_confirmations);
}
```
{% endcode %}

#### Step 4 - Define requester contract callback function

Requester contract callback functions should be defined as public entry functions and will mainly take 7 parameters. Below are the list of parameters.

In the request contract callback function they need to call _**supra\_vrf::verify\_callback**_ function to verify that the callback signature which they received is valid and then the requester contract gets the random number list.

{% code overflow="wrap" %}
```solidity
public entry fun distribute(
    nonce: u64,
    message: vector<u8>,
    signature: vector<u8>,
    caller_address: address,
    rng_count: u8,
    client_seed: u64,
) acquires RandomNumberList {
    let verified_num: vector<u256> = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);
    let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
    let random_numbers = table::borrow_mut(random_num_list, nonce);
    *random_numbers = verified_num
} 
```
{% endcode %}

#### Step 5 - Whitelist your requester contract with Supra Deposit Module and deposit funds​

It is important to note that your wallet address must be registered with Supra before this step. If that is completed, then you need to whitelist your requester smart contract under your wallet address and deposit funds to be paid for your call back transactions gas fees.

* The simplest way to interact with the deposit contract is to use the Supra CLI directly.
* Following functions will facilitate whitelisting your requester smart contracts and fund deposits.
  * **add\_contract\_to\_whitelist(contract\_address: address)**:\
    The whitelisted users will have to whitelist their contract which they will be using to request for random numbers. The parameter this function takes is the User’s contract address. This function will be called after the user deploys the requester contract post development and makes it eligible to consume random numbers.
  * **deposit\_fund(deposit\_amount:** u6&#x34;**):** This function will deposit funds in the deposit module from the users for the response/callback transaction. The funds for a specific user should remain higher than the minimum amount set by the Supra( 10 Supra for Supra testnet) for the new request transactions to be accepted.

There will be a script from Supra which will be monitoring the funds and will alert the user if a refill is required.

**Additional functions of the Deposit Module and its usage**

1. **deposit::add\_contract\_to\_whitelist(contract\_address: address):** This function is for the users to whitelist their requester contract address, given that the user’s wallet address which interacts with the function is already whitelisted by the supra team.
2. **deposit::deposit\_fund(deposit\_amount:** u6&#x34;**):** This function is for the users to add their funds, which will be used to pay the transaction fees for the response transaction of their request.
3. **remove\_contract\_from\_whitelist(contract\_address: address):** The function helps user’s to remove their contract addresses from the whitelist if that is no longer required.
4. **client\_setting\_minimum\_balance(min\_balance\_limit\_client:** u6&#x34;**):** This function is for the user to set the minimum limit of the fund they will add in the deposit contract. The higher of the user’s value or, the default value (10 Supra) will be saved as the minimum balance for the subscription. Total fund balance should be higher than the minimum balance in order to receive random numbers from Supra. Please read ore on Supra subscription model here for better understanding.
5. **withdraw\_fund(withdraw\_amount:** u6&#x34;**) :** The function helps users to withdraw funds.
6. **check\_client\_fund(client\_address: address):** The function helps the users to check their total available balance in the deposit contract.
7. **check\_effective\_balance(client\_address: address):** The function helps the user to check the effective balance, that is the balance that is left after subtracting the minimum balance from the total fund that has been deposited by the user.
8. **check\_min\_balance\_supra():** This function checks the minimum balance set by the supra team. As mentioned earlier, the minimum balance is already set by the supra team and the function helps the user to check its value.
9. **check\_min\_balance(client\_address: address):** The function checks the minimum balance for a particular user. As mentioned earlier, the minimum balance can be set by the supra team as well as the user. If the minimum balance is set by both, the minimum balance for a particular user will be the higher value among the two.
10. **get\_subscription\_by\_client(client\_address: address):** The function returns the information like when the subscription for a particular user started, when it will end and if the user is a participant of the SNAP programme or not.
11. **has\_minimum\_balance\_reached(client\_address: address):** The function returns the list of addresses, the contract addresses which are whitelisted by a particular user.

#### Example Implementation[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#example-implementation)

Please find an example implementation of VRF on Supra network. ([link here](https://github.com/Entropy-Foundation/supra-vrf-examples/tree/master/supra_l1_vrf/example))

In the example below,

* The function rng\_request is using the VRF service by calling the rng\_request function of the SupraContract.
* Then the callback function returns the signature and all other required parameters.
* Then call verify\_callback function of the SupraContract and get the random number list.

{% code overflow="wrap" %}
```solidity
module example::example_module {
    use aptos_std::table;
    use supra_addr::supra_vrf;
    use std::string;

    struct RandomNumberList has key {
        random_numbers: table::Table<u64, vector<u256>>
    }

    fun init_module(sender: &signer) {
        move_to(sender, RandomNumberList { random_numbers: table::new() });
    }

    public entry fun rng_request(sender: &signer, rng_count: u8, client_seed: u64, num_confirmations: u64) acquires RandomNumberList  {
        let callback_address = @example;
        let callback_module = string::utf8(b"example_module");
        let callback_function = string::utf8(b"distribute");
        let nonce = supra_vrf::rng_request(sender, callback_address, callback_module, callback_function, rng_count, client_seed, num_confirmations);
    
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        table::add(random_num_list, nonce, vector[]);
    }

    public entry fun distribute(
        nonce: u64,
        message: vector<u8>,
        signature: vector<u8>,
        caller_address: address,
        rng_count: u8,
        client_seed: u64,
    ) acquires RandomNumberList {
        let verified_num: vector<u64> = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        let random_numbers = table::borrow_mut(random_num_list, nonce);
        *random_numbers = verified_num
    }

    #[view]
    public fun get_rng_number_from_nonce(nonce: u64): vector<u256> acquires RandomNumberList {
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        *table::borrow(random_num_list, nonce)
    }
}

```
{% endcode %}

To call rng\_request function as below format :

{% code overflow="wrap" %}
```solidity
supra move tool run --function-id default::example_module::rng_request --args u8:{rng_count} u64:{client_seed} u64:{num_confirm}
```
{% endcode %}

Retrieve random number

{% code overflow="wrap" %}
```solidity
supra move tool view -–function-id default::example_module::get_rng_number_from_nonce --args u64:{nonce}
```
{% endcode %}
{% endtab %}

{% tab title="Aptos Move" %}
Before you dive in..

{% hint style="info" %}
In Aptos network, You will interact with a single Supra Smart contract to connect and consume Supra VRF.
{% endhint %}

#### Step 1: Create the Supra dVRF Interface[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-1-create-the-supra-router-contract-interface) <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

Import interface from [https://github.com/Entropy-Foundation/vrf-interface](https://github.com/Entropy-Foundation/vrf-interface) git repository and add subdirectory mainnet or testnet whatever you would like to use for integration.\\

#### Step 2: Configure the Supra dVRF

Import Below supra vrf-framework dependency in your requester contract Move.toml file based on the network you are connected. \\

Testnet

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity"><strong>[dependencies]
</strong>supra_vrf = { git = "https://github.com/Entropy-Foundation/vrf-interface", subdir = "aptos/testnet", rev = "0806fc554fe0235f4f16a99827e99bdffb4d0c94"  }   
</code></pre>

Mainnet

{% code overflow="wrap" %}
```
[dependencies]
supra_vrf = { git = "https://github.com/Entropy-Foundation/vrf-interface", subdir = "aptos/mainnet", rev = "0806fc554fe0235f4f16a99827e99bdffb4d0c94"  } soon
```
{% endcode %}

#### Step 3: Use the VRF service and request a Random Number[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-3-use-the-vrf-service-and-request-for-a-random-number)

In this step, we will use the “**rng\_request**” function of the SupraContract to create a request for random numbers.

* **\_sender.** - as an signer parameter, here the requester contract will have to pass his own signature.
* **\_callback\_address** - a address parameter, here the requester contract will have to pass the callback contrat address which will receive the callback.
* **\_callback\_module** - a string parameter, here the requester contract will have to pass the module name which will receive the callback.
* **\_callback\_function** - a string parameter, here the requester contract will have to pass the function name which will receive the callback. We will see an example later in the document.
* **\_rng\_count** - an integer u8 parameter, it is for the number of random numbers a particular requester wants to generate. Currently, we can generate a maximum of 255 random numbers per request.
* **\_client\_seed** - an integer u64 parameter that could be provided by the client (defaults to 0). This is for additional unpredictability. The source of the seed can be a UUID of 64 bits. This can also be from a centralized source.
* **\_num\_confirmations** - an integer u64 parameter that specifies the number of block confirmations needed before supra VRF can generate the random number.

{% code overflow="wrap" %}
```solidity
public entry fun rng_request(sender: &signer, rng_count: u8, client_seed: u64, num_confirmations: u64) {
    create_empty_struct(sender);
    let callback_address = @example;
    let callback_module = string::utf8(b"example_module");
    let callback_function = string::utf8(b"distribute");
    let _rng = supra_vrf::rng_request(sender, callback_address, callback_module, callback_function, rng_count, client_seed, num_confirmations);
}
```
{% endcode %}

#### Step 4 - Define requester contract callback function

Requester contract callback functions should be defined as public entry functions and will have 7 parameters. Below are the list of parameters.

In the request contract callback function they need to call supra\_vrf::verify\_callback function to verify that the callback signature which they received is valid and then the requester contract gets the random number list.

{% code overflow="wrap" %}
```solidity
public entry fun distribute(
    nonce: u64,
    message: vector<u8>,
    signature: vector<u8>,
    caller_address: address,
    rng_count: u8,
    client_seed: u64,
) acquires RandomNumberList {
    let verified_num: vector<u64> = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);
    let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
    let random_numbers = table::borrow_mut(random_num_list, nonce);
    *random_numbers = verified_num
}
```
{% endcode %}

#### Step 5 - Whitelist your requester contract with Supra Deposit Module and deposit funds​

It is important to note that your wallet address must be registered with Supra before this step. If that is completed, then you need to whitelist your requester smart contract under your wallet address and deposit funds to be paid for your call back transactions gas fees.

* The simplest way to interact with the deposit contract will be through Aptos Explorer or you can directly use Aptos CLI.
* Following functions will facilitate whitelisting your requester smart contracts and fund deposits.
* **deposit::add\_contract\_to\_whitelist(contract\_address: address)**\
  The whitelisted users will have to whitelist their contract which they will be using to request for random numbers. The parameter this function takes is the User’s contract address. This function will be called after the user deploys the requester contract post development and makes it eligible to consume random numbers.\\
* **deposit::deposit\_fund(deposit\_amount: u64)**\
  This function will deposit funds in the deposit module from the users for the response/callback transaction. The funds for a specific user should remain higher than the minimum amount set by the Supra( 10 Aptos for Aptos testnet) for the new request transactions to be accepted. There will be a script from Supra which will be monitoring the funds and will alert the user if a refill is required. \\
* **add\_contract\_to\_whitelist(contract\_address: address)**\
  This function is for the users to whitelist their requester contract address, given that the user’s wallet address which interacts with the function is already whitelisted by the supra team.\\
* **deposit\_fund(deposit\_amount: u64)**\
  This function is for the users to add their funds, which will be used to pay the transaction fees for the response transaction of their request.\\
* **remove\_contract\_from\_whitelist(contract\_address: address)**\
  The function helps user’s to remove their contract addresses from the whitelist if that is no longer required.\\
* **client\_setting\_minimum\_balance(min\_balance\_limit\_client: u64)**\
  This function is for the user to set the minimum limit of the fund they will add in the deposit contract. The higher of the user’s value or, the default value (10 Aptos) will be saved as the minimum balance for the subscription. Total fund balance should be higher than the minimum balance in order to receive random numbers from Supra. Please read ore on Supra subscription model [here](../build-third-party-evm-networks/vrf-subscription-model.md) for a better understanding.\\
* **withdraw\_fund(withdraw\_amount: u64)**\
  The function helps users to withdraw funds.\\
* **check\_client\_fund(client\_address: address)**\
  The function helps the users to check their total available balance in the deposit contract.\\
* **check\_effective\_balance(client\_address: address)**\
  The function helps the user to check the effective balance, that is the balance that is left after subtracting the minimum balance from the total fund that has been deposited by the user.\\
* **check\_min\_balance\_supra()**\
  This function checks the minimum balance set by the supra team. As mentioned earlier, the minimum balance is already set by the supra team and the function helps the user to check its value.\\
* **check\_min\_balance(client\_address: address)**\
  The function checks the minimum balance for a particular user. As mentioned earlier, the minimum balance can be set by the supra team as well as the user. If the minimum balance is set by both, the minimum balance for a particular user will be the higher value among the two.\\
* **get\_subscription\_by\_client(client\_address: address)**\
  The function returns the information like when the subscription for a particular user started, when it will end and if the user is a participant of the SNAP programme or not.\\
* **has\_minimum\_balance\_reached(client\_address: address)**\
  The function returns the list of addresses, the contract addresses which are whitelisted by a particular user.

### Example Implementation[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#example-implementation)

Please find below an example implementation of Supra Aptos VRF ([link here](https://github.com/Entropy-Foundation/supra-vrf-examples/tree/master/supra_aptos_vrf/example2)).

In the example below,

* The function rng\_request is using the VRF service by calling the rng\_request function of the SupraContract.
* Then the callback function returns the signature and all other required parameters.
* Then call verify\_callback function of the SupraContract and receive the random number.\\

{% code overflow="wrap" %}
```solidity
module example::example_module {
    use aptos_std::table;
    use supra_addr::supra_vrf;
    use std::string;

    struct RandomNumberList has key {
        random_numbers: table::Table<u64, vector<u256>>
    }

    fun init_module(sender: &signer) {
        move_to(sender, RandomNumberList { random_numbers: table::new() });
    }

    public entry fun rng_request(sender: &signer, rng_count: u8, client_seed: u64, num_confirmations: u64) acquires RandomNumberList  {
        let callback_address = @example;
        let callback_module = string::utf8(b"example_module");
        let callback_function = string::utf8(b"distribute");
        let nonce = supra_vrf::rng_request(sender, callback_address, callback_module, callback_function, rng_count, client_seed, num_confirmations);
    
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        table::add(random_num_list, nonce, vector[]);
    }

    public entry fun distribute(
        nonce: u64,
        message: vector<u8>,
        signature: vector<u8>,
        caller_address: address,
        rng_count: u8,
        client_seed: u64,
    ) acquires RandomNumberList {
        let verified_num: vector<u256> = supra_vrf::verify_callback(nonce, message, signature, caller_address, rng_count, client_seed);
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        let random_numbers = table::borrow_mut(random_num_list, nonce);
        *random_numbers = verified_num
    }

    #[view]
    public fun get_rng_number_from_nonce(nonce: u64): vector<u256> acquires RandomNumberList {
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        *table::borrow(random_num_list, nonce)
    }
}
```
{% endcode %}

To call rng\_request function as below format,

{% code overflow="wrap" %}
```solidity
aptos move run --function-id default::example_module::rng_request --args u8:{rng_count} u64:{client_seed} u64:{num_confirm}
```
{% endcode %}

Retrieve random number

{% code overflow="wrap" %}
```solidity
aptos move view -–function-id default::example_module::get_rng_number_from_nonce --args u64:{nonce}
```
{% endcode %}
{% endtab %}
{% endtabs %}

