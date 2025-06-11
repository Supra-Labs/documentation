# Ethereum to Supra Move Cheatsheet

## High Level Overview

| Feature           | Ethereum                                 | Supra Move                                            |
| ----------------- | ---------------------------------------- | ----------------------------------------------------- |
| Smart Contracts   | Solidity, EVM                            | Move, MoveVM                                          |
| Benefits          | Mature, wide adoption                    | Scalability, low latency, Optimal fees                |
| Transaction Fees  | Variable, can be high                    | Lower and more predictable                            |
| Account Addresses | 160-bit                                  | 256-bit                                               |
| Account Structure | Balance in a single field, uses nonce    | Modules and resources, uses sequence number           |
| Data Storage      | Patricia Merkle Trees                    | Global storage with resources and modules             |
| Storage Mindset   | Contract-based storage                   | Account & Resources centric mindset for code and data |
| Parallelization   | Limited due to shared storage space      | Enhanced parallel execution due to resource model     |
| Type Safety       | Contract types provide basic type safety | Module structs and generics offer robust type safety  |
| Unique Features   | Wide ecosystem, EVM compatibility        | Native VRF, Automation, Oracle price feeds            |

## Comparing Token Standards

| Aspect             | Ethereum/Solidity                                          | Supra Move                                                        |
| ------------------ | ---------------------------------------------------------- | ----------------------------------------------------------------- |
| Token Structure    | Each token is its own contract                             | Every token uses typed Coin with single, reusable contract        |
| Token Standard     | Must conform to standards like ERC20; implementations vary | Uniform interface and implementation for all tokens               |
| Balance Storage    | Balances stored in contract using mapping structure        | Resource-Oriented: Balances stored as resource in user's account  |
| Transfer Mechanism | Tokens can be transferred without receiver's permission    | Tokens require receiver's explicit consent for transfer           |
| Safety             | Depends on implementation quality                          | Resources cannot be arbitrarily created, ensuring token integrity |

## Comparing EVM and Move VM

| Aspect                      | EVM (Ethereum Virtual Machine)                   | Move VM (Supra Move Virtual Machine)                           |
| --------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| Data Storage                | Data stored in smart contract's storage space    | Data stored across smart contracts, user accounts, and objects |
| Parallelization             | Limited parallel execution due to shared storage | Enhanced parallel execution enabled by flexible split storage  |
| VM and Language Integration | Separate layers for EVM and languages (Solidity) | Seamless integration between VM layer and Move language        |
| Critical Network Operations | Complex implementation of network operations     | Critical operations natively implemented in Move               |
| Function Calling            | Dynamic dispatch allows arbitrary contract calls | Static dispatch focuses on security and predictable behavior   |
| Type Safety                 | Contract types provide basic type safety         | Module structs and generics offer robust type safety           |
| Transaction Safety          | Uses nonces for transaction ordering             | Uses sequence numbers for transaction ordering                 |
| Object Accessibility        | Objects bound to smart contract scope            | Guaranteed global accessibility of objects                     |

## Module Structure & Initialization

### Ethereum (Solidity)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyContract {
    address public owner;
    uint256 public value;
    
    constructor(uint256 _initialValue) {
        owner = msg.sender;
        value = _initialValue;
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }
}
```

### Supra Move

```
module my_address::my_contract {
    use supra_framework::signer;
    use supra_framework::account;
    use std::error;
    
    struct ContractData has key {
        owner: address,
        value: u64,
    }
    
    const E_NOT_OWNER: u64 = 1;
    
    // Called once when module is published
    fun init_module(account: &signer) {
        let owner = signer::address_of(account);
        move_to(account, ContractData {
            owner,
            value: 0,
        });
    }
    
    // Manual initialization function
    public entry fun initialize(account: &signer, initial_value: u64) {
        let addr = signer::address_of(account);
        move_to(account, ContractData {
            owner: addr,
            value: initial_value,
        });
    }
}
```

#### Key Differences:

* **Supra Move**: Uses `init_module` for automatic initialization or explicit initialization functions
* **Ethereum**: Uses constructors that run once during deployment
* **Supra Move:** Resources are stored under user accounts, not contract addresses
* **Ethereum**: State stored in contract storage slots

## Functions

### Ethereum (Solidity)

```solidity
contract Functions {
    uint256 private _value;
    
    // Public function (external callable)
    function setValue(uint256 newValue) public {
        _value = newValue;
    }
    
    // View function (read-only)
    function getValue() public view returns (uint256) {
        return _value;
    }
    
    // Pure function (no state access)
    function add(uint256 a, uint256 b) public pure returns (uint256) {
        return a + b;
    }
    
    // Internal function
    function _internalHelper() internal pure returns (uint256) {
        return 42;
    }
    
    // Payable function
    function deposit() public payable {
        // Function can receive Ether
    }
}
```

### Supra Move

```
module my_address::functions {
    use supra_framework::signer;
    
    struct Storage has key {
        value: u64,
    }
    
    // Public entry function (blockchain callable)
    public entry fun set_value(account: &signer, new_value: u64) acquires Storage {
        let addr = signer::address_of(account);
        let storage = borrow_global_mut<Storage>(addr);
        storage.value = new_value;
    }
    
