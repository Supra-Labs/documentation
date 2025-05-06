---
description: >-
  Create a Solidity contract to store and retrieve data, then set up a
  deployment script to automate deployment on SupraEVM using Foundry.
---

# Write Smart Contract

***

### Create a Smart Contract File

Set up the `SimpleStorage.sol` file in the `src` directory to define a basic **Solidity smart contract** that allows storing and retrieving a `uint256` value on **SupraEVM**.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 public storedData;

    function set(uint256 x) public {
        storedData = x;
    }

    function get() public view returns (uint256) {
        return storedData;
    }
}
```

### Create a Deployment Script

Write a deployment script in the `script` directory (`DeploySimpleStorage.sol`) to automate smart contract deployment on **SupraEVM** using **Foundry’s scripting tools**.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "forge-std/Script.sol";
import "../src/SimpleStorage.sol";

contract DeploySimpleStorage is Script {
    function run() external {
        uint256 privateKey = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(privateKey);
        new SimpleStorage();
        vm.stopBroadcast();
    }
}

```
