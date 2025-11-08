# SupraEVM Beta Bounty

We're offering a 4**0,000 USDC** bounty to any developer or team that can prove that they have a faster and provably correct parallel execution algorithm for the Ethereum Virtual Machine (EVM) than Supra’s BTM (Supra's conflict specification-aware Block Transactional Memory). The purpose of parallel execution is to handle large payloads with a lot of smart contract contention.

{% hint style="info" %}
**Check The Benchmarking** [**Guide Here**](supraevm-beta.md)**.**
{% endhint %}

### Key Rules:

* The competing algorithm must be open-sourced and provably correct over at least 100,000 real Ethereum blocks.
* All experiments must be conducted on commodity hardware (up to 16 cores) to maintain decentralization and practical reproducibility.
* No cherry-picking of blocks or configurations is allowed; results must reflect the algorithm’s consistent performance across the entire dataset.
* Identical thread configurations (4, 8, 16 and at most 256 fibers for Monad 2PE) must be used for all comparisons.
* The final benchmark report must include TPS results per configuration and the overall average to identify the true winner.
* If no competing algorithm outperforms SupraBTM under these conditions, the most comprehensive benchmarking report demonstrating this outcome will still be awarded 10,000 USDC.
* 500 USDC each will be granted to up to 10 participants who publish and share valid experimental results, regardless of outcome.
* If a participant successfully outperforms SupraBTM under the above verified and reproducible conditions, they will receive the full 40,000 USDC bounty.
* By the end of the campaign, if no one surpasses SupraBTM benchmarks, the best overall data report among valid submissions will receive 10,000 USDC.

{% hint style="success" %}
The competition runs for one month (**Ending Nov 21st**), and payouts are made one month after verification. Let’s find out who has the fastest EVM parallel execution in the world!&#x20;
{% endhint %}

### **System Setup Requirements**

#### **Hardware & Environment**

Benchmarks were executed on a dedicated server machine:

* **Server:** f4.metal.medium
* **CPU:** AMD 4564P, 16 cores @ 4.5 GHz
* **RAM:** 192 GB
* **Storage:** 2 × 480 GB NVMe + 2 × 1.9 TB NVMe
* **Networking:** 2 × 10 Gbps NICs
* **Execution Environment:** both executors were run inside Docker to isolate dependencies and ensure reproducibility.

#### **Executor Implementations**

* **SupraBTM (iBTM):** Implemented in **Rust**, built on **REVM** as the underlying EVM engine.
* **Monad 2PE:** Implemented in **C++**, leveraging a native **C++ EVM** implementation. Monad has recently open-sourced this codebase.
* **Sequential Executor (Baseline):** Our in-house sequential executor written in **Rust**, using the same REVM backend as iBTM.

#### Experiment Requirements

1. Participants must run experiments using the same thread configurations — specifically 4, 8, and 16 threads (at most 256 fibers for Monad 2PE) — and report the TPS (Transactions Per Second) for each configuration individually and overall.
   * The final report must clearly indicate the overall TPS and Execution Time in milliseconds and must show the 10% improvement over one another.
2. Cherry-picking blocks (i.e., selectively choosing blocks where one approach outperforms another) is not allowed. All experiments must be conducted over a continuous, representative range of real Ethereum blocks.
3. Experiments using single-threaded (1-thread) execution are not permitted.
4. All comparisons must be performed under the same configuration settings and on commodity hardware to ensure fairness and reproducibility.

#### Benchmarking Results Submission

Community members are already verifying benchmark results, post your submissions on **X** and tag us at [@SUPRA\_Labs](https://x.com/SUPRA_Labs)
