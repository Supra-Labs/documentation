# Supra Multisig Guide

The Supra Multisig module control to Multisig accounts unlike traditional Multisig systems that require users to specify public keys, this Module only requires addresses as owners.&#x20;

This module also supports updating owners without having to change the auth key. It offers two options for storing transaction payloads: either a full on-chain payload (providing full decentralization and transparency) or a lightweight payload hash (saving gas without sacrificing security during execution).

### Key Benefits

1. **Dynamic Owner Management:** Easily add or remove owners without resetting the auth key.\

2. **Flexible Transaction Storage:**
   * **Full Payload:** Maximum transparency and resilience.
   * **Hash-only:** Saves gas while retaining verification ability.\

3. **Nonce-Based Order Enforcement:** Transactions execute in order, ensuring predictable behavior.\

4. **Simple Onboarding:** Create Multisig accounts with a default owner and extend as needed.

## Interacting with Multisig via Supra CLI

Supra offers intuitive CLI commands to interact with multisig accounts. Here’s guide for CLI Commands:

#### Create a new multisig account on-chain:

```powershell
supra move multisig create 
--timeout-duration <TIMEOUT_DURATION> 
--num-signatures-required <NUM_SIGNATURES_REQUIRED>
--additional-owners <ADDRESSES OF OTHER OWNER/OWNERS>
```

{% hint style="info" %}
Use `--additional-owners` Even if you want a single threshold multisig, you must add the other owner's/owners address to not face "**MULTISIG\_TRANSACTION\_INSUFFICIENT\_APPROVALS**" ERROR while Executing Proposed Transaction.
{% endhint %}

#### Approve a pending multisig transaction:

```powershell
supra move multisig approve 
--multisig-address <MULTISIG_ADDRESS> 
--sequence-number <SEQUENCE_NUMBER>
```

#### Propose a new multisig transaction:

```powershell
supra move multisig create-transaction 
--multisig-address <MULTISIG_ADDRESS> 
--function-id <FUNCTION_ID>
```

#### Execute a multisig transaction that has its full payload stored on-chain:

```powershell
supra move multisig execute --multisig-address <MULTISIG_ADDRESS> --max-gas 500
```

#### Removes a proposed multisig transaction (finalizes a rejection):

```powershell
supra move multisig execute-reject --multisig-address <MULTISIG_ADDRESS> --max-gas 500
```

#### Execute a multisig transaction where only a payload hash was stored on-chain:

{% hint style="info" %}
**you will provide the full payload at execution time.**
{% endhint %}

```powershell
supra move multisig execute-with-payload 
--multisig-address <MULTISIG_ADDRESS> 
--function-id <FUNCTION_ID>
 --max-gas 500
```

#### Reject a multisig transaction:

```powershell
supra move multisig reject 
--multisig-address <MULTISIG_ADDRESS> 
--sequence-number <SEQUENCE_NUMBER>
```

#### Verify that the entry function matches the on-chain transaction proposal (serialization check)

```powershell
supra move multisig serialize-proposal --function-id <FUNCTION_ID>
```

#### Verify that the proposed transaction (its entry function and parameters) matches the on-chain proposal:

```powershell
supra move multisig verify-proposal 
--multisig-address <MULTISIG_ADDRESS> 
--sequence-number <SEQUENCE_NUMBER> 
--function-id <FUNCTION_ID>
```

## Transaction Flow Overview

The typical workflow when interacting with a multisig account is as follows:\


1. Creation of the Multisig Account:
   * **`create`:** Instantiates a multisig account with a single default owner.\

2. &#x20;Transaction Creation:

* `create_transaction`: An owner can initiate a multisig transaction by providing the entire transaction payload.
* A unique transaction Sequence ID is assigned to every newly proposed transaction.\


1. Transaction Voting (Approval/Rejection):
   * `approve`: Other owners can approve the pending transaction by submitting their approval with the transaction ID.
   * `reject`: Conversely, owners can reject a transaction if they do not agree with its content.\

2. Transaction Execution:
   * `execute` or `execute_with_payload` :
     * If enough approvals have accumulated, any owner can execute the transaction.
     * Depending on whether the full payload or just a hash is stored, the appropriate execute function is called.
   * Execution Details:
     * The multisig module first verifies that the payload has received the required number of signatures.
     * The executing owner pays for the gas fee.\

3. Finalizing Rejected Transactions:
   * `execute_reject` :
     * Once a transaction accumulates enough rejections, any owner can finalize the rejection of that transaction.

## Practical Tips

* Transaction Proposals: Depending on your cost and transparency preferences, choose between storing the full payload or just the payload hash when using `create-transaction`.\

* Voting and Execution: Utilize the `approve` or `reject` sub-command with the appropriate sequence numbers. When ready, execute the transaction using either `execute` (if the full payload is on-chain) or `execute-with-payload` (if you stored only a hash).\

* Verification: Always verify the proposal details with `serialize-proposal` or `verify-proposal` prior to voting, ensuring the on-chain data aligns with your expected parameters.

## Link to Module & Repository Docs:

<table data-view="cards"><thead><tr><th></th><th data-type="content-ref"></th></tr></thead><tbody><tr><td>multisig_account.move</td><td><a href="https://github.com/Entropy-Foundation/aptos-core/blob/dev/aptos-move/framework/supra-framework/sources/multisig_account.move">https://github.com/Entropy-Foundation/aptos-core/blob/dev/aptos-move/framework/supra-framework/sources/multisig_account.move</a></td></tr><tr><td>doc/multisig_account.md</td><td><a href="https://github.com/Entropy-Foundation/aptos-core/blob/dev/aptos-move/framework/supra-framework/doc/multisig_account.md">https://github.com/Entropy-Foundation/aptos-core/blob/dev/aptos-move/framework/supra-framework/doc/multisig_account.md</a></td></tr></tbody></table>

