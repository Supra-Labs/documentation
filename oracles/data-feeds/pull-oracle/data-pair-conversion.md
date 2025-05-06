# Data Pair Conversion

The list of data pairs offered by Supra is getting longer every day, but sometimes you might not find the exact pair that you have been looking for. In such instances, the data pair conversion functionality will come in handy.\
\
The data pair conversion process requires just two simple steps:

\
**Step 1:** Request for the bytes proof of two input pairs you would be using to derive a new pair via Web2 and verify the prices with the Supra Pull contract.\
**Step 2**: Request for a data pair conversion by interacting with the Supra Storage Contract.

So, let's demonstrate how it works with an example case.

{% tabs %}
{% tab title="Solidity (EVM)" %}
**Step 1 : Inherit or copy these interfaces into the contract**

<pre class="language-solidity"><code class="lang-solidity">
interface ISupraSValueFeed {

    struct derivedData{
        int256 roundDifference;
        uint256 derivedPrice;
<strong>        uint256 decimals;
</strong>    }

    function getDerivedSvalue(uint256 pair_id_1,uint256 pair_id_2,uint256 operation)
        external
        view
        returns (derivedData memory);

}
</code></pre>



**Step 2 :: Interface with the Supra Pull Contract and Storage Contract**

```solidity
contract MockOracleClient is Ownable {
    // The oracle pull contract address
    ISupraOraclePull public supra_pull;
    // The oracle storage contract address
    ISupraSValueFeed public supra_storage;


    // Event emitted when a pair price is received
    event PairPrice(uint256 pair, uint256 price, uint256 decimals);

    constructor(ISupraOraclePull pull_, ISupraSValueFeed storage_) {
        supra_pull = pull_;
        supra_storage = storage_; // Supra storage contract address
    }
}


```

**Step 3 :: Add this function to fetch the derived price pair**

{% code overflow="wrap" %}
```solidity
function GetDerivedPairPrice(bytes calldata _bytesProof, uint256 pair_id_1,uint256 pair_id_2,uint256 operation) external                 
    {
        supra_pull.verifyOracleProof(_bytesProof);
// derivedData is the structure for derived price and dp stores the derived price data
        ISupraSValueFeed.derivedData memory dp = ISupraSValueFeed(supra_storage).getDerivedSvalue(pair_id_1,pair_id_2,operation);
    }

```
{% endcode %}

**Step 4 :: (Recommended) Update the Supra Pull and Storage Contracts**

```solidity
function updatePullAddress(ISupraOraclePull oracle_) 
    external 
    onlyOwner {
        supra_pull = oracle_;
    }


function updateStorageAddress(ISupraSValueFeed storage_) 
    external 
    onlyOwner {
        supra_storage = storage_;
    }

```

\
Below is an example implementation on how your contract would look like:

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.19;

import "@openzeppelin/contracts/access/Ownable.sol";

interface ISupraOraclePull {
  
// Verified price data
   struct PriceData {
       // List of pairs
       uint256[] pairs;
       // List of prices
       // prices[i] is the price of pairs[i]
       uint256[] prices;
       // List of decimals
       // decimals[i] is the decimals of pairs[i]
       uint256[] decimals;
   }


   function verifyOracleProof(bytes calldata _bytesProof)
   external
   returns (PriceData memory);
}



interface ISupraSValueFeed {

struct derivedData{
       int256 roundDifference;
       uint256 derivedPrice;
       uint256 decimals;
   }


function getDerivedSvalue(uint256 pair_id_1,uint256 pair_id_2,uint256 operation)
       external
       view
       returns (derivedData memory);
}


// Mock contract which can consume oracle pull data
contract MockOracleClient is Ownable {
   /// @notice The oracle contract
   ISupraOraclePull public supra_pull;
   ISupraSValueFeed public supra_storage;
   uint256 public dPrice;
   uint256 public dDecimal;
   int256 public dRound;


// Event emitted when a pair price is received
   event PairPrice(uint256 pair, uint256 price, uint256 decimals);


   constructor(ISupraOraclePull oracle_, ISupraSValueFeed storage_) {
       supra_pull = oracle_;
       supra_storage = storage_;
   }

// Verify price updates recieved with Supra pull contract
   function GetPairPrice(bytes calldata _bytesProof, uint256 pair) external                
   returns(uint256){
       ISupraOraclePull.PriceData memory prices =
       supra_pull.verifyOracleProof(_bytesProof);
       uint256 price = 0;
       uint256 decimals = 0;
       for (uint256 i = 0; i < prices.pairs.length; i++) {
           if (prices.pairs[i] == pair) {
               price = prices.prices[i];
               decimals = prices.decimals[i];
               break;
           }
       }
       require(price != 0, "Pair not found");
       return price;
   }

//  Get the Derived Pair Price using two pair from oracle data
// Input parameter for "Operation" would be,  Multiplication=0 and Division=1

function GetDerivedPairPrice(bytes calldata _bytesProof, uint256 pair_id_1,uint256 pair_id_2,uint256 operation) external                
   {
       supra_pull.verifyOracleProof(_bytesProof);
       ISupraSValueFeed.derivedData memory dp = ISupraSValueFeed(supra_storage).getDerivedSvalue(pair_id_1,pair_id_2,operation);
       dPrice=dp.derivedPrice;
       dDecimal=dp.decimals;
       dRound=dp.roundDifference;
   }


function updatePullAddress(ISupraOraclePull oracle_)
   external
   onlyOwner {
       supra_pull = oracle_;
   }


function updateStorageAddress(ISupraSValueFeed storage_)
   external
   onlyOwner {
       supra_storage = storage_;
   }
}

```
{% endcode %}
{% endtab %}
{% endtabs %}
