# A Deeper Dive into Supra dVRF

{% embed url="https://vimeo.com/793270834/a195f25d3f" %}

Numerous Web3 applications (such as online gaming and lotteries on blockchain) require reliable sources of random numbers that are _unpredictable, unbiasable_ and _tamper-proof_. Verifiable random functions (VRF) enable generating numbers that are as good as random (_pseudorandom_) and can be (publicly) verified cryptographically. Pseudorandomness guarantees both unpredictability and unbiasability, whereas tamper-proofness is guaranteed by verifiability. However, to ensure pseudorandomness, one needs to maintain the secrecy of the private key used to generate the number. Therefore, any on-chain mechanism for VRF generation becomes problematic. To resolve this, the VRF computation is delegated off-chain to dedicated nodes.

However, centralized VRF computation relies upon one single node to store the private key. This becomes a single point of failure and is, therefore, inherently incompatible with the decentralization paradigm. Our approach is to use a more advanced primitive called decentralized VRF (or dVRF), where the private key is shared between many nodes in a cryptographically secure way such that any T+1 out of N nodes holds the entire private key, but any T or fewer nodes have no knowledge of that.

The VRF computation is done collectively by at least T+1 nodes in that each node computes a partial evaluation using their respective key shares, and anyone can publicly aggregate them together to compute the final computation. Importantly, the VRF verification procedure (which is done by the application) is agnostic to whether the VRF output is produced using a dVRF or a centralized VRF. This renders the framework flexible. Using a dVRF instead of a centralized VRF has huge security benefits as it is resilient to T or less faulty nodes that may behave arbitrarily maliciously (a.k.a. Byzantine).

However, using dVRF introduces new security challenges compared to VRF. A dVRF should be _consistent_, _robust, available,_ and _strongly pseudorandom_:

(i) Consistency guarantees that it does not matter which T+1 nodes participate; this ensures that the verification procedure is completely agnostic of the VRF generation procedure and supports agility in the architecture (for example, T can be changed as per requirement).

(ii) Robustness guarantees that the aggregation procedure must detect illegitimate partial evaluations and discard them; this guarantees that an honestly aggregated VRF output is correct.

(iii) Available - which means no matter what the adversary does (including, for example, not responding), as long as it compromises up to T nodes, a correct VRF output must be computed.

(iv) Strongly pseudorandom; this implies that the VRF output is pseudorandom even when up to T nodes are compromised and possibly collude. Our approach satisfies all of these.

Our approach uses a construction called GLOW \[Galindo et al. 21], which in turn is heavily based on BLS threshold signatures. Recall that (N, T) threshold signatures enable the generation of signatures by any T+1 nodes (this ensures consistency).

Additionally, it requires (for robustness) that each partial signature is verified by the aggregator before aggregation. For this purpose, a special purpose Zero-Knowledge Proof (ZKP) is used in GLOW. The use of a ZKP not only enables the verification of partial signatures but also ensures strong pseudorandomness.

An alternative approach (followed by Dfinity's distributed randomness beacon) uses a partial BLS verification instead of ZKP. While this guarantees robustness, it suffers from two drawbacks compared to GLOW: (i) it can not be proven strongly pseudorandom; (ii) it involves pairing computation, which is about 2.5x slower than the ZKP generation. The final VRF output is computed as just a hash of the signature, whereas the signature itself is supplied as a proof; the VRF output can be verified by checking whether the proof is indeed the BLS signature, and the VRF output is the hash of that.

Finally, for availability, we allow a corruption threshold T such that N is at least 2T+1; this ensures that even if T nodes are compromised and stay silent, there are T+1 honest nodes who can compute the VRF correctly. Clearly, all essential properties are met with this approach.

Of course, using dVRF compared to a centralized VRF has slight trade-offs in terms of computational efficiency, communication cost, and complexity of the system. Nevertheless, the gain in terms of security outweighs them significantly.