    // View function (read-only)
    #[view]
    public fun get_value(addr: address): u64 acquires Storage {
        borrow_global<Storage>(addr).value
    }
    
    // Public function (can be called by other modules)
    public fun add(a: u64, b: u64): u64 {
        a + b
    }
    
    // Private function (module internal only)
    fun internal_helper(): u64 {
        42
    }
    
    // Function that can receive coins
    public entry fun deposit(account: &signer, amount: u64) {
        // Use coin framework for transfers
        // coin::transfer<SupraCoin>(account, target, amount);
    }
}
```

#### Key Differences:

* **Supra Move**: Uses `#[view]` for read-only functions, public entry for blockchain calls
* **Ethereum**: Uses view, pure, public, external modifiers
* **Supra Move**: acquires keyword declares which resources the function accesses
* **Ethereum**: Automatic state access without declaration

## Basic Types

### Ethereum (Solidity)

```solidity
contract BasicTypes {
    // Integers
    uint8 smallNumber;      // 0 to 255
    uint256 bigNumber;      // 0 to 2^256-1
    int256 signedNumber;    // -2^255 to 2^255-1
    
    // Boolean
    bool isActive;
    
    // Address
    address userAddress;
    
    // Bytes
    bytes32 hash;
    bytes dynamicBytes;
    
    // String
    string text;
    
    // Arrays
    uint256[] dynamicArray;
    uint256[5] fixedArray;
    
    // Mapping
    mapping(address => uint256) balances;
    
    // Struct
    struct User {
        string name;
        uint256 age;
    }
}
```

### Supra Move

```
module my_address::basic_types {
    use std::string::{Self, String};
    use std::vector;
    use aptos_std::table::{Self, Table};
    
    struct BasicTypes has key {
        // Integers (unsigned only)
        small_number: u8,       // 0 to 255
        medium_number: u64,     // 0 to 2^64-1  
        big_number: u128,       // 0 to 2^128-1
        huge_number: u256,      // 0 to 2^256-1
        
        // Boolean
        is_active: bool,
        
        // Address
        user_address: address,
        
        // Vector (dynamic array)
        dynamic_array: vector<u64>,
        
        // String
        text: String,
        
        // Table (like mapping)
        balances: Table<address, u64>,
    }
    
    // Struct
    struct User has store, drop {
        name: String,
        age: u64,
    }
}
```

## Structs and Resources

### Ethereum (Solidity)

```solidity
contract StructsExample {
    struct Token {
        string name;
        uint256 totalSupply;
        mapping(address => uint256) balances;
    }
    
    Token public token;
    
    constructor() {
        token.name = "MyToken";
        token.totalSupply = 1000000;
    }
    
    function transfer(address to, uint256 amount) public {
        require(token.balances[msg.sender] >= amount, "Insufficient balance");
        token.balances[msg.sender] -= amount;
        token.balances[to] += amount;
    }
}
```

### Supra Move

```
module my_address::token {
    use supra_framework::signer;
    use aptos_std::table::{Self, Table};
    use std::string::String;
    use std::error;
    
    // Resource stored under account
    struct Token has key {
        name: String,
        total_supply: u64,
        balances: Table<address, u64>,
    }
    
    const E_INSUFFICIENT_BALANCE: u64 = 1;
    
    public entry fun initialize(account: &signer, name: String, total_supply: u64) {
        move_to(account, Token {
            name,
            total_supply,
            balances: table::new(),
        });
    }
    
    public entry fun transfer(
        account: &signer, 
        to: address, 
        amount: u64
    ) acquires Token {
        let from = signer::address_of(account);
        let token = borrow_global_mut<Token>(@my_address);
        
        let from_balance = table::borrow_mut(&mut token.balances, from);
        assert!(*from_balance >= amount, error::invalid_argument(E_INSUFFICIENT_BALANCE));
        
        *from_balance = *from_balance - amount;
        
        if (table::contains(&token.balances, to)) {
            let to_balance = table::borrow_mut(&mut token.balances, to);
            *to_balance = *to_balance + amount;
        } else {
            table::add(&mut token.balances, to, amount);
        };
    }
}
```

#### Key Differences:

* **Supra Move**: Resources have abilities (`key, store, drop, copy`)
* **Ethereum**: Structs are simple data containers
* **Supra Move**: Resources ensure linear type safety
* **Ethereum**: No built-in protection against double-spending

## Events

### Ethereum (Solidity)

```solidity
contract Events {
    event Transfer(
        address indexed from,
        address indexed to,
        uint256 value
    );
    
    event Approval(
        address indexed owner,
        address indexed spender,
        uint256 value
    );
    
    function transfer(address to, uint256 amount) public {
        // Transfer logic...
        
        emit Transfer(msg.sender, to, amount);
    }
}
```

### Supra Move

