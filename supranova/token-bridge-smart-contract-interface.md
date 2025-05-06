# Token Bridge Smart Contract Interface

SupraNova’s Token Bridge contract is a service layer contract integrated within Supranova. It exposes key functions that allow users and developers to interact with the bridge directly. The functions are as follows:

* `sendNative()`: Locks native ETH on Ethereum and emits a bridge event for Supra to process.
* `sendTokens(address tokenAddress, uint256 amount)`: Locks an approved ERC20 token for cross-chain bridging.
* `calculateTokenBridgeFee(address token, uint256 amount)`: Returns the service fee applicable for a given asset and amount.
* `isRegistered(address tokenAddress)`: Checks if a token is approved for bridging.
* `isChainIdRegistered(uint256 chainId)`: Checks if a destination chain is supported.

These methods provide a flexible, decentralized interface for both end-users and cross-chain applications interacting with SupraNova.

\
\
