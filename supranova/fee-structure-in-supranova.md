# Fee Structure in SupraNova

SupraNova’s fee model is designed to sustain a permissionless, decentralized bridging ecosystem while incentivizing critical actors like relayers and committee updaters.

<figure><img src=".gitbook/assets/3.png" alt="" width="563"><figcaption></figcaption></figure>

\
\
SupraNova applies two levels of fees internally but simplifies the user experience by exposing only the Service Layer Fee during transaction confirmation.\


* **Verification Fee:**\
  This internal fee compensates only the committee updaters for proof generation, signature validation, and validators’ public key update operations. It is the responsibility of the Service Layer to pay this fee to the message passing layer Users do not see this fee directly.\

* **Service Layer Fee:**\
  This fee is displayed to users during bridging and deducted at the time of submitting the request. It covers protocol operational costs, treasury buildup, relayer incentives and other service specific expenses.\
  \
  **Then the system separates fees into two distinct layers:**

| Fee Type          | Purpose                                                                 | Source of Funding        |
| ----------------- | ----------------------------------------------------------------------- | ------------------------ |
| Verification Fee  | Covers cryptographic proof verification on Supra                        | Funded from service fee. |
| Service Layer Fee | Specific to the token bridge service, customizable by service providers | Paid by bridging users   |

***

### Message Passing Layer Fee

This fee is charged for:

* Verifying Sync Committee proofs
* Verifying ancestry and receipt proofs
* Managing public key updates (Committee Updater work)
* Safe validators committee handovers through public key updates.&#x20;

#### **Who Charges It?**

* The HyperNovaCore verifier contract charges this fee internally. Service-contracts calling the message-passing layer for validation pay this fee as part of the process.

***

### Service Layer Fee (Token Bridge Specific)

This fee is charged by service-specific components like the Token Bridge and includes:

* Operational Service costs.
* Relayer rewards.

#### **Who Sets It?**

* The Token Bridge smart contract service owner (typically the protocol team).

#### **How is it Collected?**

* A small cut is deducted from the bridged amount (in wETH) at the time of minting.

{% hint style="warning" %}
**The entire bridging fee is borne by the bridging users.**
{% endhint %}

#### **What Does It Fund?**

* Relayer incentives
* Operational treasury buildup

#### Fee Breakdown Example During a Bridge Transaction

{% hint style="success" %}
**Suppose you are bridging 1 ETH:**
{% endhint %}

| Fee Component                     | Example                                |
| --------------------------------- | -------------------------------------- |
| Message Passing Verification Fee  | 0.001 $SUPRA equivalent                |
| Service Layer Fee (token bridge)  | 0.1% of bridged amount                 |
| Relayer Reward (from service fee) | Paid after periodic treasury disbursal |
| Committee Updater Reward          | Paid after periodic treasury disbursal |



* The net amount minted as wETH will be slightly less than 1 ETH after fees.
* All fees are transparent and visible during the bridge confirmation step.

***

### How SupraNova Incentivizes Liveness

Actors like relayers and committee updaters are permissionless, meaning any one such as service providers themselves can host a relayer or a committee updater:

They are incentivized through fee rewards:

| Actor             | Task                       | Incentive                                      |
| ----------------- | -------------------------- | ---------------------------------------------- |
| Relayer           | Submit proof bundles       | Treasury payouts funded by service fees        |
| Committee Updater | Update Sync Committee keys | Direct payouts from message passing layer fees |

Without sufficient incentives:

* Relayers would delay proof submissions
* Updaters might fail to refresh committee keys

SupraNova’s modular fee system makes sure both actor roles remain sustainably rewarded.

{% hint style="info" %}
**Current Testnet Settings**

* **In the Testnet release, some fees (especially the verification fee) are set to zero or significantly reduced for easier testing.**
* **In mainnet launch, full fee structures will be enforced.**
{% endhint %}

***

## Fee Model for our Ethereum to Supra Bridge protocol

This protocol involves a **Token Bridge Service** and a **Message Passing Layer**. The model details various fees and rewards, including:

* **Service Fee (S)**
* **Relayer Rewards (RR)**
* **Committee Updater Rewards (CUR)**
* **Relayer Expense (RG)**
* **Committee Updater Expense (CUG)**
* **Verification Fee (V)**

### Key points of the model:

* Users pay a **Service Fee (S)** and **gas costs** when initiating a bridge request.
* **Relayers** are compensated with **Relayer Rewards (RR)** that cover their expenses (**RG**).
* **Committee Updaters (CU)** are rewarded (**CUR**) for updating Ethereum sync committee public keys to Supra, covering their expenses (**CUG**).
* **RG** and **CUG** are treated as constants determined by the Admin.

### Fee Derivation Formulas

Given traffic estimate `X`, and gross margins `SM`, `VM`, `CUM`, `RM`:

