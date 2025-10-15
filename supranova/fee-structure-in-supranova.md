# Fee Structure in SupraNova

SupraNova’s fee model is designed to sustain a permissionless, decentralized bridging ecosystem while incentivizing critical actors like relayers and committee updaters.

<figure><img src=".gitbook/assets/3.png" alt="" width="563"><figcaption></figcaption></figure>

\


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

* A small cut is deducted from the bridged amount (in bridged assets like supETH or supUSDC) at the time of minting.

{% hint style="warning" %}
**The entire bridging fee is borne by the bridging users.**
{% endhint %}

#### **What Does It Fund?**

* Relayer incentives
* Operational treasury buildup

***

## Fee Breakdown Example During a Bridge Transaction

{% hint style="info" %}
**Suppose you are bridging 1 ETH:**
{% endhint %}

| Fee Component                     | Example                                |
| --------------------------------- | -------------------------------------- |
| Message Passing Verification Fee  | 0.001 $SUPRA equivalent                |
| Service Layer Fee (token bridge)  | 0.1% of bridged amount                 |
| Relayer Reward (from service fee) | Paid after periodic treasury disbursal |
| Committee Updater Reward          | Paid after periodic treasury disbursal |



* The net amount minted as supETH will be slightly less than 1 ETH after fees.
* All fees are transparent and visible during the bridge confirmation step.

***

## Incentivizing Liveness

Both relayers and committee updaters are permissionless roles that ensure network liveness and decentralization.

They are incentivized through fee rewards:

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

### Tiered Service Fee Structure

To accommodate varying bridge volumes, SupraNova introduces a **tiered service fee model** based on the **value bridged in USDT**:

| Tier     | Bridged Value Range | Service Fee Rate |
| -------- | ------------------- | ---------------- |
| Micro    | < $5,000            | 0.5%             |
| Standard | $5,000 – $100,000   | 0.3%             |
| Whale    | > $100,000          | 0.2%             |

***

## SupraNova Address Details

<table><thead><tr><th width="123.15625">Network Name</th><th>Hypernova</th><th>Token Bridge</th></tr></thead><tbody><tr><td>Ethereum Sepolia</td><td>0x50888Fc24e1224E12f5C8a31310A47B98b2A7f75</td><td>0x71eb89D6D0d6ED44526dd8EAcb956735934eC2d5</td></tr><tr><td>Supra Testnet</td><td>0x3ab1136a5dbb76b923d2b02f95b042f5d80b70218a0395755eafb63d4eefc340</td><td>0x76b38ad503118cb7749a835d965b023a2fe4801dba736bfa66e18d1f0339695c</td></tr><tr><td>Ethereum Mainnet</td><td>0xEF7238503Fdd7671d85F3Cc5e4B9d7D90e99bFF1</td><td>0x7cECd42A15A691EF693512b1D508F1465ec5DA16</td></tr><tr><td>Supra Mainnet</td><td>0xda20f7d0ec813c751926f06004a10bc6ee1eefc96798f6a1aa31447ee146f932</td><td>0xda20f7d0ec813c751926f06004a10bc6ee1eefc96798f6a1aa31447ee146f932</td></tr></tbody></table>

***

## Asset Details

#### Testnet

<table><thead><tr><th width="123.15625">Asset Name</th><th width="250.6171875">Ethereum (Lock)</th><th width="95.59765625">Asset Name</th><th>Supra (Mint)</th></tr></thead><tbody><tr><td>WETH</td><td>0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14</td><td>supETH</td><td>0x1a57888bfcd31ef82a8e3dac93db14335b36f9228d5f050b722151ae2a77153a</td></tr><tr><td>USDC</td><td>0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238</td><td>supUSDC</td><td><p>0x3ca17a5b04d826f9be112c319e003b3a79b2682c40613876023758dbb0d</p><p>03e96</p></td></tr><tr><td>USDT</td><td>0xaA8E23Fb1079EA71e0a56F48a2aA51851D8433D0</td><td>supUSDT</td><td>0x2946e0f1a942ab8eb6f763f19da3d7540609aba8b00fb54e680cac5e048f8d0</td></tr><tr><td>SolvBTC</td><td>0xE33109766662932a26d978123383ff9E7bdeF346</td><td>supSolvBTC</td><td>0x420c8751a043b5b216cf02a011b0d563f3638eb082f91f0d879254e388f2b53a</td></tr><tr><td>WBTC</td><td>0x29f2D40B0605204364af54EC677bD022dA425d03</td><td>supBTC</td><td>0x7ca33bcce4f4bb7904ac7b46e3613b6b62dae55cc89923706aac1ad1c244e3e6</td></tr></tbody></table>

#### Mainnet

<table><thead><tr><th width="123.15625">Asset Name</th><th width="252.37890625">Ethereum (Lock)</th><th width="97.3984375">Asset Name</th><th>Supra (Mint)</th></tr></thead><tbody><tr><td>WETH</td><td>0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2</td><td>supETH</td><td>0xe4af154ade9551e7f58a23b8f727ae2dca050f1b74582bb518ba361c889d246d</td></tr><tr><td>USDC</td><td>0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48</td><td>supUSDC</td><td>0xf90b4b9d4a9d87c39fb3140513e52edc3ead5eaddcb9881b02becdeb63c5793d</td></tr><tr><td>USDT</td><td>0xdAC17F958D2ee523a2206206994597C13D831ec7</td><td>supUSDT</td><td>0x7b6463ca7a54ee37e113c8333db9c0af49de39555ee1cb44837db4c085f8964</td></tr><tr><td>SolvBTC</td><td>0x7A56E1C57C7475CCf742a1832B028F0456652F97</td><td>supSolvBTC</td><td>0x224bee573e0ae84ba7f1eee4f7063a945ad7a268bea7ab9e004f6890177a480b</td></tr><tr><td>WBTC</td><td>0x2260fac5e5542a773aa44fbcfedf7c193bc2c599</td><td>supBTC</td><td>0x348b1a4fa68803cbda3b50326a90e59b21e80c3ced93588b20950b5c0697a67e</td></tr></tbody></table>

***

## Asset Transaction Limit

#### Mainnet

<table><thead><tr><th width="152.296875">Asset Name</th><th>Max Global Limit</th><th>Per Transaction Limit</th></tr></thead><tbody><tr><td>ETH</td><td>23000000000000000000 (23 ETH)</td><td>4500000000000000000 (4.5 ETH)</td></tr><tr><td>USDC</td><td>100000000000  (100K USDC)</td><td>20000000000 (20K USDC)</td></tr><tr><td>USDT</td><td>100000000000 (100K USDT)</td><td>20000000000 (20K USDT)</td></tr><tr><td>SolvBTC</td><td>1000000000000000000 (1 solvBTC)</td><td>200000000000000000 (0.2 solvBTC)</td></tr><tr><td>WBTC</td><td>100000000 (1 wBTC)</td><td>20000000 (0.2 wBTC)</td></tr></tbody></table>

###
