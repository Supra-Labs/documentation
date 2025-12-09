---
description: Initialize a new Foundry project and prepare it for deployment on SupraEVM.
---

# Start Building

### Step 1: Initialize Your Project

Create and enter a new Foundry project directory:

```
forge init foundry-project
cd foundry-project
```

### Step 2: Configure `foundry.toml`

Add a SupraEVM profile to configure your RPC, chain ID, and private key:

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../../network-information.md) page.
{% endhint %}

```
[profile.supra]
rpc_url = "<SUPRAEVM_RPC_URL>"
private_key = "0xYourPrivateKey"
```

This allows Foundry to deploy and broadcast transactions to SupraEVM.

### Step 3: Create a Smart Contract

Inside `src/`, create **SimpleStorage.sol**:

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

### Step 4: Create a Deployment Script

In `script/DeploySimpleStorage.sol`:

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

### Step 5: Compile Your Contract

```
forge build
```

### Step 6: Deploy to SupraEVM

Use `forge create` to deploy the contract:

```
forge create src/SimpleStorage.sol:SimpleStorage \
  --rpc-url <SUPRAEVM_RPC_URL> \
  --private-key 0xYourPrivateKey \
  --broadcast
```

This broadcasts the deployment transaction to SupraEVM.

### Step 7: Interact Using `cast send`

Call functions on your deployed contract:

**Set a value:**

```
cast send <ContractAddress> "set(uint256)" 42 \
  --rpc-url <SUPRAEVM_RPC_URL> \
  --private-key 0xYourPrivateKey
```

### Step 8: Read the Contract State with `cast call`

This is a gas-free read:

```
cast call <ContractAddress> "get() view returns (uint256)" \
  --rpc-url <SUPRAEVM_RPC_URL> 
```
