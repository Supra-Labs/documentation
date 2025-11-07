# Request Random Numbers

### Supra dVRF Contracts Architecture in EVMs

Supra dVRF uses a two-contract system:

* **Router Contract**: Handles VRF requests and delivers random numbers to your callback function
* **Deposit Contract**: Manages user funds, whitelisting, and gas configurations\


Ready to get random? Follow these four simple steps to integrate Supra dVRF into your smart contract and start requesting verifiable random numbers.

### Prerequisites

Before requesting random numbers, ensure you have:

* ✅ Whitelisted wallet address with configured gas parameters
* ✅ Deposited sufficient funds in the Deposit Contract
* ✅ Whitelisted your consumer contract address
*

### Step 1:  Create the Router Interface

Add the Router Contract interface to your smart contract. This interface enables communication with Supra's VRF service.\
The interface provides two versions of `generateRequest` - use the first one if you want to provide your own seed for additional entropy, or the second one for standard random number generation.

#### Parameters

* **`_functionSig`**: The signature of your callback function that will receive the random numbers (e.g., "myCallback(uint256,uint256\[])")
* **`_rngCount`**: Number of random numbers to generate in a single request (maximum 255)
* **`_numConfirmations`**: Number of block confirmations after request transaction to wait before generating random numbers (Min:1 , Max: 20)
* **`_clientSeed`** _(optional)_: Your custom seed value for additional randomness (can be UUID, timestamp, etc.)
* **`_clientWalletAddress`**: Your whitelisted wallet address that will pay for the transaction
* **Returns**: `uint256 nonce` - A unique identifier to track your request

```solidity
interface ISupraRouterContract {
    // With custom client seed for additional randomness
    function generateRequest(
        string memory _functionSig, 
        uint8 _rngCount, 
        uint256 _numConfirmations, 
        uint256 _clientSeed, 
        address _clientWalletAddress
    ) external returns(uint256);
    
    // Without client seed (uses default seed)
    function generateRequest(
        string memory _functionSig, 
        uint8 _rngCount, 
        uint256 _numConfirmations, 
        address _clientWalletAddress
    ) external returns(uint256);
}
```



### Step 2 : Configure Router Address

Initialize the Router Contract connection in your constructor. This binds your contract to Supra's on-chain router.

```solidity
contract ExampleContract {
    ISupraRouter internal supraRouter;
    
    constructor(address routerAddress) {
        supraRouter = ISupraRouterContract(routerAddress);
    }
}

```

### Step 3 : Request Random Numbers

Call the `generateRequest` function to request random numbers. This function returns a nonce that you can use to track your request and link it back to the original requester or context.&#x20;

```solidity
function exampleRequest() external {
    uint8 rngCount = 10;
    uint256 numConfirmations = 1;
    address clientWallet = msg.sender;
    
    uint256 nonce = supraRouter.generateRequest(
        "exampleCallback(uint256,uint256[])",
        rngCount,
        numConfirmations,
        clientWallet
    );
    
    // Store nonce if necessary (e.g., in a hashmap)
    // This can be used to track parameters related to the request
}
```

### Step 4 : Implement Callback Function

Create a callback function to receive the random numbers. This function must have the exact signature: `(uint256 nonce, uint256[] memory rngList)`. Please refer to below example.\
The callback validation is crucial to prevent malicious contracts from calling your function with fake random data.

```solidity
function exampleCallback(uint256 nonce, uint256[] memory rngList) external {
    // Security: Only allow Router Contract to call this function
    require(msg.sender == address(supraRouter), "Only Supra Router allowed");
    
    // Process your random numbers
    for(uint i = 0; i < rngList.length; i++) {
        uint256 randomNumber = rngList[i];
        // Your logic here
    }
}
```

### Complete Example

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ISupraRouter {
    function generateRequest(
        string memory _functionSig, 
        uint8 _rngCount, 
        uint256 _numConfirmations, 
        uint256 _clientSeed,
        address _clientWalletAddress
    ) external returns(uint256);
}

contract Interaction {
    address supraAddr;
    
    constructor(address supraSC) {
        supraAddr = supraSC;
    }
    
    mapping(uint256 => string) result;
    mapping(string => uint256[]) rngForUser;
    
    function exampleRequest(uint8 rngCount, string memory username) external {
        // You can customize this value to meet your needs. Minimum: 1, Maximum: 20.
        uint256 numConfirmations = 1; 
        
        uint256 nonce = ISupraRouter(supraAddr).generateRequest(
            "exampleCallback(uint256,uint256[])", 
            rngCount, 
            numConfirmations, 
            123, 
            msg.sender
        );
        // Can pass "msg.sender" when calling from the whitelisted wallet address
        
        result[nonce] = username;
    }
    
    function exampleCallback(uint256 nonce, uint256[] calldata rngList) external {
        require(msg.sender == supraAddr, "only supra router can call this function");
        
        uint256[] memory x = new uint256[](rngList.length);
        rngForUser[result[nonce]] = x;
        
        for(uint8 i = 0; i < rngList.length; i++) {
            rngForUser[result[nonce]][i] = rngList[i] % 100;
        }
    }
    
    function viewUserName(string memory username) external view returns (uint256[] memory) {
        return rngForUser[username];
    }
}
```

### Next Steps

After implementing the request functionality, you can:

* Monitor your balance using Deposit Contract view functions
* Update gas parameters for your contracts
* Scale your random number requests based on your application needs

The random numbers generated are cryptographically secure and verifiable, making them suitable for gaming, NFT minting, and other blockchain applications requiring true randomness.
