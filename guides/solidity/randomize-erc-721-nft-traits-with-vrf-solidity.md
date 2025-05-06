---
description: >-
  This guide uses our VRF service to randomize the trait values of each ERC-721
  token that is minted.
---

# Randomize ERC-721 NFT Traits with VRF

{% hint style="info" %}
This guide assumes that you have successfully interacted with the [Supra Deposit Contract](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/v2-guide.md) to whitelist and fund the VRF requests for the use of VRF V2. This step is only applicable if the[ version of your destination chain is V2](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/networks.md).
{% endhint %}

## Intro

If you're interested in NFTs, you may have heard about randomizing traits/properties assigned to each token. This is a very common topic within the NFT space and is something that many developers (like you!) will face throughout their journey. There are a few different ways to do this. Today, we’ll be randomizing the traits for each token and then storing them on-chain within an NFT smart contract.

## Getting Started

Imagine this: You’re creating an NFT project for a game where each token represents a character. When a new character is created (minted), we want to randomly assign their starting stats/levels. Of course, we’ll want to be able to level up and view the stats for each token after they're generated as well.

In this guide, we’ll be [inheriting](https://docs.soliditylang.org/en/v0.8.17/contracts.html?highlight=inheritance#inheritance) [OpenZeppelin's ERC-721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) for our NFT contract, [OpenZeppelin’s Counters](https://docs.openzeppelin.com/contracts/4.x/api/utils#Counters) for tracking the next token ID to be minted, and the Supra VRF to generate verifiable random numbers for the token traits.

## Time to Code

First, make a new solidity file `randomTraits.sol` and declare our contract `randomTraits` with an empty constructor. After that, we'll set up our dependencies, variables, and functions before finalizing our code.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
 
contract randomTraits {
 
    constructor(){
       
    }
 
}
```

### Dependencies

Now is a great time to set up our dependencies. We have three (3) things that we need to do. First, we'll import [OpenZeppelin’s ERC-721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721), [inherit](https://docs.soliditylang.org/en/v0.8.17/contracts.html?highlight=inheritance#inheritance) it, and then update our constructor accordingly. The [constructor of the ERC-721](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721-constructor-string-string-) contract accepts two parameters. The first parameter is the name of the token, and the second parameter is the symbol. We’ll be naming it `randomTraits` with the symbol `RAT`, but you can name yours whatever you want.

This contract has become an industry standard that is compliant with the [ERC-721 Non-Fungible Token Standard](https://eips.ethereum.org/EIPS/eip-721). Don’t be afraid to read through their documentation and explore the expansive functionality that this token standard offers.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
 
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
 
contract randomTraits is ERC721 {
 
  constructor(address supraSC) ERC721("randomTraits", "RAT") {
  }
 
}
```

Since we’ll be minting tokens in order by ID, we’ll need a way to keep track of which token ID is next in line to be minted. Rather than simply incrementing any uint/int that we declare within our contract, we'll follow the industry standard of using [OpenZeppelin’s Counters](https://docs.openzeppelin.com/contracts/4.x/api/utils#Counters) library. Import [OpenZeppelin’s Counters](https://docs.openzeppelin.com/contracts/4.x/api/utils#Counters) library and then include it with the [Using For directive](https://docs.soliditylang.org/en/v0.8.17/contracts.html?highlight=using#using-for): `using Counters for Counters.Counter;`. While we’re at it, we’ll also go ahead and instantiate a new counter with the name `_tokenIdCounter`.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
 
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
 
contract randomTraits is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;
 
    constructor(address supraSC) ERC721("randomTraits", "RAT") {
 
    }
 
}

```

The final step for our dependencies is declaring the [ISupraRouter](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/v2-guide.md) [interface](https://docs.soliditylang.org/en/v0.8.17/contracts.html#interfaces) that will allow us to interact with the Supra VRF service. We'll declare an address variable named `supraAddr` that will hold the [contract address of the SupraRouter contract](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/networks.md), which will be passed through our contract constructor upon deployment. We’ll need this later to make our request for a random number. More information on available chains and the relative contract addresses are available in our [docs](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/networks.md). If you haven’t already, now is a great time to take a look at the [dVRF litepaper](https://supra.com/news/supra-distributed-vrf/).

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
 
interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}
 
contract randomTraits is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;
    address supraAddr;
 
    constructor(address supraSC) ERC721("randomTraits", "RAT") {
        supraAddr = supraSC;
 
    }
 
}
```

