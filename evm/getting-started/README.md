---
description: Interested in building on Supra's EVM network? You've come to the right place!
hidden: true
---

# Getting Started

{% include "../.gitbook/includes/evm-is-currently-in-testnet....md" %}

Developing on Supra's EVM network is no different than the EVM networks that you may be used to. Using existing development tools within the industry, you can start developing immediately.

Supra MultiVM is a core feature of the Supra Layer-1 blockchain that allows developers to deploy smart contracts written in different programming languages and from various blockchain ecosystems natively on the Supra network.

The MultiVM (Multiple Virtual Machine) support means developers do not need to learn a new language or port their existing code to build on Supra. Instead, they can use the Virtual Machine and programming language they are already familiar with.

Supra’s MultiVM architecture currently supports or plans to support:

* **MoveVM:** For smart contracts written in the Move language (used by Aptos and Sui)
* **EVM:** For Solidity smart contracts from the Ethereum ecosystem
* **SolanaVM (SVM):** Coming soon, for smart contracts written in Rust (used by Solana)

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../overview/network-information.md) page.
{% endhint %}

***

## Development Tools & Workflows

This section provides a step-by-step guide for setting up and using various development environments to build and deploy smart contracts on Supra's EVM-compatible infrastructure.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><mark style="color:red;"><strong>Remix IDE →</strong></mark></td><td>A web-based development environment that allows you to write, compile, deploy, and interact with Solidity smart contracts without needing to install additional dependencies.</td><td><a href="remix-ide/">remix-ide</a></td></tr><tr><td></td><td><mark style="color:red;"><strong>Hardhat →</strong></mark></td><td>A powerful development framework for Ethereum and EVM-compatible chains. It enables local testing, debugging, and deployment of smart contracts with custom scripts.</td><td><a href="hardhat/">hardhat</a></td></tr><tr><td></td><td><mark style="color:red;"><strong>Foundry →</strong></mark></td><td>A fast, Rust-based development toolkit for EVM smart contracts, designed for high-performance testing, fuzzing, and debugging.</td><td><a href="foundry/">foundry</a></td></tr></tbody></table>

## Libraries

JavaScript libraries that allow developers to interact with smart contracts deployed on Supra's EVM. These libraries enable reading blockchain data, sending transactions, and integrating smart contracts into decentralized applications (dApps).

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td><mark style="color:red;"><strong>Web3.js →</strong></mark></td><td>A widely used JavaScript library for interacting with EVM-compatible blockchains. It provides a comprehensive set of tools to connect with Supra’s RPC, send transactions, and interact with smart contracts.</td><td><a href="https://web3js.readthedocs.io/">https://web3js.readthedocs.io/</a></td></tr><tr><td></td><td><mark style="color:red;"><strong>Ethers.js →</strong></mark></td><td>A modern, lightweight, and developer-friendly alternative to Web3.js, designed for secure and efficient smart contract interactions. It is widely used for dApp development due to its intuitive API and TypeScript support.</td><td><a href="https://docs.ethers.org/">https://docs.ethers.org/</a></td></tr></tbody></table>
