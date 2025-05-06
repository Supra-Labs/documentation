# VRF Developer Guide

{% hint style="info" %}
Supra dVRF is in transition period from upgrading to dVRF 3.0. Please identify the correct version of Supra dVRF deployed in your preferred [network](networks.md) and pick the relevant dev guide sections accordingly.
{% endhint %}

Supra dVRF requires a whitelisted subscription to the service with a customer-controlled wallet address to act as the main reference. Once your wallet is whitelisted, you can use it to whitelist any number of VRF requester smart contracts and top up the deposit balance maintained with Supra in order to pay for the gas fees of callback (response) transactions.

* Please refer to the [VRF subscription FAQ](vrf-subscription-model.md) page for a better understanding of how it works.
* Please refer to the [Network Addresses](networks.md) page for Supra dVRF version and contract addresses. \\

The following guide explains the steps a user has to follow in order to request random numbers.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

{% tabs %}
{% tab title="Supra  Move" %}
{% hint style="info" %}
In Supra network, You will interact with a single Supra Smart contract to connect and consume Supra VRF.
{% endhint %}

#### Step 1: Create the Supra dVRF Interface[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-1-create-the-supra-router-contract-interface) <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

Import interface from [https://github.com/Entropy-Foundation/vrf-interface](https://github.com/Entropy-Foundation/vrf-interface) git repository and add subdirectory mainnet or testnet whatever you would like to use for integration.\\

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

\\
{% endtab %}

{% tab title="Solidity" %}
Before you dive in..

{% hint style="info" %}
In EVM ( Ethereum Virtual Machine) based networks, you will interact with two Supra smart contracts to connect and consume Supra VRF.\
\
**Supra Deposit Contract** - to whitelist smart contracts under the registered wallet address, pre-pay/top up the callback gas fee deposit maintained with Supra.

**Supra Router Contract** - to request and receive random numbers.
{% endhint %}

#### Step 1: Whitelist your admin wallet and Consumer Contracts <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

This works differently in dVRF 2.0 and dVRF 3.0. Please chose accordingly.

| <p><strong>VRF 3.0</strong><br><strong>Whitelist your wallet</strong><br>User should first whitelist themselves by calling <code>"</code><strong><code>function addClientToWhitelist(uint128 _maxGasPrice, uint128 _maxGasLimit)</code></strong><code>”</code> of the Deposit contract, specifying “maxGasPrice” and “maxGasLimit”.<br>Read more on how to determine the Max Gas Price and Max Gas Limit <a href="https://docs.supra.com/oracles/dvrf/vrf-subscription-model#how-to-set-determine-max-gas-limit-and-max-gas-price">here</a>.<br><br><strong>Whitelist your consumer contracts</strong><br>"<strong><code>addContractToWhitelist(address _contractAddress, uint128 _callbackGasPrice, uint128 _callbackGasLimit)</code></strong>”. The parameter this function takes is the User’s contract address along with callbackGasPrice and callbackGasLimit for the contract which should be smaller than the maxGasPrice and maxGasLimit respectively.</p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>VRF 2.0</strong><br><strong>Whitelist your wallet</strong><br>Feel free to send us a request via this <a href="https://forms.gle/WFvpBXg67GmDrokv5">form</a> to get your wallet registered with Supra.<br><br><strong>Whitelist your Consumer Contracts</strong><br><code>“</code><strong><code>addContracttoWhitelist(address _contractAddress)”</code></strong> - Once Supra team confirms your whitelist request you can call this function with your consumer contract address.</p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

#### Step 2: Create the Supra VRF Interface[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-1-create-the-supra-router-contract-interface) <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

Once you are whitelisted (by requesting Supra team or by self whitelisting), add the following code to the consumer contract i.e., the contract which uses VRF as a service. You can also add the code and inherit the interface in the requester contract.

{% code overflow="wrap" %}
```solidity
interface ISupraRouterContract {
	function generateRequest(string memory _functionSig, uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
	function generateRequest(string memory _functionSig, uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}
```
{% endcode %}

This interface will help the requester contract interact with the Supra Router contract through which the requester contract can use the VRF service.

#### Step 3: Configure the Supra Router Contract Address[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-2-configure-the-supra-router-contract-address) <a href="#step-2-configure-the-supra-router-contract-address" id="step-2-configure-the-supra-router-contract-address"></a>

Contracts that need random numbers should utilize the Supra Router Contract. In order to do that, they need to create an interface and bind it to the on-chain [address](networks.md) of the Supra router contract.

```solidity
contract ExampleContract {
    ISupraRouter internal supraRouter;

    constructor(address routerAddress) {
        supraRouter = ISupraRouter(routerAddress);
    }
}
```

#### Step 4: Use the VRF Service and Request a Random Number[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-3-use-the-vrf-service-and-request-for-a-random-number) <a href="#step-3-use-the-vrf-service-and-request-for-a-random-number" id="step-3-use-the-vrf-service-and-request-for-a-random-number"></a>

In this step, we will use the **“generateRequest”** function of the Supra Router Contract to create a request for random numbers. There are two modes for the **"generateRequest"** function. The only difference between them is that you can optionally provide a client-side input, which will also be part of the payload being threshold-signed to provide randomness.

* **\_functionSig**- a string parameter; here, the requester contract will have to pass the function signature, which will receive the callback i.e., a random number from the Supra Router Contract. The function signature should be in the form of the function name following the parameters it accepts. We will see an example later in the document.
* **\_rngCount** - an integer parameter; this is for the number of random numbers a particular requester wants to generate. Currently, we can generate a maximum of 255 random numbers per request.
* **\_numConfirmations** - an integer parameter (Minimum: 1 - Maximum :20) that specifies the number of block confirmations needed before Supra VRF can generate the random number.
* **\_clientSeed** (optional) - an optional integer parameter that could be provided by the client (defaults to 0). This is for additional unpredictability. The source of the seed can be a UUID of 256 bits. This can also be from a centralized source.
* **\_clientWalletAddress** - an “address” type parameter that takes the client wallet address that is already registered with the Supra Team as input.

Supra's VRF process requires splitting the contract logic into two functions.

* **The request function** - the signature of this function is up to the developer.
* **The callback function** - the signature must be of the form **“uint256 nonce, uint256\[] calldata rngList”**

```solidity
function exampleRNG() external {  
     //Function validation and logic
     // requesting 10 random numbers
     uint8 rngCount = 10; 

     // we want to wait for 1 confirmations before the request is considered complete/final. 
     // You can customize this value to meet your needs. Minimum: 1, Maximum: 20.
     uint256 numConfirmations = 1; 
	address _clientWalletAddress = //Add the whitelisted wallet address here
     uint256 generated_nonce = supraRouter.generateRequest(“exampleCallback(uint256,uint256[])”, rngCount, numConfirmations, _clientWalletAddress);

     // store generated_nonce if necessary (eg: in a hashmap)
     // this can be used to track parameters related to the request, such as user address, nft address etc in a lookup table
     // these can be accessed inside the callback since the response from supra will include the nonce
}
```

#### Step 5 - Add the Validation in the Callback Function of the Requester Contract[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-4---add-the-validation-in-the-callback-function-of-requester-contract) <a href="#step-4---add-the-validation-in-the-callback-function-of-requester-contract" id="step-4---add-the-validation-in-the-callback-function-of-requester-contract"></a>

Inside the callback function where the requester contract wants the random number (in this example, the callback function is exampleCallback), the requester contract will have to add the validation such that only the Supra router contract can call the function. The validation is necessary to protect against malicious contracts/users executing the callback with fake data.

For example, if the callback function is pickWinner in the requester contract, the snippet can be as follows:

```solidity
function exampleCallback(uint256 _nonce ,uint256[] _rngList) external {
    require(msg.sender == address(supraRouter));
    // Following the required logic of the function
 }
```

#### Step 6 : Whitelist Your Requester Contract with Supra Deposit Contract and Deposit Funds[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#step-1-create-the-supra-router-contract-interface) <a href="#step-1-create-the-supra-router-contract-interface" id="step-1-create-the-supra-router-contract-interface"></a>

It is important to note that your wallet address must be registered with Supra before this step. If that is completed, then you need to whitelist your requester smart contract under your wallet address and deposit funds to be paid for your callback transactions gas fees.

* The simplest way to interact with the deposit contract will be through Remix IDE.
* Go to [Remix IDE](http://remix.ethereum.org/) & create a file with the name _IDepositContract.sol_
*   Paste the following code in the file:

    <pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">interface IDepositContract{
    	function depositFundClient() external payable;
    	function addContractToWhitelist(address _contractAddress, uint128 _callbackGasPrice, uint128 _callbackGasLimit) external;
    	function removeContractFromWhitelist(address _contractAddress) external;
    	function withdrawFundClient(uint128 _amount) external;
    <strong>	function updateMaxGasPrice(uint128 _maxGasPrice) external;
    </strong>	function updateMaxGasLimit(uint128 _maxGasLimit) external;
    	function updateCallbackGasPrice(address _contractAddress, uint128 _callbackGasPrice) external;
    	function updateCallbackGasLimit(address _contractAddress, uint128 _callbackGasLimit) external;
    	function checkClientFund(address _clientAddress) external view returns (uint128);
    	function checkEffectiveBalance(address _clientAddress) external view returns (uint256);
    	function checkMinBalanceClient(address _clientAddress) external view returns (uint128);
    	function countTotalWhitelistedContractByClient(address _clientAddress) external view returns (uint256);
    	function getSubscriptionInfoByClient(address _clientAddress) external view returns (uint64, bool);
    	function isMinimumBalanceReached(address _clientAddress) external view returns (bool);
    	function listAllWhitelistedContractByClient(address _clientAddress) external view returns (address[] memory);
    }
    </code></pre>
* Navigate to the “Navigate & run Transactions” tab in remix and paste the Deposit Contract address into the text box next to the “At Address” button. Then, press the At Address button. You will find the instance for the Deposit Contract created using which a user can interact and use the features provided by the Deposit Contract.
* The following functions will facilitate whitelisting your requester smart contracts and fund deposits:
  1.  **\[VRF 2.0]** -\
      “**addContracttoWhitelist(address)”** - The whitelisted users will have to whitelist the contract that they will be using to request the random numbers. The parameter this function takes is the user’s contract address. This function will be called after the user deploys the requester contract post development and makes it ready for interacting with the Supra Contracts.\
      \
      &#xNAN;**\[VRF 3.0]** -

      “**addContractToWhitelist(address \_contractAddress, uint128 \_callbackGasPrice, uint128 \_callbackGasLimit)**”. Once your wallet is whitelisted you have to whitelist your contract to request for random numbers. The parameter this function takes is the User’s contract address along with callbackGasPrice and callbackGasLimit for the contract which should be <= maxGasPrice and maxGasLimit respectively.


  2. **“depositFundClient()” -** This is another mandatory function for a user to use once before the user starts requesting from that contract. This is a function that will deposit funds in the Deposit Contract from the users for the response/callback transaction. The funds for a specific user should remain higher than the minimum amount set by the Supra (ex. 0.1 ETH for Arbitrum testnet) for the new request transactions to be accepted.
* In essence, the user will have to interact with the Deposit Contract and add funds for their accounts, which will be utilized for the response transaction gas fee. There will be a script from Supra that will monitor the funds and will alert the user if a refill is required.

**Additional Functions of the Deposit Contract and its Usage**

* **removeContractFromWhitelist(address \_contractAddress)**: The function helps user’s to remove their contract addresses from the whitelist if that is no longer required.
  * \_contractAddress: The parameter, again will only accept the contract address & not the EOA address. Also the contract addresses which are already whitelisted will only be accepted.
* **setMinBalanceClient(uint256 \_limit)**: Applicable only in VRF 2.1, This function is for the user to set the minimum limit of the fund they will add in the deposit contract. If a user doesn’t set it from their side, there is a limit set by the supra team and that will be considered and if the user sets it, the value which will be higher (the one set by the user or the supra team) will be selected as the limit for the particular user. The limit helps keep track of the balance and notify the user’s if the balance is getting lower and near to the minimum balance. Also the user’s fund added in the client should be greater than the minimum balance to request the random number.
  * \_limit: The parameter takes the limit value in wei, for example if a user wants to set the limit as 0.001 ETH, the user will have to pass it as 1000000000000000 in WEI.
* **withdrawFundClient(uint256 \_amount)**: The function helps users to withdraw the amount of funds they have deposited in the deposit contract.
  * \_amount: The total amount of funds that the user wants to retrieve from the contract, however the amount should not exceed the deposited amount.
* **checkClientFund(address \_clientAddress)**: The function helps the users to check their total available balance in the deposit contract.
  * \_clientAddress: The user’s wallet address has to be passed for which the user wants to check their balance, the contract addresses won’t be accepted by the function and only the EOA address. Also the address has to be whitelisted.
* **checkEffectiveBalance(address \_clientAddress)**: The function helps the user to check the effective balance, that is the balance that is left after subtracting the minimum balance from the total fund that has been deposited by the user.
  * \_clientAddress: The function will take the user wallet address as a parameter.
* **checkMinBalanceSupra()**: This function checks the minimum balance set by the supra team. As mentioned earlier, the minimum balance is already set by the supra team and the function helps the user to check its value.
* **checkMinBalance(address \_clientAddress)**: The function checks the minimum balance for a particular user. As mentioned earlier, the minimum balance can be set by the supra team as well as the user. If the minimum balance is set by both, the minimum balance for a particular user will be the higher value among the two.
  * \_clientAddress: The function takes the whitelisted wallet address of the particular user as an input parameter.
* **countTotalWhitelistedContractByClient(address \_clientAddress)**: The function returns the total number of contracts that has been whitelisted by a particular user.
  * \_clientAddress: This function also takes the whitelisted wallet address of a particular user as an input parameter.
* **getSubscriptionInfoByClient(address \_clientAddress)**: The function returns the information like when the subscription for a particular user started, when it will end and if the user is a participant of the SNAP programme or not.
  * \_clientAddress: The parameter value remains the same as the previous function, a user wallet address which is whitelisted.
*   **isMinimumBalanceReached(address \_clientAddress)**: The function returns a boolean value, that is true if the user’s balance is lesser than or equal to the minimum balance set else it returns false.

    * \_clientAddress: This is again the user’s wallet address which is whitelisted.


* **listAllWhitelistedContractByClient(address \_clientAddress)**: The function returns the list of addresses, the contract addresses which are whitelisted by a particular user.
  * \_clientAddress: The function takes the user’s whitelisted wallet address as an input parameter.
* **\[VRF 3.0] updateMaxGasPrice(uint128 \_maxGasPrice):** This function helps the user to update their max gas price i.e. the maximum gas price beyond which the callback transaction won’t be processed. Updating the max gas price will also update the minimum balance for the user.
  * \_maxGasPrice: Updated max gas price the user wants to set, this must be greater than zero.
* **\[VRF 3.0] updateCallbackGasPrice(address \_contractAddress, uint128 \_callbackGasPrice):** This function helps users to update the callback gas price for a specific contract i.e. the maximum gas price beyond which the callback transaction won’t be processed for the contract.
  * \_contractAddress: Address of the contract for which user wants to update the callback gas price. The contract must be whitelisted.
  * \_callbackGasPrice: Updated callback gas price for the contract, this must be greater than zero.
* **\[VRF 3.0] updateCallbackGasLimit(address \_contractAddress, uint128 \_callbackGasLimit):** This function helps users to update the callback gas limit for a specific contract i.e. the maximum amount of gas consumed by a specific consumer contract. .
  * \_contractAddress: Address of the contract for which user wants to update the callback gas limit. The contract must be whitelisted.
  * \_callbackGasLimit: Updated callback gas limit for the contract, this must be greater than zero.
* **\[VRF 3.0] updateMaxGasLimit(uint128 \_maxGasLimit)**: This function helps the user to update their max gas limit i.e. the maximum amount of gas consumed by the user’s consumer contract. Updating the max gas limit will also update the minimum balance for the user.
  * \_maxGasLimit: Updated max gas limit the user wants to set, this must be greater than zero.

#### Example Implementation[​](https://qa-docs.supraoracles.com/docs/vrf-dev-guide#example-implementation) <a href="#example-implementation" id="example-implementation"></a>

Please find below an example implementation of Supra dVRF.

* The function **getRNGForUser** is using the VRF service by calling the **generateRequest** function of the Supra Router Contract.
* Then we store the username of the user requesting the random number mapped to the nonce returned by **generateRequest.**
* Then the callback function prints the random numbers requested by a specific user, and it has the signature: **myCallbackUsername(uint256 nonce, uint256\[] calldata rngList)**.

Once Supra generates the random number and it is verified by the on-chain logic to be authentic, **myCallbackUsername** is executed by the Supra Router, which completes the second half of the process. The nonce from the first argument is used to look up the username that originated the request.

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed,address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations,address _clientWalletAddress) external returns(uint256);
}
contract Interaction {
   address supraAddr;
   constructor(address supraSC) {
       supraAddr = supraSC;
   }
   mapping (uint256 => string ) result;
   mapping (string => uint256[] ) rngForUser;
   function getRNGForUser(uint8 rngCount, string memory username) external {
     // You can customize this value to meet your needs. Minimum: 1, Maximum: 20.
     uint256 numConfirmations = 1; 
      uint256 nonce =  ISupraRouter(supraAddr).generateRequest("myCallbackUsername(uint256,uint256[])", rngCount, numConfirmations, 123, msg.sender);
//Can pass "msg.sender" when calling from the whitelisted wallet address
      result[nonce] = username;
   }
   function myCallbackUsername(uint256 nonce, uint256[] calldata rngList) external {
      require(msg.sender == supraAddr, "only supra router can call this function");
      uint8 i = 0;
      uint256[] memory x = new uint256[](rngList.length);
      rngForUser[result[nonce]] = x;
      for(i=0; i<rngList.length;i++){
         rngForUser[result[nonce]][i] = rngList[i] % 100;
      }
   }
   function viewUserName(string memory username) external view returns (uint256[] memory) {
      return rngForUser[username];
   }
   }								
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
  This function is for the user to set the minimum limit of the fund they will add in the deposit contract. The higher of the user’s value or, the default value (10 Aptos) will be saved as the minimum balance for the subscription. Total fund balance should be higher than the minimum balance in order to receive random numbers from Supra. Please read ore on Supra subscription model [here](vrf-subscription-model.md) for a better understanding.\\
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
