# SupraEVM Beta Bounty

We're offering a **30,000 USDC** bounty to any developer or team that can prove that they have a faster and provably correct parallel execution algorithm for the Ethereum Virtual Machine (EVM) than Supra’s BTM (Supra's conflict specification-aware Block Transactional Memory).&#x20;

{% hint style="info" %}
**Check The Benchmarking** [**Guide Here**](supraevm-beta.md)**.**
{% endhint %}

### Key Rules:

1. The competing algorithm must be open-sourced and provably correct over 100,000 real Ethereum blocks.
2. It must run on commodity hardware (up to 16 cores) to ensure decentralization.
3. If no one beats our algorithm, the most comprehensive benchmarking report showing that we’re the best still wins **10,000 USDC**.
4. **500 USDC** to upto 10 People for just Posting that they ran the test and what their results are.
5. If someone does outperform us under these conditions, they will receive the full **30,000** **USDC** prize.
6. By end of campaign, if no one's able to beat SupraBTM Benchmarks, the person with the best data report will get **10,000 USDC.**

{% hint style="success" %}
The competition runs for one month (**Ending Nov 21st**), and payouts are made one month after verification. Let’s find out who has the fastest EVM parallel execution in the world!
{% endhint %}

### **System Setup Requirements**

We conducted a head-to-head benchmark of **SupraBTM** and **Monad’s 2PE** executor on Ethereum workloads.

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

#### Benchmarking Results Submission

Community members are already verifying benchmark results, post your submissions on X and tag us at [@SUPRA\_Labs](https://x.com/SUPRA_Labs)
