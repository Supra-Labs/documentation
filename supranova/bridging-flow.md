# Bridging Flow

This section explains how to use the SupraNova Bridge dApp to bridge ETH from Ethereum to wETH on Supra network, with each technical stage explained clearly.

## Stage 1: Connecting Your Wallets

Before initiating a bridge transaction, you must connect two wallets:

| **Wallet**                               | **Purpose**                                      |
| ---------------------------------------- | ------------------------------------------------ |
| MetaMask (Ethereum) or any source wallet | To send the ETH and sign the bridge transaction  |
| Supra Wallet                             | To receive the wrappedETH (wETH) minted on Supra |

### Getting Started:

* Open the SupraNova Bridge dApp.
* Click “Connect Wallet” on the top right corner.
* Approve the MetaMask pop-up to connect your Ethereum wallet.
* Choose your Supra Wallet (StarKey) or manually enter a recipient address using the “Type Recipient Address” toggle.

The toggle is notated as 1 and the existing wallet is notated as 2.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdQnA3nfdzQ4NG1rCCUQPiqdKweUbqeiQAVKVCEHXQJGO-qTIUXHKa3lT6wywbYDXtns9d4ZBU5XWO4Eqg5jN1sDBb_gzczQ6QPPowi2YWyuwtOy0Kt29ivPs9VU8ZPLeXSndn_bg?key=W1uheq6YYdL1sS6tR2vIl4e_)

&#x20;**Note:**

* **The Supra Wallet is where your minted wETH will arrive after successful bridging.**
* **Make sure the address is correct before proceeding.**

## Stage 2: Initiating the Bridge Transaction

After wallets are connected, follow these steps.

Under the Bridge tab, select:

* Source Chain: Ethereum
* Destination Chain: Supra
* Token: ETH –>  wETH
* Enter the amount of ETH you wish to bridge.

Review:

1. Estimated bridge time
2. Bridge fee breakdown (in ETH and $SUPRA)

Click **“Confirm”** to proceed.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfj7UB1OxdlPvEPNLJ98mo_w0BnC77-a-wEzxgz56P1aNigNYur-St-351roxiZLxbwOvrFsqZG_wcQfRxMNFq_Xd9y-oIKmZebQUR54awjlfP0aBbY2FurOzaDdB6AANwOOJ0p6g?key=W1uheq6YYdL1sS6tR2vIl4e_)

**Internal Protocol Note:**

* **This action calls the HyperNovaCore smart contract on Ethereum, locking your ETH and emitting a LOGX event signaling a bridge request.**

## Stage 3: MetaMask Transaction Confirmation

Upon clicking **“Confirm”**:

* MetaMask prompts you to confirm the transaction.
* Gas fees are shown (normal Ethereum fees apply).
* Approve and confirm the transaction.![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeG_ekdOxb8t-9iJTO6fGnp7vKhvOmJUNr-mWatb5LSY_HPY8S4a4vUpwF4mgFiknZjQRCW_kUA6jNH3h-Trb5v3wxFsg_nng3V5W1WPFR01SbqzwBFUw3Teqxml_jgcqMBBDy0CQ?key=W1uheq6YYdL1sS6tR2vIl4e_)

## Stage 4:  Event Capture and Proof Generation by Relayer

After the bridge event is emitted:

* The Event Listener captures the bridge event from the ethereum chain and stores it in a message queue. The Relayer Driver then consumes the event. detects the bridge event from the Ethereum chain.
* It generates the following proofs:\


1. **Sync Committee Proof** – Block signed by the Sync Committee
2. **Receipt Proof** – Proof that the transaction succeeded
3. **Ancestry Proof** (if the block lacks enough signatures)

If required, Ancestry Proof connects the event’s block to a more recent, heavily signed block.

The Technical flow is as follows:

**Ethereum Event → Relayer captures → Builds Proof Bundle → Prepares Submission to Supra**

## Stage 5: Proof Submission and On-Chain Verification

Once proofs are generated:

* The Relayer submits the proof bundle to Supra’s HyperNovaCore verifier smart contract.
* The verifier checks:

1. Validity of the block header signatures
2. Correctness of transaction existence and execution
3. Authenticity of the event data (right source, token, amount, recipient)\
   If all checks pass, the verifier marks the bridge request as valid.

## Stage 6: Token Minting and Fund Receipt

Upon successful verification:

* The Token Bridge Service Contract is triggered.
* Wrapped ETH (wETH) is minted on Supra in a 1:1 ratio to the locked ETH.
* The minted wETH is sent to your connected (or specified) Supra Wallet address.

You can now:

* View transaction history under Explorer > My Transactions.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfeg7FeGFyZkc1PTMJ9ibc-CFClKclgGPcwZWrspeSchgRR6UDPINW2iIFSLq5bIBtXwNSZcHgPy3r9XdG_upnWFynyBPMYi8h2dTGLZC0RzvhmQTbJwfGoA7arkmRmBhBb0uRFQQ?key=W1uheq6YYdL1sS6tR2vIl4e_)

#### &#x20;Transaction Lifecycle Summary

| **Stage**          | **System Action**                         | **User Action**       |
| ------------------ | ----------------------------------------- | --------------------- |
| Wallet Connection  | Connect MetaMask and Supra Wallet         | Click Connect Wallet  |
| Initiate Bridge    | Lock ETH on Ethereum                      | Enter Amount, Confirm |
| Event Emission     | Emit bridge request log                   | Wait                  |
| Relayer Proofs     | Generate, Sync , Receipt, Ancestry proofs | Wait                  |
| Supra Verification | Validate proofs on-chain                  | Monitor Explore       |
| Token Min          | Mint wETH to recipient wallet             | Receive tokens        |

**Please note, during Bridging these are the in-built safeguards:**

* **Incomplete Proofs: Bridge request remains pending until sufficient proofs arrive.**
* **Relayer Failure: Another permissionless relayer can submit the proof.**
* **Committee Key Rotation: If Sync Committee public keys expire, Committee Updater submits fresh keys to Supra automatically.**
