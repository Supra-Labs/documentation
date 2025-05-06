---
description: This guide uses our VRF service to create a simple ERC-721 loot box contract.
---

# Simple Loot Box Contract with VRF

{% hint style="info" %}
This guide assumes that you have successfully interacted with the [Supra Deposit Contract](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/v2-guide.md) to whitelist and fund VRF requests for the use of VRF V2. Note that this step is only applicable if the[ version of your destination chain is V2](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/networks.md).
{% endhint %}

## Intro

In the world of gaming, chance encounters are generate excitement and galvanize players to keep coming back for another pleasant surprise. In fact, loot boxes are particularly useful for digital assets as the contents can subsequently be monetized for sale on secondary NFT markets.

Unboxing items with higher rarity scores tend to make those lucky few winners rather happy. Indeed, the most valuable in-game items and user-generated content sell for premium prices and serve to sustain brand hype.

Generally, some in-game event occurs that results in the user being rewarded an unopened container (like discovering a hidden area or defeating a boss). The user would then be able to open the loot box by taking a deliberate action to open the container. Sometimes, games also require a key to open the loot box obtained through gameplay or an in-game purchase.

We can view this as a two-step process. The first step is to log/mint an unopened loot box to the user's wallet when some event occurs. The second is to allow the user to open the loot box and receive a randomized reward using Supra's VRF.

For the first step, some possible approaches are:

* Use a mapping/integer to track the balance of "unopened" boxes.
* Use tokens to represent unopened loot boxes.

For the second step, some possible approaches are:

* Reduce the mapping/integer used to track the balance and then mint a new token when opened.
* Update the token metadata (stored in the contract) when opened to "reveal" prizes.
* Burn the initial unopened loot box and mint a new token for the reward.

For this guide, we are going to represent our unopened loot boxes as ERC-721 tokens and will simply update the stored metadata for the token after it's been opened.

We'll say that a loot box can contain either a shortsword or a longsword represented as the integer value 0 or 1. The sword will also have a random power value assigned to it.

## Full Code

Here's the full code so we can see the big picture before we get into the step-by-step details.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}

contract lootbox is ERC721{
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;

    address supraAddr;
    address supraClientAddress;

    mapping(uint256 => uint256) nonceToLootBox;

    mapping(uint256 => lootBoxData) tokenData;

    struct lootBoxData{
        bool opened;
        int swordType;
        int power;
    }

    constructor(address supraSC) ERC721("supraLootBox", "SLB") {
        supraAddr = supraSC;
        supraClientAddress = msg.sender;
    }

    function mintUnopenedLootBox() public {
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _safeMint(msg.sender, tokenId);
    }

    function openLootBox(uint256 token) public {
        //check to see if this person owns this token
        require(ownerOf(token) == msg.sender, 'You do not own this token.');
        //check to see if the token is closed. False == closed!
        require(tokenData[token].opened == false);

        uint256 nonce =  ISupraRouter(supraAddr).generateRequest("finishLootBox(uint256,uint256[])", 1, 1, 123, supraClientAddress);
        nonceToLootBox[nonce] = token;
    }

    function finishLootBox(uint256 nonce, uint256[] calldata rngList) external{
        require(msg.sender == supraAddr, "only supra router can call this function");
        
        tokenData[nonceToLootBox[nonce]].opened = true;
        tokenData[nonceToLootBox[nonce]].swordType = int((rngList[0] % 2));
        tokenData[nonceToLootBox[nonce]].power = int((rngList[0] % 100));
    }

}
```

## Breakdown

First, we'll need to set up a few things to make this work:

* Import our OpenZeppelin Requirements

```solidity
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
```

* ISupraRouter interface to interact with the Supra VRF

```solidity
interface ISupraRouter {
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, uint256 _clientSeed, address _clientWalletAddress) external returns(uint256);
   function generateRequest(string memory _functionSig , uint8 _rngCount, uint256 _numConfirmations, address _clientWalletAddress) external returns(uint256);
}
```

* Counters from OpenZeppelin to track the next token ID to be minted

```solidity
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;
```

* Variables to handle interactions with the Supra VRF

```solidity
    address supraAddr;
    address supraClientAddress;
    mapping(uint256 => uint256) nonceToLootBox;
```

* Variables to store the data of each token

```solidity
    mapping(uint256 => lootBoxData) tokenData;

    struct lootBoxData{
        bool opened;
        int swordType;
        int power;
    }
```

* Constructor: Set up the ERC-721 portion, store the Supra VRF contract address, and then store your whitelisted wallet address for the Supra VRF subscription. Read more on the [Supra VRF Subscription Model](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/vrf-subscription-model.md) and the [Supra VRF Guide](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/v2-guide.md).

```solidity
    constructor(address supraSC) ERC721("supraLootBox", "SLB") {
        supraAddr = supraSC;
        supraClientAddress = msg.sender;
    }
```

Now that those are set up, we can move on to the minting part:

The first step in the process is to mint the unopened loot box to a user. For this guide, we'll be assuming that the user is able to call the function directly themselves without any restrictions. You will likely find it important for your project to limit/restrict this in some way. For GameFi, maybe you only want this function to be called/triggered by an automated/internal system/function when the user defeats a boss.

Grab the current token ID to be minted from our counter, then mint the token to the calling user.

```solidity
    function mintUnopenedLootBox() public {
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _safeMint(msg.sender, tokenId);
    }
```

The second step in the process is to allow the user to open the loot box. As we are using the Supra VRF to randomize the type of sword being rewarded, this step is broken up into two sub-steps.

The first sub-step is the request portion, where we will request a random number from the Supra VRF service.

Then, pass the token ID to be opened as a parameter, as a user may have multiple unopened loot boxes/tokens in their possession. Next, check to make sure that the user is the owner of this token. Finally, check to make sure that the token hasn't been opened already. Then, generate the request to the Supra VRF. If you're unfamiliar with this step, you can read more on the [Supra VRF here](https://github.com/nolan-supra/docs-test/blob/guides/oracles/dvrf/v2-guide.md).

```solidity
    function openLootBox(uint256 token) public {
        //check to see if this person owns this token
        require(ownerOf(token) == msg.sender, 'You do not own this token.');
        //check to see if the token is closed. False == closed!
        require(tokenData[token].opened == false);

        uint256 nonce =  ISupraRouter(supraAddr).generateRequest("finishLootBox(uint256,uint256[])", 1, 1, 123, supraClientAddress);
        nonceToLootBox[nonce] = token;
    }
```

The second sub-step is the callback portion, where we will process the random number returned to us by the Supra VRF.

Check to make sure that the wallet calling this function is the Supra VRF. Next, set the opened state of the token to true to show that it has been opened. Then, use modulus on the returned random number to determine the type of sword and the power.

```solidity
    function finishLootBox(uint256 nonce, uint256[] calldata rngList) external{
        require(msg.sender == supraAddr, "only supra router can call this function");
        
        tokenData[nonceToLootBox[nonce]].opened = true;
        tokenData[nonceToLootBox[nonce]].swordType = int((rngList[0] % 2));
        tokenData[nonceToLootBox[nonce]].power = int((rngList[0] % 100));
    }
```

## Conclusion

That's it. With some modifications to fit your own requirements, you'll be adding loot boxes to your project in no time. Good luck!
