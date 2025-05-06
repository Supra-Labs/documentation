---
description: >-
  This guide uses our S-Value Price Feeds to offer Ether to USDC collateral
  loans.
hidden: true
---

# Ether to USDC Collateral Loan (Part 1)

{% hint style="danger" %}
Please note that this guide uses an outdated version of the PUSH model.

This guide will be updated soon.
{% endhint %}

## Intro

This is a two-part guide to creating a collateral loan contract. The goal of this contract will be to allow users to deposit Ether and take a percentage loan out in USDC. The user will be required to pay off the loan within a designated timeframe and maintain a specific collateral percentage. Otherwise, the user will be liquidated.\
\
In part 1 of this guide, we’re going to create a simple smart contract that allows a user to deposit Ether and withdraw a set percentage of that deposited amount as USDC (the loan). The user will be required to pay off the original loan amount plus fees in order to withdraw their ether collateral. The contract will track the amount of deposited ether that is being used as collateral for the loan as well as the corresponding amount of USDC that can be borrowed at competitive interest rates. Users can always deposit more collateral, borrow additional USDC, or withdraw any leftover Ether.\
\
In part 2, we will improve this smart contract by adding loan durations and functionality for maintaining a specific collateral-to-loan percentage to account for price fluctuations. Additionally, we will implement a function to liquidate loans in the event that the user is unable to pay it off within the designated duration or is unable to maintain a set collateral-to-loan percentage.\
\
For now, let's just focus on building the foundation in part 1.

## Getting Started

Example: the available loan is equal to 80% of the available deposit/collateral with a fee of 10% on top of the loaned amount. If a user deposits 1 Ether at the going exchange rate of $1,577.23, they would be able to withdraw a loan of $1,261.784 in USDC. Including the fee, the user will be required to pay $1,387.9624 to get their initial collateral of 1 Ether back.

| For this guide, we're going to make the following assumptions...                                                                                                                                                                                                                                                            |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <ul><li>One (1) loan per user, but the user can increase/decrease loaned amounts by depositing/withdrawing.</li><li>Users must pay off the loan entirely in one (1) transaction.</li><li>Users can deposit more than once.</li><li>Users can call withdraw as long as they have available/unused ether deposited.</li></ul> |

{% hint style="info" %}
This guide also assumes that you (the developer) have a basic understanding of Solidity. The code is commented on and documented rather than lengthy paragraphs for each section. We will be adding diagrams to assist in understanding the flow of data.

