# Other Functions

Beyond requesting random numbers, Supra dVRF provides additional functions for managing your account, monitoring balances, and configuring contract settings. These utility functions help you maintain your VRF integration and optimize your random number generation workflow.

### Interact with deposit contract - EVM networks

The simplest way to interact with the deposit contract is through Remix IDE.

#### Setup in Remix IDE

1. Go to Remix IDE & create a file with name `IDepositContract.sol`
2. Paste the following interface code in the file:

{% code overflow="wrap" %}
```solidity
interface IDepositContract {
    function depositFundClient() external payable;
    function addContractToWhitelist(address _contractAddress, uint128 _callbackGasPrice, uint128 _callbackGasLimit) external;
    function removeContractFromWhitelist(address _contractAddress) external;
    function withdrawFundClient(uint128 _amount) external;
    function updateMaxGasPrice(uint128 _maxGasPrice) external;
    function updateMaxGasLimit(uint128 _maxGasLimit) external;
    function updateCallbackGasPrice(address _contractAddress, uint128 _callbackGasPrice) external;
    function updateCallbackGasLimit(address _contractAddress, uint128 _callbackGasLimit) external;
    function checkClientFund(address _clientAddress) external view returns (uint128);
    function checkMinBalanceClient(address _clientAddress) external view returns (uint128);
    function countTotalWhitelistedContractByClient(address _clientAddress) external view returns (uint256);
    function getSubscriptionInfoByClient(address _clientAddress) external view returns (uint64, bool);
    function isMinimumBalanceReached(address _clientAddress) external view returns (bool);
    function listAllWhitelistedContractByClient(address _clientAddress) external view returns (address[] memory);
}
```
{% endcode %}

3. Navigate to the "Deploy & run Transactions" tab in Remix
4. Paste the Deposit Contract Address into the text box beside the "At Address" button & press the At Address button
5. You will find the instance for the Deposit Contract created, which you can use to interact with the contract features

### Essential Functions

<table><thead><tr><th width="148.31640625">Function</th><th>Purpose</th><th width="260.984375">Parameters</th><th width="100.92578125">Returns</th><th>Notes</th></tr></thead><tbody><tr><td><strong>Contract Management</strong></td><td></td><td></td><td></td><td></td></tr><tr><td><code>addContractToWhitelist</code></td><td>Whitelists your contract for requesting random numbers</td><td><code>_contractAddress</code>: Contract address (not EOA)<br><code>_callbackGasPrice</code>: Max gas price for callbacks (≤ maxGasPrice)<br><code>_callbackGasLimit</code>: Max gas limit (≤ maxGasLimit)</td><td>-</td><td>Call after deploying your requester contract</td></tr><tr><td><code>removeContractFromWhitelist</code></td><td>Removes contract from whitelist</td><td><code>_contractAddress</code>: Whitelisted contract address to remove</td><td>-</td><td>Use when contract no longer needed</td></tr><tr><td><strong>Fund Management</strong></td><td></td><td></td><td></td><td></td></tr><tr><td><code>depositFundClient</code></td><td> Deposits funds for callback transactions</td><td>None (payable function)</td><td>-</td><td>Must call before requesting random numbers. Keep balance above minimum</td></tr><tr><td><code>withdrawFundClient</code></td><td>Withdraws funds from account</td><td><code>_amount</code>: Amount to withdraw (≤ deposited balance)</td><td>-</td><td>Cannot exceed available balance</td></tr><tr><td><strong>Gas Configuration</strong></td><td></td><td></td><td></td><td></td></tr><tr><td><code>updateMaxGasPrice</code></td><td>Updates maximum gas price for callbacks</td><td><code>_maxGasPrice</code>: New max gas price (> 0)</td><td>-</td><td>Also updates minimum balance requirement</td></tr><tr><td><code>updateMaxGasLimit</code></td><td>Updates maximum gas limit for contracts</td><td><code>_maxGasLimit</code>: New max gas limit (> 0)</td><td>-</td><td>Also updates minimum balance requirement</td></tr><tr><td><code>updateCallbackGasPrice</code></td><td>Updates gas price for specific contract</td><td><code>_contractAddress</code>: Whitelisted contract<br><code>_callbackGasPrice</code>: New gas price (> 0)</td><td>-</td><td>Contract-specific setting</td></tr><tr><td><code>updateCallbackGasLimit</code></td><td>Updates gas limit for specific contract</td><td><code>_contractAddress</code>: Whitelisted contract<br><code>_callbackGasLimit</code>: New gas limit (> 0)</td><td>-</td><td>Contract-specific setting</td></tr><tr><td><strong>Balance Information</strong></td><td></td><td></td><td></td><td></td></tr><tr><td><code>checkClientFund</code></td><td>Returns total available balance</td><td><code>_clientAddress</code>: Whitelisted wallet address (EOA only)</td><td><code>uint128</code></td><td>Your deposited funds</td></tr><tr><td><code>checkMinBalanceClient</code></td><td>Returns minimum required balance</td><td><code>_clientAddress</code>: Whitelisted wallet address</td><td><code>uint128</code></td><td>Minimum balance threshold</td></tr><tr><td><code>isMinimumBalanceReached</code></td><td>Checks if balance is at/below minimum</td><td><code>_clientAddress</code>: Whitelisted wallet address</td><td><code>bool</code></td><td><code>true</code> if balance ≤ minimum, <code>false</code> otherwise</td></tr><tr><td><strong>Contract Information</strong></td><td></td><td></td><td></td><td></td></tr><tr><td><code>countTotalWhitelistedContractByClient</code></td><td>Returns number of whitelisted contracts</td><td><code>_clientAddress</code>: Whitelisted wallet address</td><td><code>uint256</code></td><td>Total contract count</td></tr><tr><td><code>listAllWhitelistedContractByClient</code></td><td>Returns all whitelisted contract addresses</td><td><code>_clientAddress</code>: Whitelisted wallet address</td><td><code>address[]</code></td><td>Array of contract addresses</td></tr><tr><td><code>getSubscriptionInfoByClient</code></td><td>Returns subscription details</td><td><code>_clientAddress</code>: Whitelisted wallet address</td><td><code>uint64, bool</code></td><td>Subscription end timestamp, SNAP program status</td></tr></tbody></table>

\
\
\
**Important Notes**

* Supra runs monitoring scripts that will alert you when a fund refill is required
* Your funds are used to pay transaction fees for VRF response callbacks
* Always ensure your total balance remains well above the minimum requirement to avoid request failures
* Contract addresses and EOA addresses serve different purposes - use the correct type for each function

These functions provide comprehensive control over your Supra dVRF integration, allowing you to manage funds, monitor usage, and configure your setup according to your application's needs.
