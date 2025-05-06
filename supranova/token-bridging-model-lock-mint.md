# Token Bridging Model - Lock-Mint

SupraNova’s initial asset bridging uses the Lock-Mint model, a secure method that preserves the total supply of assets across chains while ensuring collateralization.

### What is the Lock-Mint Bridging Model?

In a Lock-Mint model:

* The asset on the source chain (Ethereum) is locked securely in a vault contract.
* A wrapped equivalent token is minted on the destination chain (Supra).

There is no destruction, burning, or movement of tokens between chains.

Instead:

* Original tokens remain locked.
* Wrapped tokens represent a claim on the locked original.
* The minting only occurs after proof verification by HyperNovaCore on Supra.

#### How Lock-Mint Works in SupraNova

| **Steps** | **Action**                                                                                              |
| --------- | ------------------------------------------------------------------------------------------------------- |
| Step 1    | User initiates a bridge request locking ETH into HyperNovaCore (Ethereum)                               |
| Step 2    | Relayer submits proofs verifying the lock event                                                         |
| Step 3    | Supra’s HyperNovaCore verifier validates the proofs                                                     |
| Step 4    | The Token Bridge Service Contract mints equivalent wrapped ETH (wETH) on Supra to the recipient address |

**Example Flow**

* You bridge 5 ETH from Ethereum.
* SupraNova locks exactly 5 ETH in the Ethereum bridge vault.
* After proof validation, Supra mints the equivalent amount of wETH to your wallet, minus any applicable service fee.

**Note:**

* **In the current testnet release, fee deductions may be waived or minimal.**

**Collateral Status:**

* ETH remains locked securely in Ethereum contracts.
* wETH is fully backed 1:1 by real ETH.

#### Security Guarantees

* **No Double Spending:** Because ETH remains immobile once locked.
* **Full Collateralization:** wETH minted on Supra cannot exceed ETH locked on Ethereum.
* **Proof-Driven Minting:** No minting happens without complete proof validation across transaction, receipt, and consensus.

#### Why is Lock-Mint chosen?

SupraNova chose Lock-Mint because:

* It is simple and verifiable.
* It aligns perfectly with Supra’s trustless, proof-based validation model.
* It avoids complexities like token burns, which are harder to verify without replay attacks or external watchdogs.

#### Supported Asset in Initial Release

| **Source Token**            | **Destination Token**    | **Model** |
| --------------------------- | ------------------------ | --------- |
| ETH (Ethereum native asset) | wETH (Supra FA standard) | Lock-Mint |

**Note:**

* **You cannot unlock ETH back to Ethereum yet in this release.**
* **Supra → Ethereum (burn-unlock model) will be added in a future HyperNova reverse bridge upgrade.**

### Future Transfer Models

SupraNova currently uses a Lock-Mint bridging model where original assets are locked on the source chain and mirrored on Supra through minting.

In future versions, the bridge will also support:

* **Burn-Release**: Users will burn wrapped tokens on Supra, which will trigger the release of locked assets back to the source chain.
* **Burn-Mint**: Burning wrapped assets on Supra to mint another representation on a different destination chain.

These additional modes will allow Supra to offer both one-way and bi-directional bridging based on user needs and liquidity dynamics.

\
\
