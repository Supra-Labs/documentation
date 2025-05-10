# What’s New in dVRF 3.0?

Request Retry Mechanism

Failed callback transactions (due to insufficient gas) are automatically retried every 6 hours, for up to 48 hours. Greatly improves reliability without manual resubmission.\


Custom Callback Gas Settings

Clients can now set: callbackGasPrice and callbackGasLimit. This gives full control over callback execution costs and helps adapt to varying network conditions.\


Request & Response Tracking

Every client now has counters to track: Total RNG requests made and total responses fulfilled.\


Self whitelisting

Clients can whitelist themselves, specifying “maxGasPrice” and “maxGasLimit” which acts as default values for their contracts.\


On-chain Request Validation

A hash of request parameters is stored and validated during callbacks for data integrity: Includes nonce, caller, clientSeed, gas details, etc. It prevents tampering and enhances security.\


Upgradeable & Migration-Friendly Deposit Contract

The Deposit contract is now upgradeable and backward-compatible with older versions. It supports seamless client migration, dynamic gas balance requirements, auto-whitelisting during migration

\
Fund Safety and Locking

Funds are held while a request is pending. Clients cannot withdraw until all requests are resolved, improving safety.

\
Dynamic Minimum Balance Enforcement

“minBalanceLimit” for a client is calculated dynamically as follows:

uint128 minBalanceLimit = minRequests \* \_maxGasPrice \* (\_maxGasLimit +  verificationGasValue);

\
Introduction of “supraMinimumPerTx”

It is the gas used by 'generateRngCallback()' for transaction initialization and calldata. If a transaction fails due to insufficient funds, “supraMinimumPerTx” is deducted from the client.

\
Client removal

removeClientFromWhitelist(address \_clientAddress, bool \_forceRemove) removes the client and their contracts. It transfers back the client fund if no pending requests exist or transfers the client fund to “supraFund” if pending requests exist and “\_forceRemove” is true.
