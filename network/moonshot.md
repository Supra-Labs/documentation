---
description: >-
  Moonshot is a family of high-performance Byzantine Fault Tolerant (BFT)
  consensus protocols designed for blockchain systems.
---

# Moonshot Consensus Algorithm

### Overview

The Moonshot protocols aim to optimize chain-based rotating leader BFT consensus by achieving both low latency and high throughput.

### Key Features

1. Low Latency: Moonshot protocols achieve a minimum view change block period (ω) of δ and a minimum commit latency (λ) of 3δ, where δ is the network transmission latency.
2. Optimistic Responsiveness: Moonshot protocols make progress in time proportional to the actual network delay, independent of any known upper bound.
3. Reorg Resilience: Moonshot ensures that when an honest leader proposes after Global Stabilization Time (GST), one of its proposals becomes certified and is extended by every subsequently certified proposal.
4. Short View Length: Pipelined Moonshot and Commit Moonshot achieve a view length (τ) of 3Δ, where Δ is the known upper bound on network delay.
5. Pipelining: Some Moonshot variants implement pipelining to improve efficiency in certain network conditions.

### Variants

1. **Simple Moonshot:** The basic version with ω = δ, λ = 3δ, and reorg resilience.
2. **Pipelined Moonshot:** Improves upon Simple Moonshot with full optimistic responsiveness and τ = 3Δ.
3. **Commit Moonshot**: Further optimizes for scenarios where block proposals take longer to disseminate than votes.

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Explicit commit votes (pictured in green) enable Commit Moonshot to commit blocks sooner than its pipelined counterparts when block proposals (pictured in blue) take sufficiently longer to disseminate than votes.</p></figcaption></figure>

1.**Direct Pre-commit:** When a node receives Cv(Bk) in any view v' where v' ≤ v:

* If timeout\_view < v, send out ⟨commit, H(Bk), v⟩

2.**Indirect Pre-commit**: When a node receives Cv(Bk) in any view:

* If it has already sent a commit vote for any descendant of Bk
* And timeout\_view < v
* And it hasn't yet sent ⟨commit, H(Bk), v⟩ Then send out ⟨commit, H(Bk), v⟩

3.**Alternative Direct Commit**: When a node receives commit messages from a quorum:

* If the messages are ⟨commit, H(Bk), v⟩ from different nodes
* In any view Then commit Bk

### Performance

Moonshot protocols have been shown to outperform existing state-of-the-art protocols like Jolteon in both failure-free scenarios and the presence of failures. They achieve higher throughput and lower latency in wide-area networks of up to 200 nodes.

### Implementation Considerations

While Moonshot protocols offer superior performance in many scenarios, they have higher communication complexity (O(n²)) compared to some linear protocols. Developers should consider the trade-offs between communication complexity and performance metrics like latency and throughput when choosing a consensus protocol for their specific use case.

For detailed implementation guidelines and API references, please refer to the subsequent sections of this documentation.