* `CUR = CUG / (1 - CUM)`
* `V = (CUR / X) / (1 - VM)`
* `RR = (V + RG) / (1 - RM)`
* `S = RR / (1 - SM)`

***

## Tiered Service Fee Structure

To accommodate varying bridge volumes, SupraNova introduces a **tiered service fee model** based on the **value bridged in USDT**:

| Tier     | Bridged Value Range | Service Fee Rate |
| -------- | ------------------- | ---------------- |
| Micro    | < $5,000            | 0.5%             |
| Standard | $5,000 – $100,000   | 0.3%             |
| Whale    | > $100,000          | 0.2%             |

***

## Fee Overview and Responsibilities

SupraNova applies two levels of fees internally but simplifies the user experience by exposing only the **Service Layer Fee** during transaction confirmation.

### **Verification Fee**

An internal fee that compensates **Committee Updaters** for:

* Proof generation
* Signature validation
* Validator public key updates

**Paid by:** Service Layer (not the user directly)

***

### **Service Layer Fee**

Visible to users and deducted upon initiating a bridge transaction. It covers:

* Operational costs
* Treasury accumulation
* Relayer and Committee Updater rewards

| Fee Type          | Purpose                                                                 | Source of Funding       |
| ----------------- | ----------------------------------------------------------------------- | ----------------------- |
| Verification Fee  | Covers cryptographic proof verification on Supra                        | Funded from Service Fee |
| Service Layer Fee | Specific to the token bridge service, customizable by service providers | Paid by bridging users  |

{% hint style="danger" %}
The entire bridging fee is borne by the bridging users.
{% endhint %}

***

## Fee Breakdown Example During a Bridge Transaction

{% hint style="info" %}
**Suppose you are bridging 1 ETH**
{% endhint %}

| Fee Component                    | Example                            |
| -------------------------------- | ---------------------------------- |
| Message Passing Verification Fee | 0.001 $SUPRA equivalent            |
| Service Layer Fee (token bridge) | Tier-based cut from bridged amount |
| Relayer Reward                   | Funded from Service Layer Fee      |
| Committee Updater Reward         | Funded from Service Layer Fee      |

* Net amount minted as wETH will be slightly less than 1 ETH after fees.
* All fees are transparent and visible during the bridge confirmation step.

***

## Incentivizing Liveness

Both relayers and committee updaters are permissionless roles that ensure network liveness and decentralization.

| Actor            | Task                              | Incentive Source                       |
| ---------------- | --------------------------------- | -------------------------------------- |
| Relayer          | Submit proof bundles              | Treasury payouts via Service Layer Fee |
| CommitteeUpdater | Update Sync Committee public keys | Payouts via Verification Fee           |

Without sufficient rewards:

* Relayers may delay proof submissions
* Committee updates may lapse, compromising security

SupraNova’s modular fee and reward system ensures both roles remain sustainably incentivized.

{% hint style="info" %}
**Testnet Fee Settings**

* Some fees (especially the Verification Fee) are reduced or zero for testing purposes.
* The full model is enforced at Mainnet launch.
{% endhint %}

***

### Token Bridge Details

<table><thead><tr><th width="123.15625">Network Name</th><th>Hypernova</th><th>Token Bridge</th></tr></thead><tbody><tr><td>Ethereum Sepolia</td><td>0x50888Fc24e1224E12f5C8a31310A47B98b2A7f75</td><td>0x71eb89D6D0d6ED44526dd8EAcb956735934eC2d5</td></tr><tr><td>Supra Testnet</td><td>0xada99b67a6dbef6b1b30dbd3c059a0a6d52db8ae8994f1304100b81d3f0555bf</td><td>0xeea93baecf106a4d98cc9859afc2f74709bfc664e0fb916fea8ac65fcb5a67da</td></tr></tbody></table>

### Address Details

<table><thead><tr><th width="123.15625">Asset Name</th><th>Ethereum (Lock)</th><th>Supra (Mint)</th></tr></thead><tbody><tr><td>WETH</td><td>0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14</td><td>0x259762e67167464aeb018777395c808493983e2162b3f475cbd7ed0761aa2915</td></tr><tr><td>USDC</td><td>0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238</td><td>0x31f5836c7163802690e5a170fbf70c48eda42f50a8948219c8a49f3140c3263d</td></tr></tbody></table>

***

### Asset Transaction Limit

<table><thead><tr><th width="152.296875">Asset Name</th><th>Max Value</th></tr></thead><tbody><tr><td>ETH</td><td><p>184467440737.09551615  ETH</p><p></p><p>Receiver balance should not be more than 18446744073709551615</p></td></tr><tr><td>USDC</td><td><p>18446744073709.551615 USDC</p><p><br>Receiver balance should not be more than 18446744073709551615</p></td></tr></tbody></table>

###
