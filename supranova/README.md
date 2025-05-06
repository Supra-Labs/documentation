# SupraNova Bridge - Bridging ETH to Supra

## Introduction

SupraNova is a cross-chain communication framework developed by Supra. It enables the transfer of assets and messages across blockchains by integrating multiple bridging technologies under a unified architecture.

SupraNova does not represent a single bridge protocol. Instead, it supports two different bridging technologies based on use cases:

* [HyperNova](https://supra.com/documents/Supra-HyperNova-Whitepaper.pdf): A trustless bridge protocol that uses the source chain’s consensus layer for event verification using validator signatures.
* [Hyperloop](https://supra.com/documents/Supra-Hyperloop-Whitepaper.pdf): A fast, multi-signature based game-theoretically secure bridge designed for source chains for which HyperNova style trustless bridging is not applicable or feasible or results in high latency (Layer 2 chains).

By combining these models, SupraNova offers both trust-minimized and high-speed bridging capabilities that can be selected per deployment context.

Our first SupraNova release, focuses on bridging native ETH from Ethereum to the Supra blockchain, using HyperNova. Bridged ETH is minted on Supra as wrapped ETH(wETH) using the FA token standard.

**Future versions of SupraNova will support:**

* Reverse bridging from Supra to Ethereum.
* Asset entry from other EVM chains.
* Application-level services and integrations built on top of validated cross chain messages.

These expansions are made possible without changing the underlying message validation logic powered by HyperNova Core.

\
\
