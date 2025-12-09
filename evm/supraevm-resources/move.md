---
description: >-
  Learn how Move enables secure, resource-oriented smart contracts on the Supra
  L1.
---

# Move

### What is a Smart Contract? <a href="#what-is-a-smart-contract" id="what-is-a-smart-contract"></a>

_A program stored on-chain that executes automatically when conditions are met._

A smart contract is a set of instructions deployed to a blockchain. Once deployed, it runs automatically without third-party intervention. The code defines how it responds to inputs, similar to any traditional program, but it operates in a decentralized and tamper-proof environment.

Because smart contracts have their own on-chain address, they can hold and transfer tokens, track ownership, and perform actions on behalf of users. This makes them essential for building decentralized applications that manage assets transparently.

Smart contracts can also connect to off-chain data through oracles, letting them interact with real-world information like prices, events, or sensor data while maintaining on-chain trust and automation.

### What is Move? <a href="#what-is-move" id="what-is-move"></a>

_A resource-oriented programming language designed for safe and predictable smart contracts._

Move is a next-generation smart contract language built for security, flexibility, and performance. Unlike Solidity, which models everything as numbers and storage slots, Move treats assets as resources, values that can’t be copied or accidentally lost. This design prevents common issues like double-spending and reentrancy, giving developers stronger safety guarantees by default. Originally developed for Diem (Meta’s blockchain project), Move has evolved into a powerful ecosystem language used by several modern blockchains, including Supra, Aptos, and Sui. On Supra, Move ensures each transaction executes with precision and security, making it ideal for both financial and enterprise-grade applications.

### Why Learn Move? <a href="#why-learn-move" id="why-learn-move"></a>

_Because Move gives you fine-grained control and built-in safety for digital assets._

Move introduces a unique resource-based type system that enforces strong ownership rules at the language level. Assets in Move can’t be duplicated or destroyed without explicit permission, meaning developers don’t have to rely solely on external audits or manual safety checks. Learning Move helps you write more secure and predictable code from the start. It’s perfect for developers who want to create high-performance dApps, design custom tokens, or work with complex logic, without sacrificing safety or composability.

### How Does Move Work? <a href="#how-does-move-work" id="how-does-move-work"></a>

_Write modules, compile them, and deploy them directly to the blockchain._

Move organizes code into modules that define data structures and functions. You write your logic in `.move` files, compile them, and deploy them to the chain. Each module becomes part of the blockchain state and can be imported or called by other contracts.

When executed, the Move Virtual Machine (MoveVM) ensures every transaction follows strict type and resource rules. This guarantees consistent results and prevents unintended asset loss or unauthorized access.

### What Can You Build With Move? <a href="#what-can-you-build-with-move" id="what-can-you-build-with-move"></a>

_Applications built with security and scalability at their core._

* Tokens and assets: Create custom coins or NFTs using Move’s resource types.
* Financial dApps: Build lending, staking, or payment systems with predictable logic.
* DAOs and governance tools: Design secure on-chain voting and proposal systems.
* Game logic: Build games that require strong ownership guarantees for assets.