### Variables

Great! With that out of the way, it's time to declare all of the variables that we need. Let’s say that we want each character (token) to have a “level,” “health,” and “strength” trait assigned to it. We’ll need a way to conveniently store these values and a way to map each token to these values. This is a perfect use case for a [struct](https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=structs#structs) and a [mapping](https://docs.soliditylang.org/en/v0.8.17/style-guide.html?highlight=mapping#mappings).

We’ll declare a struct with the name `characterStats`. Within the [struct](https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=structs#structs), let’s add three (3) `uint8` variables with the names `level`, `health`, and `strength`. Remember, these will be holding the randomly generated values from the Supra VRF.

```solidity
    struct playerStats{
        uint8 level;
        uint8 health;
        uint8 strength;
    }
```

For the [mapping](https://docs.soliditylang.org/en/v0.8.17/style-guide.html?highlight=mapping#mappings), what better value to use than the token ID! As per the [ERC-721 Non-Fungible Token Standard](https://eips.ethereum.org/EIPS/eip-721), the token ID is a `uint256` value. As such, create a mapping of `uint256` to the playerStats [struct](https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=structs#structs) with the name `tokenIdToStats`.

```solidity
mapping (uint256 => playerStats ) tokenIdToStats;
```

Now, we have one more important variable to create before we move on to our functions. When a user calls our `mint()` function to mint a token, a request to the Supra VRF service is made.

Due to the request and response model of VRF services, we'll need a way to keep track of which responses belong to which requests. That is, we need a way to know which random numbers belong to which users. Since the user has called the `mint()` function using their wallet, we can easily access it through the `msg.sender` [global variable](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html) and store it for later. That means we’ll know which user address is responsible for the request.

However, the response won’t be coming from that same user’s address. It will be coming from the Supra VRF service’s address. This begs the question of what to use as an identifier when we get our response.

Luckily, the Supra VRF developer guide provides us with all the relevant information. When we make a request using the [interface](https://docs.soliditylang.org/en/v0.8.17/contracts.html#interfaces) and contract address that we previously discussed, we'll be returned a value named `nonce` that is of type `uint256`. The Supra VRF will then provide this nonce to our callback function when it delivers the randomly generated number.

As such, we can map this nonce value to the address of the original `mint()` caller to keep track of which responses belong to which requests. Remember that this means which random numbers belong to which users/requests.

To do this, create a [mapping](https://docs.soliditylang.org/en/v0.8.17/style-guide.html?highlight=mapping#mappings) of type `uint256` to address with the name `nonceToMinterAddress`.

```solidity
mapping(uint256 => address) nonceToMinterAddress;
```

Up until this point, your code should look like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
 
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
 
interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}
 
contract randomTraits is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;
    address supraAddr;
 
    struct playerStats{
        uint8 level;
        uint8 health;
        uint8 strength;
    }
 
    mapping(uint256 => address) nonceToMinterAddress;
    mapping (uint256 => playerStats ) tokenIdToStats;
 
    constructor(address supraSC) ERC721("randomTraits", "RAT") {
        supraAddr = supraSC;
 
    }
 
}

```

### Functions

We’re on the home stretch now. Our contract will consist of four (4) functions to make the request, receive the response (callback), view the token stats/traits, and "level up" the token stats.

#### Mint() - Request function

The first function will be our `mint()` function. For our situation, this function won’t actually hold any of the minting logic from the [ERC-721 standard](https://eips.ethereum.org/EIPS/eip-721). Instead, its entire purpose is to make the request for a random number from the Supra VRF and map the returned `nonce` to the requesting user's `address`.

As per the Supra VRF docs and interface, we initiate the request by pairing the address stored within `supraAddr` and our declared [interface](https://docs.soliditylang.org/en/v0.8.17/contracts.html#interfaces) to call the `generateRequest()` function of the Supra Router contract. Our request must consist of our callback function’s signature (`_functionSig`), the amount of random numbers that are requested (`_rngCount`), the number of confirmations needed before the Supra VRF can generate a number (`_numConfirmations` Minimum of 1 - Maximum of 20) , and an optional client-seed parameter that provides additional unpredictability (`_clientSeed`). An additional parameter (`_clientWalletAddress`) is used in dVRF v2 whitelisting. This is the client wallet address that is responsible for the whitelisting of your contract and will be used to deduct the appropriate funds from the deposit contract. Please refer to the dVRF developer guide v2 for more information on this step. We’ll then map the returned `nonce` value to the `msg.sender` value, as we previously discussed.

You’ll see that our parameters passed with the request are as follows:

`_functionSig: "finishMint(uint256,uint256[])"`\
`_rngCount: 1`\
`_numConfirmations: 1`\
`_clientSeed: 123`\
`_clientWalletAddress: msg.sender`

```solidity
    function mint() public {
        uint256 nonce =  ISupraRouter(supraAddr).generateRequest("finishMint(uint256,uint256[])", 1, 0, 123, msg.sender);
        nonceToMinterAddress[nonce] = msg.sender;
    }
```

#### finishMint() - Callback function

As you may have guessed, the callback function that we'll now define will is `finishMint()`. This is where we'll handle the response from the Supra VRF.

As per the Supra VRF docs, callback functions must accept two parameters of `uint256` and `uint256[]`. The first parameter is the `nonce` that was returned to us when the original request was made. The second parameter is an array (`uint256[]`) consisting of the random values that the Supra VRF has generated for us. We’ll call this `rngList`.

The first step in our callback function is to check to make sure that the calling address is that of the Supra VRF/Router contract. This ensures that _only_ Supra VRF can return values to your callback function.

```solidity
require(msg.sender == supraAddr, "only supra router can call this function");
```

Now, it's important to note that our token doesn’t actually exist yet, as we haven’t called the [`_safeMint()`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_safeMint-address-uint256-bytes-) function belonging to [ERC-721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721).

The [`_safeMint(address _to, uint256 tokenId)`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_safeMint-address-uint256-bytes-) function accepts two parameters. The first parameter is the address of the user to whom the token belongs/should be minted for. If you recall, we stored this address within the `nonceToMinterAddress` mapping.

The second is the `tokenId` that is next in line to be minted. Luckily, our trusty [counter](https://docs.openzeppelin.com/contracts/4.x/api/utils#Counters) has been keeping track for us. We’ll grab that value and then [increment](https://docs.openzeppelin.com/contracts/4.x/api/utils#Counters-increment-struct-Counters-Counter-) it to keep track of the token IDs that have been minted.

We'll then pass these two parameters through the `_safeMint()` function.

```solidity
uint256 tokenId = _tokenIdCounter.current();
_tokenIdCounter.increment();
_safeMint(nonceToMinterAddress[nonce], tokenId);
```

Next, we are going to use the randomly generated number to get a number between the range of 0-99 for each trait that we need to set within the `playerStats`. Luckily, we can use a single random number from the Supra VRF to generate multiple numbers within the range of 0-99.

To do this, we use a combination of modulus and division. Alternatively, we could also request 3 random numbers by changing our `_rngCount` value in the initial request and then assign the returned values accordingly.

```solidity
 tokenIdToStats[tokenId].level = uint8((rngList[0] % 100));
 tokenIdToStats[tokenId].health = uint8((rngList[0] % 10000)/100);
 tokenIdToStats[tokenId].strength = uint8((rngList[0] % 1000000)/10000);
```

Here's the code for the complete callback function:

```solidity
function finishMint(uint256 nonce, uint256[] calldata rngList) external {
        require(msg.sender == supraAddr, "only supra router can call this function");
 
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
 
        _safeMint(nonceToMinterAddress[nonce], tokenId);
 
        tokenIdToStats[tokenId].level = uint8((rngList[0] % 100));
        tokenIdToStats[tokenId].health = uint8((rngList[0] % 10000)/100);
        tokenIdToStats[tokenId].strength = uint8((rngList[0] % 1000000)/10000);
    }

```

#### getTokenStats() - View function to return token stats/trait values

The final step is to add our third and final function `getTokenStats()` , which will return the trait values for the passed token ID.

```solidity
function getTokenStats(uint tokenId) external view returns (uint, uint, uint){
    return (tokenIdToStats[tokenId].level, tokenIdToStats[tokenId].health, tokenIdToStats[tokenId].strength);
  }

```

#### levelUpToken() - Function to increase token stats for "leveling up"

This function will increase the level of the passed token ID by 1, health by 10, and strength by 10.\
\
In our example, the only condition we have added is that the token Id must exist. We do this by using the [`_exists(uint tokenId)`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_exists-uint256-) function included within the [ERC-721 contract](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_exists-uint256-).\
\
\&#xNAN;_Note: It would be best practice to apply some form of_ [_access control_](https://docs.openzeppelin.com/contracts/2.x/access-control) _to restrict who can call this function. As there is no_ [_access control_](https://docs.openzeppelin.com/contracts/2.x/access-control) _added to this function, any user could call this function to increase their token stats as much as they desired._

```solidity
  function levelUpToken(uint tokenId) external{
    require(_exists(tokenId), 'Token does not exist.');
    tokenIdToStats[tokenId].level = tokenIdToStats[tokenId].level + 1;
    tokenIdToStats[tokenId].health = tokenIdToStats[tokenId].health + 10;
    tokenIdToStats[tokenId].strength = tokenIdToStats[tokenId].strength + 10;
  }
```

## Final Code

{% code lineNumbers="true" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
 
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
 
interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}
 
contract randomTraits is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;
    address supraAddr;
 
    struct playerStats{
        uint8 level;
        uint8 health;
        uint8 strength;
    }
 
    mapping(uint256 => address) nonceToMinterAddress;
    mapping (uint256 => playerStats ) tokenIdToStats;
 
    constructor(address supraSC) ERC721("randomTraits", "RAT") {
        supraAddr = supraSC;
 
    }
 
    function mint() public {
        uint256 nonce =  ISupraRouter(supraAddr).generateRequest("finishMint(uint256,uint256[])", 1, 1, 123, msg.sender);
        nonceToMinterAddress[nonce] = msg.sender;
    }
 
    function finishMint(uint256 nonce, uint256[] calldata rngList) external {
        require(msg.sender == supraAddr, "only supra router can call this function");
 
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _safeMint(nonceToMinterAddress[nonce], tokenId);
 
        tokenIdToStats[tokenId].level = uint8((rngList[0] % 100));
        tokenIdToStats[tokenId].health = uint8((rngList[0] % 10000)/100);
        tokenIdToStats[tokenId].strength = uint8((rngList[0] % 1000000)/10000);
    }
 
    function getTokenStats(uint tokenId) external view returns (uint, uint, uint){
        return (tokenIdToStats[tokenId].level, tokenIdToStats[tokenId].health, tokenIdToStats[tokenId].strength);
    }
    
    function levelUpToken(uint tokenId) external{
        require(_exists(tokenId), 'Token does not exist.');
        tokenIdToStats[tokenId].level = tokenIdToStats[tokenId].level + 1;
        tokenIdToStats[tokenId].health = tokenIdToStats[tokenId].health + 10;
        tokenIdToStats[tokenId].strength = tokenIdToStats[tokenId].strength + 10;
    }
 
}

```
{% endcode %}

_Note: in this guide, the token isn't officially minted/created until the callback function `finishMint()` is hit. Alternatively, you could move the minting logic into the request function to ensure that the token is generated prior to the callback being received._

## Conclusion

A request creates a response, which creates a world of opportunity.

With just a little bit of time, you were able to create the foundation for your own NFT project that utilizes the Supra VRF to randomize NFT trait values. In one of our next guides, we’ll show you how to generate a JSON metadata file with the randomized traits that we just stored on-chain in our NFT smart contract to set the token URI.

In the meantime, I need a coffee…