\
If you have any questions, please join our [Discord](https://discord.com/invite/supraoracles) server and obtain the [Developer role here](https://discord.com/channels/850682587273625661/918092801084887040). You'll be able to discuss, offer feedback, and ask questions within the [developer text channel](https://discord.com/channels/850682587273625661/1039323073972813895).
{% endhint %}

## Time to Code

### Dependencies

We'll need a way to convert the deposited Ether to USDC. For this, we're going to need the current price of ETH. This is where [Supra's S-Value Price Feed](https://github.com/nolan-supra/docs-test/blob/guides/data-feeds/README.md) comes into play. We'll use the following interface to interact with the `SupraSValueFeed` smart contract.

{% code title="ISupraSValueFeed.sol" lineNumbers="true" %}
```solidity
// SPDX-License-Identifier: MIT
// SupraOracles Docs: https://supraoracles.com/docs/get-started
pragma solidity ^0.8.13;

interface ISupraSValueFeed {
    function checkPrice(string memory marketPair) external view returns (int256 price, uint256 timestamp);
}

```
{% endcode %}

We'll need a way to interact with the ERC-20 USDC contract to transfer the USDC loan and receive USDC payments from the user. For this, we'll import [IERC.20 from OpenZeppelin](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#IERC20). This interface allows us to interact with contracts that abide by the ERC-20 standard.

For our constructor, we'll pass the addresses associated with the two smart contracts that we wish to interact with (through the imported interfaces) as parameters rather than hard-coding them.

Go ahead and create a new solidity file `collatLoanGuide.sol`, import our two interfaces, and create an empty constructor that accepts two parameters.

{% code title="collatLoanGuide.sol" lineNumbers="true" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;


//OpenZeppelin IERC20.sol :: https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#IERC20
//                        :: Interface for interacting with ERC20 tokens conforming to the ERC20 standard.
//                        :: Used to transfer ERC20 USDC tokens to and from the user for loans and loan payoff.
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";


//SupraOracles ISupraSValueFeed.sol :: https://supraoracles.com/docs/get-started
//                                  :: Interface for interacting with SupraOracles S-Value Price Feed.
//                                  :: Used to obtain the latest price data for the ETH/USDT pair for unit conversion.
import "./ISupraSValueFeed.sol";


//Contract :: Collateral Loan Guide Part 1
//Includes functionality for :: Deposit Collateral, Withdraw Loan, Unit Conversion (WEI <-> USDC), Pay Off Loan
contract collatLoanGuide {


    /*
    *   constructor(address usdcAddress, address supraAddress)
    *               param1  :   address usdcAddress    -   Used to interact with the USDC contract through the IERC20 interface
    *               param2  :   address supraAddress   -   Used to interact with the SupraSValueFeed contract through the ISupraSValueFeed interface
    *               bio     :   Called once upon contract deployment.
    *                           Set USDC/SupraSValueFeed interfaces and values
    */
    constructor(address usdcAddress, address supraAddress){


    }
}

```
{% endcode %}

### Variables and Constructor

We'll need to track a few different variables to bring this all together. This includes variables to interact with the declared interfaces, values for loan calculations, and a way to track the loan details/data for each user.

{% code title="collatLoanGuide.sol" lineNumbers="true" %}
```solidity
    //--- VARIABLES ---\\

    //Instance of IERC20 interface to interact with USDC smart contract.
    IERC20 internal usdc;
    //Instance of ISupraSValueFeed interface to interact with SupraOracles S-Value price feed.
    ISupraSValueFeed internal sValueFeed;
    //Percentage used to calculate available loan.
    uint public loanPercentage;
    //Percentage used to calculate fee.
    uint public feePercentage;
    //Minimum amount of ether to be loaned/deposited.
    uint public minimumLoan;

    //Mapping of users address to loanDetails struct. Holds loan data for each user.
    mapping(address => loanDetails) public loanMap;

    //Struct for loan details
    struct loanDetails {
        uint deposited; //Total amount of ether (WEI) that the user has deposited for their loan.
        uint depositedAvailable; //Amount of deposited ether that is available/unused.
        uint loan;      //Amount of USDC that the user has withdrawn for their loan.
        uint fee;  //Amount of fee that the user must pay in addition to the original loan amount.
    }
    
```
{% endcode %}

Within our constructor, let's bind the contract addresses to the appropriate interface variable and then set the default values for our loan calculations.

{% code title="collatLoanGuide.sol" lineNumbers="true" %}
```solidity
    //--- CONSTRUCTOR ---\\
 
    /*
    *   constructor(address usdcAddress, address supraAddress)
    *               param1  :   address usdcAddress    -   Used to interact with the USDC contract through the IERC20 interface
    *               param2  :   address supraAddress   -   Used to interact with the SupraSValueFeed contract through the ISupraSValueFeed interface
    *               bio     :   Called once upon contract deployment.
    *                           Set USDC/SupraSValueFeed interfaces and values
    */
    constructor(address usdcAddress, address supraAddress){
        //----Interfaces----\\
        //Binds the usdcAddress to the IERC20 interface
        usdc = IERC20(usdcAddress);
        //Binds the supraAddress to the ISupraSValueFeed interface
        sValueFeed = ISupraSValueFeed(supraAddress);

        //----Values----\\
        //Set the loan percenage to 80%.
        loanPercentage = 80;
        //Set the fee percenage to 10%.
        feePercentage = 10;
        //Set the minimum loan/deposit allowed to .1 ether.
        minimumLoan = .1 ether;
    }
```
{% endcode %}

{% hint style="info" %}
Rather than hardcoding the loanPercentage, feePercentage and minimumLoan , you may opt to add constructor parameters to pass/set them upon deployment.
{% endhint %}

### Functions

#### Depositing

The user needs to be able to deposit Ether. We'll only allow deposits greater than or equal to the `minimumLoan` amount and then update the deposited values accordingly.

{% code title="depositEther()" lineNumbers="true" %}
```solidity
/*
    *   depositEther() external payable
    *               bio     :   Payable function that allows a user to deposit ether to the contract.
    *                           Requires the sent ether to be >= the set minimum deposit value.
    *                           User can make multiple deposits.
    */
    function depositEther() external payable{
        //Only allow deposits that are >= the set minimumLoan amount.
        require(msg.value >= minimumLoan, 'Minimum deposit is 0.1 ether.');

        //Update the amount of ether that the user has deposited and has available.
        //The user can make multiple deposits before taking out a loan.
        loanMap[msg.sender].deposited += msg.value;
        loanMap[msg.sender].depositedAvailable += msg.value;
    } 
```
{% endcode %}

The user needs to be able to withdraw their loan. We check to make sure that the user has enough available ether deposited, determine the loan values through the `calculateUsdc(uint loanAmount)` function, check to make sure the smart contract has an adequate balance to collateralize the loan, update the loan details, and then transfer the USDC loan to the user.

#### Borrowing

{% code title="withdrawLoan()" lineNumbers="true" %}
```solidity
    /*
    *   withdrawLoan(uint loanAmount) external
    *               param1  :   uint loanAmount         -   Amount of available ether to take the loan against.
    *               bio     :   Function that allows the user to withdraw USDC (take the loan) dependent on their deposited available amount.
    *                           Requires the withdraw to meet the minimum loan requirement.
    *                           Requires the user to have enough available ether for the requested amount.
    *                           Updates the loan details associated with the user's address
    *                           Transfers the USDC loan to the user's address.
    */
    function withdrawLoan(uint loanAmount) external {
        //Only allow withdraws that meet the minimum loan requirement.
        require(loanMap[msg.sender].depositedAvailable >= minimumLoan, 'Not enough ether deposited.');

        //Only allow withdraws for users that have enough balance available.
        require(loanMap[msg.sender].depositedAvailable >= loanAmount, 'Not enough ETH deposited.');

        //Calls the calculateUsdc(uint loanAmount) function to calculate the loan amount and fee in USDC.
        //Pass the requested amount as the parameter..
        (uint amount, uint fee) = calculateUsdc(loanAmount);

        //Only allow withdraws if there is enough USDC to loan in the contract.
        require(usdc.balanceOf(address(this))>= amount, 'Not enough USDC in contract.');

        //Update the loan details associated with the user's address.
        loanMap[msg.sender].depositedAvailable -= loanAmount;
        loanMap[msg.sender].loan += amount;
        loanMap[msg.sender].fee += fee;

        //Transfer the calculated amount of USDC to the user's address.
        usdc.transfer(msg.sender, amount);
    }
```
{% endcode %}

#### Loan Calculation

We need to convert the Ether (WEI) to USDC for loan calculations. We'll take the passed loan amount (in WEI), get the ETH/USDT price data from [Supra's S-Value feed](https://github.com/nolan-supra/docs-test/blob/guides/data-feeds/README.md), calculate the USD value, convert the value to 6 decimal places to match the USDC decimal count, and then calculate the fee associated with the loan before returning both values.

{% code title="calculateUsdc()" lineNumbers="true" %}
```solidity
 /*
    *   calculateUsdc(uint loanAmount) public view returns (uint, uint)
    *               param1  :   uint loanAmount    -   Amount of ether (in WEI, 18 decimals) that the user has deposited.
    *               returns :   uint amount        -   Amount to be withdrawn (USDC, 6 decimals).
    *                           uint fee           -   Amount of fee to be paid back (USDC, 6 decimals).
    *               bio     :   Function that calculates the available loan and fee based on the passed ether.
    *                           Converts the deposited ether (in WEI) to USDC.
    */
    function calculateUsdc(uint loanAmount) public view returns (uint, uint) {
        //Determine the loan amount by taking the percentage of the requested amount (in WEI, 18 decimals).
        uint amount = loanAmount * loanPercentage / 100;

        //Obtain the latest ETH/USDT price value from the SupraOracles S-Value Price Feed (feed returns 8 decimals).
        (int ethPrice, /*uint timestamp */) = sValueFeed.checkPrice("eth_usdt");
        //Cast the price value to uint and adjust to 18 points of conversion for calculation.
        uint eth = uint(ethPrice) * 10**10;

        //Convert WEI to USD
        amount = eth * amount;
        //Due to multiplication, amount is currently 10**36 (36 decimals). Convert to 6 to match USDC decimal count.
        amount = amount / (10 ** 30);

        //Calculate the fee on the USDC value.
        uint fee = amount * feePercentage / 100 ;

        //Return the loan amount and fee
        return (amount, fee);
    }
```
{% endcode %}

#### Paying Off the Loan

The user needs to be able to pay off their loan. We'll check to make sure the user has a loan to pay off, check to see if they have set the proper "allowance" within the USDC contract to pay off the loan, update the loan details, transfer the USDC from the user to this smart contract, and then finally return the user's Ether collateral to their wallet.

{% hint style="info" %}
As per the [ERC-20 standard](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/), the user must increase the allowance of our smart contract prior to calling this function. You can read more on the [ERC-20 standard here](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/).
{% endhint %}

{% code title="payOff()" lineNumbers="true" %}
```solidity
    /*
    *   payOff() external
    *               bio     :   Function that allows the user to pay off their loan. Users must pay off all at once.
    *                           Requires the user to have a loan balance that needs to be paid off.
    *                           Requires the user to have approved the proper amount of tokens to be transferred.
    *                           Updates loan details before handling token transfer and updates the user's available balance.
    *                           Transfers USDC from the user's wallet to this contract equivalent to the loan amount and fee.
    */
    function payOff() external {
        //Only allow users with a loan to call this function.
        require(loanMap[msg.sender].loan > 0, 'No loan to pay off.');
        
        //Grab the required payment amount (loaned amount + fee)
        uint paymentAmount = loanMap[msg.sender].loan + loanMap[msg.sender].fee;
        
        //Only allow pay off if the user has set the proper allowance.
        require(usdc.allowance(msg.sender, address(this)) >= paymentAmount, 'Not enough allowance.');

        //Update the loan details.
        loanMap[msg.sender].depositedAvailable = loanMap[msg.sender].deposited;
        loanMap[msg.sender].loan = 0;
        loanMap[msg.sender].fee = 0;

        //Transfer the USDC from the user's wallet to this contract.
        usdc.transferFrom(msg.sender, address(this), paymentAmount);
    }
```
{% endcode %}

#### Withdrawing Collateral

The user needs to be able to withdraw available/unused Ether. We'll check to see if the user has enough available/unused Ether, make sure this smart contract has enough Ether in its balance, update the loan details, and then transfer the Ether to the user.

{% code title="withdrawEther()" lineNumbers="true" %}
```solidity
    /*
    *   withdrawEther() external
    *               bio     :   Function that allows the user to withdraw their deposited ether collateral.
    *                           Requires the user to have enough available ether.
    *                           Requires the contract to have enough ether to return to the user.
    *                           Updates loan details before handling the return of user's original ether deposit.
    *                           Transfers the user's original ether deposit from the contract to the user's wallet.   
    */
    function withdrawEther(uint withdrawAmount) external {
        //Only allow user to withdraw if they have enough ether available to be withdrawn.
        require(loanMap[msg.sender].depositedAvailable >= withdrawAmount, 'Not enough ether available to withdraw.');

        //Only allow the withdraw of ether if the contract has enough.
        require(address(this).balance >= withdrawAmount, 'Not enough ether in contract.');

        //Update the loan details.
        loanMap[msg.sender].deposited -= withdrawAmount; 
        loanMap[msg.sender].depositedAvailable -= withdrawAmount;

        //Transfer the originally deposited ether from this contract to the user's wallet.
        payable(msg.sender).transfer(withdrawAmount);
    }
```
{% endcode %}

#### View Loan Details

The users need a way to view their loan details. We'll simply return the loan details/values associated with the calling user through a view function.

{% code title="getLoanDetails()" lineNumbers="true" %}
```solidity
    /*
    *   getLoanDetails() public view returns (bool, uint, uint, uint)
    *               returns :   uint deposited          -   Total Amount of ether that the user has deposited (WEI, 18 decimals).
    *                           uint depositedAvailable -   Amount of ether that the user has not used yet (WEI, 18 decimals).
    *                           uint loan               -   Amount of USDC that the user has withdrawn for their loan (USDC, 6 decimals).
    *                           uint fee                -   Amount of fee that the user must pay in addition to the original loan amount (USDC 6 decimals).
    *               bio     :   Function that returns the loan details for the calling user's address.
    */
    function getLoanDetails() public view returns (uint, uint, uint, uint){
        return (loanMap[msg.sender].deposited, loanMap[msg.sender].depositedAvailable, loanMap[msg.sender].loan, loanMap[msg.sender].fee);
    }
```
{% endcode %}

## Final Code

{% code title="collatLoanGuide.sol" lineNumbers="true" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

//OpenZeppelin IERC20.sol :: https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#IERC20
//                        :: Interface for interacting with ERC20 tokens conforming to the ERC20 standard.
//                        :: Used to transfer ERC20 USDC tokens to and from the user for loans and loan payoff.
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

//SupraOracles ISupraSValueFeed.sol :: https://supraoracles.com/docs/get-started
//                                  :: Interface for interacting with SupraOracles S-Value Price Feed.
//                                  :: Used to obtain the latest price data for the ETH/USDT pair for unit conversion.
import "./ISupraSValueFeed.sol";

//Contract :: Collateral Loan Guide Part 1
//Includes functionality for :: Deposit Collateral, Withdraw Loan, Unit Conversion (WEI <-> USDC), Pay Off Loan
contract collatLoanGuide {

    //--- VARIABLES ---\\

    //Instance of IERC20 interface to interact with USDC smart contract.
    IERC20 internal usdc;
    //Instance of ISupraSValueFeed interface to interact with SupraOracles S-Value price feed.
    ISupraSValueFeed internal sValueFeed;
    //Percentage used to calculate available loan.
    uint public loanPercentage;
    //Percentage used to calculate fee.
    uint public feePercentage;
    //Minimum amount of ether to be loaned/deposited.
    uint public minimumLoan;

    //Mapping of users address to loanDetails struct. Holds loan data for each user.
    mapping(address => loanDetails) public loanMap;

    //Struct for loan details
    struct loanDetails {
        uint deposited; //Total amount of ether (WEI) that the user has deposited for their loan.
        uint depositedAvailable; //Amount of deposited ether that is available/has not been used.
        uint loan;      //Amount of USDC that the user has withdrawn for their loan.
        uint fee;  //Amount of fee that the user must pay in addition to the original loan amount.
    }
    
    //--- CONSTRUCTOR ---\\
 
    /*
    *   constructor(address usdcAddress, address supraAddress)
    *               param1  :   address usdcAddress    -   Used to interact with the USDC contract through the IERC20 interface
    *               param2  :   address supraAddress   -   Used to interact with the SupraSValueFeed contract through the ISupraSValueFeed interface
    *               bio     :   Called once upon contract deployment.
    *                           Set USDC/SupraSValueFeed interfaces and values
    */
    constructor(address usdcAddress, address supraAddress){
        //----Interfaces----\\
        //Binds the usdcAddress to the IERC20 interface
        usdc = IERC20(usdcAddress);
        //Binds the supraAddress to the ISupraSValueFeed interface
        sValueFeed = ISupraSValueFeed(supraAddress);

        //----Values----\\
        //Set the loan percenage to 80%.
        loanPercentage = 80;
        //Set the fee percenage to 10%.
        feePercentage = 10;
        //Set the minimum loan/deposit allowed to .1 ether.
        minimumLoan = .1 ether;
    }

    //--- FUNCTIONS ---\\

    /*
    *   depositEther() external payable
    *               bio     :   Payable function that allows a user to deposit ether to the contract.
    *                           Requires the sent ether to be >= the set minimum deposit value.
    *                           User can make multiple deposits.
    */
    function depositEther() external payable{
        //Only allow deposits that are >= the set minimumLoan amount.
        require(msg.value >= minimumLoan, 'Not enough ether sent.');

        //Update the amount of ether that the user has deposited and has available.
        //By += the deposited amount, we enable the user to make multiple deposits before taking out a loan.
        loanMap[msg.sender].deposited += msg.value;
        loanMap[msg.sender].depositedAvailable += msg.value;
    } 

    /*
    *   withdrawLoan(uint loanAmount) external
    *               param1  :   uint loanAmount         -   Amount of available ether to take the loan against.
    *               bio     :   Function that allows the user to withdraw USDC (take the loan) dependent on their deposited available amount.
    *                           Requires the withdraw to meet the minimum loan requirement.
    *                           Requires the user to have enough available ether for the requested amount.
    *                           Updates the loan details associated with the user's address
    *                           Transfers the USDC loan to the user's address.
    */
    function withdrawLoan(uint loanAmount) external {
        //Only allow withdraws that meet the minimum loan requirement.
        require(loanMap[msg.sender].depositedAvailable >= minimumLoan, 'Not enough ether deposited.');

        //Only allow withdraws for users that have enough balance available.
        require(loanMap[msg.sender].depositedAvailable >= loanAmount, 'Not enough ETH deposited.');

        //Calls the calculateUsdc(uint loanAmount) function to calculate the loan amount and fee in USDC.
        //Pass the requested amount as the parameter..
        (uint amount, uint fee) = calculateUsdc(loanAmount);

        //Only allow withdraws if there is enough USDC to loan in the contract.
        require(usdc.balanceOf(address(this))>= amount, 'Not enough USDC in contract.');

        //Update the loan details associated with the user's address.
        loanMap[msg.sender].depositedAvailable -= loanAmount;
        loanMap[msg.sender].loan += amount;
        loanMap[msg.sender].fee += fee;

        //Transfer the calculated amount of USDC to the user's address.
        usdc.transfer(msg.sender, amount);
    }

    /*
    *   calculateUsdc(uint loanAmount) public view returns (uint, uint)
    *               param1  :   uint loanAmount        -   Amount of ether (in WEI, 18 decimals) that the user has deposited.
    *               returns :   uint amount            -   Amount to be withdrawn (USDC, 6 decimals).
    *                           uint fee               -   Amount of fee to be paid back (USDC, 6 decimals).
    *               bio     :   Function that calculates the available loan and fee based on the passed ether.
    *                           Converts the deposited ether (in WEI) to USDC.
    */
    function calculateUsdc(uint loanAmount) public view returns (uint, uint) {
        //Determine the loan amount by taking the percentage of the requested amount (in WEI, 18 decimals).
        uint amount = loanAmount * loanPercentage / 100;

        //Obtain the latest ETH/USDT price value from the SupraOracles S-Value Price Feed (feed returns 8 decimals).
        (int ethPrice, /*uint timestamp */) = sValueFeed.checkPrice("eth_usdt");
        //Cast the price value to uint and adjust to 18 points of conversion for calculation.
        uint eth = uint(ethPrice) * 10**10;

        //Convert WEI to USD
        amount = eth * amount;
        //Due to multiplication, amount is currently 10**36 (36 decimals). Convert to 6 to match USDC decimal count.
        amount = amount / (10 ** 30);

        //Calculate the fee on the USDC value.
        uint fee = amount * feePercentage / 100 ;

        //Return the loan amount and fee
        return (amount, fee);
    }

    /*
    *   payOff() external
    *               bio     :   Function that allows the user to pay off their loan. Users must pay off all at once.
    *                           Requires the user to have a loan balance that needs to be paid off.
    *                           Requires the user to have approved the proper amount of tokens to be transferred.
    *                           Updates loan details before handling token transfer and updates the user's available balance.
    *                           Transfers USDC from the user's wallet to this contract equivalent to the loan amount and fee.
    */
    function payOff() external {
        //Only allow users with a loan to call this function.
        require(loanMap[msg.sender].loan > 0, 'No loan to pay off.');
        
        //Grab the required payment amount (loaned amount + fee)
        uint paymentAmount = loanMap[msg.sender].loan + loanMap[msg.sender].fee;
        
        //Only allow pay off if the user has set the proper allowance.
        require(usdc.allowance(msg.sender, address(this)) >= paymentAmount, 'Not enough allowance.');

        //Update the loan details.
        loanMap[msg.sender].depositedAvailable = loanMap[msg.sender].deposited;
        loanMap[msg.sender].loan = 0;
        loanMap[msg.sender].fee = 0;

        //Transfer the USDC from the user's wallet to this contract.
        usdc.transferFrom(msg.sender, address(this), paymentAmount);
    }


    /*
    *   withdrawEther() external
    *               bio     :   Function that allows the user to withdraw their deposited ether if they don't want a loan.
    *                           Requires the user to have enough available ether.
    *                           Requires the contract to have enough ether to return to the user.
    *                           Updates loan details before handling the return of user's original ether deposit.
    *                           Transfers the user's original ether deposit from the contract to the user's wallet.   
    */
    function withdrawEther(uint withdrawAmount) external {
        //Only allow user's to withdraw if they have enough ether available to be withdrawn.
        require(loanMap[msg.sender].depositedAvailable >= withdrawAmount, 'Not enough ether available to withdraw.');

        //Only allow the withdraw of ether if the contract has enough.
        require(address(this).balance >= withdrawAmount, 'Not enough ether in contract.');

        //Update the loan details.
        loanMap[msg.sender].deposited -= withdrawAmount; 
        loanMap[msg.sender].depositedAvailable -= withdrawAmount;

        //Transfer the originally deposited ether from this contract to the user's wallet.
        payable(msg.sender).transfer(withdrawAmount);
    }

    /*
    *   getLoanDetails() public view returns (bool, uint, uint, uint)
    *               returns :   uint deposited          -   Total Amount of ether that the user has deposited (WEI, 18 decimals).
    *                           uint depositedAvailable -   Amount of ether that the user has not used yet (WEI, 18 decimals).
    *                           uint loan               -   Amount of USDC that the user has withdrawn for their loan (USDC, 6 decimals).
    *                           uint fee                -   Amount of fee that the user must pay in addition to the original loan amount (USDC 6 decimals).
    *               bio     :   Function that returns the loan details for the calling user's address.
    */
    function getLoanDetails() public view returns (uint, uint, uint, uint){
        return (loanMap[msg.sender].deposited, loanMap[msg.sender].depositedAvailable, loanMap[msg.sender].loan, loanMap[msg.sender].fee);
    }

}
```
{% endcode %}

## Conclusion

In part 2, we'll expand on this foundation and introduce even more functionality. Although it may just be a proof of concept, you took the time to read this guide and expand your knowledge. You're one step closer to achieving your goals.\
\
As the saying goes! A lesson a day keeps the doctor away... I think?...
