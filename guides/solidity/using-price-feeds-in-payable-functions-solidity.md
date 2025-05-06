---
description: >-
  This guide uses our S-Value Price Feeds to validate that the amount of ether
  sent to a payable function is greater than or equal to a defined dollar value.
  EVM - Solidity
hidden: true
---

# Using Price Feeds in Payable Functions

{% hint style="info" %}
This guide is for the PUSH model. This guide assumes that you have reviewed the [Price Feed integration](https://github.com/nolan-supra/docs-test/blob/guides/dev-tutorials/solidity/broken-reference/README.md) documentation.
{% endhint %}

## Intro

One common problem that developers may encounter is the need to take payments in the form of a specific/correct amount of ether based on the going exchange rate with a given asset.

Imagine this: you're creating an NFT project and want your minting price to be exactly $100. That is, whenever a user mints your token, they must also send the equivalent amount of USD in ETH based on the current exchange rate.

If the going rate of ETH is $2,000 and the set minting price is $100, we would expect the user to send 0.05 Ether ($100/$2,000 = 0.05) in addition to the gas cost of calling the function. If the user sent too little, the transaction would revert.

This is where the S-Value price feeds come into play. In this guide, we will touch on the following:

| <ul><li>Interfaces</li></ul>                       | <ul><li>Modifiers</li></ul>         |
| -------------------------------------------------- | ----------------------------------- |
| <ul><li>SupraOracles S-Value Price Feeds</li></ul> | <ul><li>Payable Functions</li></ul> |

_Note: For the sake of this guide, we won't be implementing the minting function/ERC-721 token. We'll focus solely on the use of price feeds to validate the amount sent to our payable function._

## Time to Code

Our smart contract will primarily consist of the interface to interact with the Supra `ISupraSValueFeed` contract, modifiers for access control/verifying the correct amount of ether, and two functions for minting/accepting payment and withdrawing any amount of ether stored in the contract.

### Contract and Declaration

First, we'll create a new contract with an empty constructor. Don't forget to inherit the Ownable contract and include it within the constructor as well. For the Ownable contract, we will initialize the owner by passing the `msg.sender` value. This will set the default owner as the deploying address. Then, we'll import the `ISupraSValueFeed` interface from the [Price Feeds integration guide](https://github.com/nolan-supra/docs-test/blob/guides/dev-tutorials/solidity/broken-reference/README.md) and the `Ownable` contract from OpenZeppelin.

```solidity
pragma solidity ^0.8.20;
 
import "./ISupraSValueFeed.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract PayableEtherExample is Ownable {
 
    constructor() Ownable (msg.sender){
    }

    }

}
```

Now, we will declare two variables. The first is `mintPrice()` which will be used to store the minting price of our minting function. The second is the `sValueFeed` which will be used to create an instance of the `ISupraSValueFeed` contract using the interface that we previously defined. We will use `sValueFeed` to retrieve the price of ETH/USDT later.

```solidity
pragma solidity ^0.8.20;
 
import "./ISupraSValueFeed.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
 
contract PayableEtherExample {

    uint mintPrice;
    ISupraSValueFeed sValueFeed;
 
    constructor() Ownable (msg.sender){
    }


}

```

For our constructor, we will add an address parameter for the address of the SValueFeed and use it to initialize the `ISupraSValueFeed` contract. To find the correct contract address for your network, navigate to this [link](https://supraoracles.com/docs/get-started/networks). You will need this when you go to deploy your contract.\
\
While we're at it, we will initialize the `mintPrice` value as 100. Feel free to modify this to be passed as a construction parameter rather than hard coding it within the constructor!

```solidity
pragma solidity ^0.8.20;
 
import "./ISupraSValueFeed.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
 
contract PayableEtherExample {

    uint mintPrice;
    ISupraSValueFeed sValueFeed;
 
    constructor(address supraAdr) Ownable (msg.sender){
        sValueFeed = ISupraSValueFeed(supraAdr);
        mintPrice = 100;
    }


}


```

For this example, we'll only be working with "ETH\_USDT". Each pair has an assigned index that we will use to get the associated price. ETH\_USDT has an index of 19, but you can find other [market pairs here](https://github.com/nolan-supra/docs-test/blob/guides/data-feeds/data-feeds-index.md). We're now able to obtain the price data for ETH/USDT at any time using the two following line.

```solidity
ISupraSValueFeed.priceFeed memory data = sValueFeed.getSvalue(75);
```

You'll notice that the `getSvalue` function returns a `priceFeed` struct. You can always refer to the interface for more insight. For the sake of this guide, I have included it here.\
\
The struct contains:

* `uint256 round` - The round that this value was updated in.
* `uint256 decimals` - The decimals (points of precision) of the value.
* `uint256 time` - The timestamp of when this value was updated.
* `uint256 price` - The current price of the pair.

### Modifiers and Payable Function

Now, we have a few more things to do. We need to define the [modifier](https://docs.soliditylang.org/en/v0.8.17/contracts.html?highlight=modifier#function-modifiers) that'll be used to validate the amount of Ether sent to the minting function, the `mint()` function itself, a modifier to restrict access to the `withdraw()` function, and the `withdraw()` function itself to withdraw Ether from the contract.

We'll declare the modifier `validAmount()` and use it to check the amount of Ether sent. It's important to note that as Solidity does not fully support floating point numbers, we must first convert our values into [wei](https://solidity-by-example.org/ether-units/) (18 points of precision) in order to make the comparison.

Here is the full code for our `validAmount()` modifier:

```solidity
 //modifier used to validate the amount sent in the transaction
modifier validAmount(){
    
    //pair index for eth or whatever pair you want
    ISupraSValueFeed.priceFeed memory data = sValueFeed.getSvalue(19);

    //match the points of precision
    uint mintPriceAdjusted = mintPrice * (10 ** data.decimals);
    //Increase size to account for division
    uint requiredAmount = (mintPriceAdjusted * (10 ** data.decimals)) / data.price;

    //compare values
    require(msg.value >= requiredAmount, 'Not enough value sent.');
    _;
}
```

Now that our modifier is declared, let's go ahead and define the function that would make use of the modifier. For the sake of this example, we will leave the minting logic empty. However, the important thing to note is the `payable` and `validAmount()` modifiers. This allows for the function to act as a payable function and receive payment while verifying that the amount sent to the function is correct before function execution begins.

```solidity
function mint() external payable validAmount() {
   //mint logic here
}
```

### Withdrawing

Now all we need to do is define a `withdraw()` function so that we're able to retrieve the Ether sent to the contract. Along with this, we're going to want to restrict who can actually call the `withdraw()` function. We can easily do this by using the `onlyOwner` modifier of the inherited OpenZeppelin contract.

```solidity
    function withdraw(address payable _to) external onlyOwner {
        _to.transfer(address(this).balance);
    }
```

## Final Code

Once you've made it this far, your completed contract should look like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

//interface to interact with price feed, refer to documentation https://supraoracles.com/docs/get-started
import "./ISupraSValueFeed.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract priceFeedInPayable is Ownable {
    uint mintPrice;
    ISupraSValueFeed sValueFeed;
 
    constructor(address supraAdr) Ownable (msg.sender) {
        sValueFeed = ISupraSValueFeed(supraAdr);
        mintPrice = 100;
    }

    function updateSupraSvalueFeed(address _newSValueFeed) external  onlyOwner {
        sValueFeed = ISupraSValueFeed(_newSValueFeed);
    }
 
     //modifier used to validate the amount sent in the transaction
    modifier validAmount(){
        
        //pair index for eth or whatever pair you want
        ISupraSValueFeed.priceFeed memory data = sValueFeed.getSvalue(19);

        //msg.value is in WEI - convert values to WEI and compare
        //data.decimals of ETH/USDT is 18
        uint mintPriceAdjusted = mintPrice * (10 ** data.decimals);
        uint requiredAmount = (mintPriceAdjusted * (10 ** data.decimals)) / data.price;

        require(msg.value >= requiredAmount, 'Not enough value sent.');
        _;
    }

     //arbitrary PAYABLE function with the validAmount modifier
    function mint() external payable validAmount() {
        //mint logic here
    }
 
     //withdraw function
    function withdraw(address payable _to) external onlyOwner {
        _to.transfer(address(this).balance);
    }
 
}
```

Note that while the `mint()` doesn't have any code, it will accept Ether as payment because we've set it to payable. So any ether sent to the function will trigger the `validAmount()` modifier. If the amount is valid, the ether gets deposited into the contract balance, which can be withdrawn by the `owner`.

## Conclusion

From here, the world is your oyster. You may even want to add functions that allow you to update or retrieve the minting price (set/get functions) and a function to allow for transfer of ownership. Regardless, you can apply this concept to your minting contract for your ERC-721/ERC-1155 tokens, any dApp, or whatever you have in mind. Happy building!