```
module my_address::events {
    use supra_framework::event;
    use supra_framework::signer;
    
    #[event]
    struct TransferEvent has drop, store {
        from: address,
        to: address,
        value: u64,
    }
    
    #[event]
    struct ApprovalEvent has drop, store {
        owner: address,
        spender: address,
        value: u64,
    }
    
    public entry fun transfer(account: &signer, to: address, amount: u64) {
        let from = signer::address_of(account);
        
        // Transfer logic...
        
        event::emit(TransferEvent {
            from,
            to,
            value: amount,
        });
    }
}
```

#### Key Differences:

* **Supra Move**: Events are structs with `#[event]` attribute
* **Ethereum**: Events are declared with `event` keyword
* **Supra Move**: Uses `event::emit()` to emit events
* **Ethereum**: Uses `emit` keyword
* **Supra Move**: No indexed parameters concept
* **Ethereum**: Supports indexed parameters for filtering

## Storage & State Management

### Ethereum (Solidity)

```solidity
contract Storage {
    // State variables stored in contract storage
    uint256 private _totalSupply;
    mapping(address => uint256) private _balances;
    
    function updateBalance(address user, uint256 amount) internal {
        _balances[user] = amount;
    }
    
    function getBalance(address user) public view returns (uint256) {
        return _balances[user];
    }
}
```

### Supra Move

```
module my_address::storage {
    use supra_framework::signer;
    use aptos_std::table::{Self, Table};
    
    struct TokenStorage has key {
        total_supply: u64,
        balances: Table<address, u64>,
    }
    
    public entry fun initialize(account: &signer) {
        move_to(account, TokenStorage {
            total_supply: 0,
            balances: table::new(),
        });
    }
    
    fun update_balance(storage: &mut TokenStorage, user: address, amount: u64) {
        if (table::contains(&storage.balances, user)) {
            *table::borrow_mut(&mut storage.balances, user) = amount;
        } else {
            table::add(&mut storage.balances, user, amount);
        };
    }
    
    #[view]
    public fun get_balance(addr: address, user: address): u64 acquires TokenStorage {
        let storage = borrow_global<TokenStorage>(addr);
        if (table::contains(&storage.balances, user)) {
            *table::borrow(&storage.balances, user)
        } else {
            0
        }
    }
}
```

#### Key Differences:

* Supra Move: Resources stored under specific account addresses
* Ethereum: State stored in contract's storage slots
* Supra Move: Must explicitly declare resource access with `acquires`
* Ethereum: Automatic state access

## Testing

### Ethereum (Solidity with Hardhat)

```solidity
const { expect } = require("chai");

describe("Token", function () {
    it("Should transfer tokens correctly", async function () {
        const Token = await ethers.getContractFactory("Token");
        const token = await Token.deploy();
        
        await token.transfer(addr1.address, 100);
        expect(await token.balanceOf(addr1.address)).to.equal(100);
    });
});
```

### Supra Move

```
#[test_only]
module my_address::token_tests {
    use my_address::token;
    use supra_framework::account;
    use std::signer;
    
    #[test(account = @0x123)]
    public entry fun test_transfer(account: signer) {
        let addr = signer::address_of(&account);
        account::create_account_for_test(addr);
        
        token::initialize(&account, string::utf8(b"Test"), 1000);
        token::transfer(&account, @0x456, 100);
        
        assert!(token::get_balance(addr, @0x456) == 100, 1);
    }
}
```

## Deployment

### Ethereum

```sh
# Using Hardhat
npx hardhat compile
npx hardhat run scripts/deploy.js --network mainnet
```

### Supra Move

```powershell
# Compile and publish module
supra move tool publish \
  --package-dir /path/to/project \
  --rpc-url https://rpc-testnet.supra.com
```

## Account Structure & Transaction Model

### Ethereum Account Model

```solidity
// Ethereum accounts have:
// - Address (160-bit)
// - Balance (single ETH balance)
// - Nonce (for transaction ordering)
// - Contract code (for contract accounts)
// - Storage (key-value store)

contract Example {
    mapping(address => uint256) balances;
    
    function transfer(address to, uint256 amount) public {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        balances[to] += amount;
    }
}
```

### Supra Move Account Model

```
// Supra accounts have:
// - Address (256-bit)
// - Sequence number (for transaction ordering)
// - Resources (typed data structures)
// - Modules (code)

module my_address::example {
    use supra_framework::coin;
    use supra_framework::supra_coin::SupraCoin;
    
    // Each account can hold resources directly
    public entry fun transfer(from: &signer, to: address, amount: u64) {
        coin::transfer<SupraCoin>(from, to, amount);
    }
}
```

| **Feature**         | **Ethereum/Solidity**          | **Supra Move**                     |
| ------------------- | ------------------------------ | ---------------------------------- |
| Type System         | Dynamic typing, runtime checks | Static typing, compile-time safety |
| Resource Management | Manual memory management       | Automatic resource lifecycle       |
| Upgrades            | Proxy patterns, complex        | Module upgrades, simpler           |
| State Storage       | Contract storage               | Account resources                  |
| Safety              | Runtime safety                 | Compile-time + runtime safety      |
| Arrays              | Fixed/dynamic arrays           | Vectors                            |
| Mappings            | Built-in mappings              | Table data structure               |
| Events              | Built-in event system          | Struct-based events                |
| Testing             | External frameworks            | Built-in testing                   |
