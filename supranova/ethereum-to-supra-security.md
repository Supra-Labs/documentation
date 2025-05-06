# Ethereum to Supra Security

SupraNova is designed not just for operational efficiency, but also for deep, protocol-level security through its modular components like HyperNovaCore, Relayers, and Committee Updates.

It actively addresses several major risk factors that traditional bridges often fail to solve.

Each component plays a distinct role in minimizing attack surface, validating cryptographic proofs, and to maintain liveness under adversarial conditions.

#### Long-Range Attack Risks: How SupraNova Handles Them

A potential vulnerability in trustless bridges using the Sync Committee model is the possibility of a long-range attack:

* A malicious Sync Committee could collude to forge signatures on a fake block.
* If unchecked, this could allow them to fool external chains like Supra into believing invalid transactions are real.

**Protocol Response:**

* SupraNova requires more than 90% of Sync Committee members to sign a block for it to be considered valid.
* Even if signatures are forged, the fork would not be accepted unless justified by Ethereum’s fork choice rule(LMD-Ghost).
* The probability of randomly selecting a colluding majority is astronomically low (studied by [Succinct](https://www.gnosis.io/blog/succincts-ethereum-zk-light-client-and-the-road-to-trust-minimzed-bridges-with-hashi), Snowfork, [T3rn](https://www.t3rn.io/blog/exploring-eths-altair-light-client-protocol-t3rns-vision), etc.).
* Even if a malicious Sync Committee existed, they could not fork Ethereum itself ,  only mislead a bridge verifier if the bridge does not validate correctly.
* HyperNovaCore on-chain verifier checks proof authenticity and threshold participation before accepting any event.

**Security Insight:**&#x20;

* **Ethereum’s primary consensus remains intact even if a Sync Committee misbehaves.**&#x20;
* **SupraNova’s HyperNovaCore piggybacks on Ethereum’s finality guarantees by verifying Sync Committee signatures and ancestry root on-chain.**&#x20;

### Liveness Threats: Missing Sync Committee Signatures

Sometimes, an Ethereum block may have:

* Very low Sync Committee participation
* No signatures at all
* Delay in event availability

This could disrupt timely proof verification.

**Protocol Response:**

* If the bridge event’s block lacks signatures, Relayers submit Ancestry Proofs; to trace back to a recent block that is signed with more than the threshold number of Sync Committee members. HyperNovaCore validates them to maintain liveness.
* Supra’s verifier can validate bridge events indirectly through a valid child block, maintaining liveness even during Ethereum anomalies.

#### Relayer Safety: What if Relayers Misbehave?

* The relayer is permissionless.
* Anyone can pick up events and submit proofs.

**Possible issues:**

* A relayer could submit incomplete proofs
* A relayer could submit tampered proofs

**Protocol Response:**

* The on-chain HyperNovaCore verifier rejects any invalid or incomplete submission. No service level bridge minting of assets can occur unless full proof validation passes on-chain.
* Invalid proofs are rejected;at worst, relayers incur gas cost without affecting protocol state.
* Relayer rewards are structured such that relayers have economic incentives to behave correctly, but even if they don’t behave correctly no safety violation can happen

#### Committee Updater Safety

* Ethereum’s Sync Committee rotates every \~27 hours.

**If the public keys are not updated on Supra:**

* New blocks cannot be verified
* Bridge operations stall
* This would also prevent the verifier from recognizing new finalized blocks, effectively pausing new bridge actions.

**Protocol Response:**

* Committee Updaters are funded through protocol-collected fees and operate independently to refresh Sync Committee public keys on Supra approximately every 27 hours.&#x20;
* This automation maintains decentralization and system continuity.
* If an updater becomes inactive, others can be permissionlessly added to resume key publishing and restore liveness.

#### Summary of Security Layers

| Threat                    | Mitigation Mechanism                              |
| ------------------------- | ------------------------------------------------- |
| Long-range attacks        | High signature threshold (>90% required)          |
| Missing signatures        | Ancestry Proofs fallback                          |
| Malicious relayers        | On-chain proof validation rejects bad submissions |
| Stale Sync Committee Keys | Permissionless Committee Updater mechanism        |

