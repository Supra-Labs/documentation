# SupraNova Bridge Admin Functionalities

## Overview

To ensure robust governance, security, and operational control, SupraNova employs a multi-signature (MultiSig) administration model. This model distributes administrative privileges across three distinct MultiSig entities, each with defined membership, thresholds, and scoped functionalities.

Administrative actions are executed via on-chain transactions, leveraging MultiSig wallets to enforce consensus-based approvals. This documentation details the composition, thresholds, and capabilities of each admin entity for the SupraNova bridge.

#### **Key Principles**

* All MultiSig actions require a quorum threshold to prevent single-points of failure.
* MultiSig quorum fall-back features are temporary and will be deprecated via governance over time..



## Admin Entities

#### 1. Supra Foundation MultiSig Admin on Ethereum

**Composition:** A 16-member committee comprising core members of the Supra Ecosystem.

**Threshold:** 9 out of 16 signatures are required to execute any admin function.

**Administrative Capabilities:**

* **Delegate Admin Capabilities:** Transfer delegation of these admin privileges to another MultiSig wallet address. This allows for future governance evolution without redeploying contracts.
* **Pause Vault Contract:** Temporarily halt the Ethereum Token Bridge Vault contract, effectively pausing all token bridging operations originating from Ethereum users. This serves as a global emergency stop for bridge inflows.
* **Set Transfer and Vault Caps:** Configure per-transfer amount limits and total vault capacity limits per token. This mitigates risk from large-scale exploits or imbalances by enforcing economic bounds on bridged assets.
* **Emergency Withdrawal (Temporary Feature):** Exclusive to the current unidirectional Ethereum-to-Supra bridge phase. Enables the withdrawal of locked funds from the Ethereum Token Bridge Vault in crisis scenarios.
  * **Process:** Requires two sequential transactions:
    1. **Request Transaction:** Initiates the withdrawal request.
    2. **Execution Transaction:** Performs the actual fund withdrawal from the Vault.
  * **Safeguard:** The execution transaction must be submitted at least 1 hour after the request to allow for community review and intervention.
  * **Deprecation Note:** This capability will be removed upon activation of the reverse (Supra-to-Ethereum) bridge.



#### 2. Operating MultiSig Admin on Ethereum

**Composition:** A 6-member committee consisting of core cross-chain team members from Supra.

**Threshold:** 4 out of 6 signatures required to execute any admin function.

**Administrative Capabilities:**

* **Transfer Admin Privileges:** Delegate or transfer these operational admin privileges to another MultiSig wallet address, enabling seamless team transitions or expansions.
* **Pause/Resume Bridge Operations:**&#x20;
  * Pause or resume the cross-chain message passing (CCM) feature.
  * Pause or resume the Token Bridge smart contracts on Ethereum. This provides granular control over bridge activity without affecting the entire system.
* **Update Configuration Parameters:** Modify bridge-specific settings, such as transaction fees, to adapt to network conditions or economic models.
* **Smart Contract Upgrades:** Perform upgrades to the Token Bridge and Cross-Chain Message Passing (CCM) smart contracts on Ethereum. Upgrades follow a proxy pattern for minimal disruption.
* **Link to Oracle Price Feeds:** Integrate or update links to oracle-provided price feed contracts on Ethereum, ensuring accurate asset valuations for bridging operations.

**Cross-Admin Requirement:** Registration of new tokens on the Ethereum side requires joint approval from both the Supra Foundation MultiSig Admin on Ethereum and the Operating MultiSig Admin on Ethereum.



#### 3. Supra Foundation MultiSig Admin on the Supra Blockchain

**Composition:** A 16-member committee comprising core members of the Supra team.

**Threshold:** 9 out of 16 signatures required to execute any admin function.

**Administrative Capabilities:**

* **Delegate Admin Capabilities:** Transfer delegation of these admin privileges to another MultiSig wallet address on the Supra blockchain.
* **Module Upgrades:** Upgrade Supra modules related to cross-chain message passing (CCM) and token bridging functionalities. This includes governance-approved code updates via the Supra runtime.
* **Deploy New Wrapped Tokens:** Deploy smart contracts for new wrapped token representations on Supra, enabling support for additional assets in the bridge ecosystem.
* **Minting Authority:** Grant minting capabilities to the Token Bridge Module, allowing controlled issuance of wrapped tokens upon successful bridge transfers from Ethereum.

**Cross-Admin Dependencies:**

* **Message Passing Verification Fee Modification:** Adjustments to fees for verifying cross-chain messages require concurrent approval from:
  * Supra Foundation MultiSig Admin on Ethereum.
  * Supra Foundation MultiSig Admin on Supra. This ensures balanced incentives across chains.



#### **Security and Best Practices:**

* **Threshold Design:** MultiSig thresholds (e.g., 9/16, 4/6) balance accessibility with security, requiring supermajority consensus.
* **Audit and Monitoring:** All admin transactions are publicly verifiable on-chain.
* **Recovery Mechanisms:** Delegation functions allow for key rotations or recoveries without halting operations.
* **Temporary Features:** Emergency withdrawal is audited for removal upon reverse bridge deployment, estimated in Q4 2025. In short, SupraNova begins to be run by more decentralized operators, towards full L1 security.





