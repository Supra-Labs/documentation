# Supra EVM Beta Testnet

Supra EVM Beta Testnet powered by **SupraBTM** (Supra's conflict specification-aware Block Transactional Memory) - a parallel transaction execution framework that achieves breakthrough performance in EVM transaction processing.

#### Performance Highlights

* **\~4× speedup** over traditional sequential execution
* **\~1.5-1.7× speedup** over 2-phase optimistic parallel execution
* **Executes in \~50% less time** compared to known state-of-the-art
* Evaluated on 10,000 historical Ethereum blocks

**Detailed Technical Analysis**: For a comprehensive comparison of both approaches, read our study: [Supra vs Monad: Towards the Best Parallel Execution of the Ethereum Virtual Machine](https://supra.com/academy/supra-vs-monad-towards-the-best-parallel-execution-of-the-ethereum-virtual-machine/)

***

### What is SupraBTM?

**SupraBTM** is built on **iBTM (Intelligent Block Transactional Memory)**, a parallel transaction execution framework inspired by BlockSTM and designed to bring scalable, concurrent execution to Ethereum-like environments.

It leverages Software Transactional Memory (STM) principles, conflict analysis, and adaptive scheduling to achieve high throughput while preserving safety and determinism. Supra's iBTM adapts STM principles to the EVM context, allowing parallel execution of Ethereum transactions. It integrates seamlessly into the RISE-PEVM framework, extending its architecture to support intelligent dependency resolution and adaptive conflict management.

***

### Release Information: v0 (In-Memory Beta)

#### What's Included

✅ Stable and fully functional integration of iBTM with [Hydrangea](https://x.com/SUPRA_Labs/status/1933726886393221246)\
✅ In-memory EVM execution with SupraBTM\
✅ Binary-only release\
✅ Historical Ethereum block dataset (10,000 blocks)\
✅ Performance benchmarking tools (Sequential vs SupraBTM)

{% hint style="info" %}
**Important Notes for v0**:\


* **In-Memory Execution Only**: This version operates entirely in-memory and does NOT include persistent storage.
* **Binary Release**: This is a binary-only public release
* **Benchmarking Focus**: This release is for demonstrating the performance of SupraBTM over Sequential execution on in-memory operations
* **No Custom Contract Testing**: Developers cannot deploy or test their own smart contracts in this version
{% endhint %}

***

### Minimum System Requirements

**Docker**: Required for running the pre-compiled binary

**Operating Systems Supported:**

* Linux (Ubuntu 20.04+, Debian 11+)
* macOS (Intel and Apple Silicon)
* Windows with WSL2
* gdown (for dataset download)
* **CPU**: 8/8+ cores recommended for optimal performance with 32 GB

***

### Installation & Setup

{% stepper %}
{% step %}
**Create Working Directory**

```bash
# Create a directory for the benchmark
mkdir supraevmbeta
cd supraevmbeta
```
{% endstep %}

{% step %}
**Download the Dataset**

```bash
# Download the dataset (~14 GB)
gdown --id 1zgP48T3IAmg5yDkaN4h9RaD09klMN5QF

# Extract it
unzip ./data_bdf.zip
```

{% hint style="info" %}
**This will take time depending on your hardware specs as well as internet connection.**
{% endhint %}
{% endstep %}

{% step %}
**Create Output Directory**

```bash
mkdir stats
```
{% endstep %}

{% step %}
**Run the Benchmark by pulling the docker image!!**

```bash
docker run --rm \
  -v ./data_bdf:/data \
  -v "$PWD/stats:/out" \
  davidsupra/ibtm:latest \
  --data-dir /data \
  --output-dir /out
```
{% endstep %}
{% endstepper %}

#### Output

Each record contains execution logs for both **sequential** and **iBTM** runs, including:

* Block number
* Block size (number of transactions)
* Sequential execution time
* iBTM execution time

**What you'll see:** The benchmark will process each block and show progress:

```
================================================================
Block Number: "14000011"
================================================================
Block Number: "14000018"
================================================================
Block Number: "14000022"
...
```

#### View Your Results

```bash
cat ./stats/execution_time.txt
```

**Sample output:**

```
Block_num	Concurrency_level	Block_size	Seq. Time	iBTM Time
14000011	12	                99	        3.850743ms      1.724187ms
14000018	12	                115	        2.135975ms	748.909µs
14000022	12	                339	        7.706865ms	2.026971ms
```

#### What's Next?

Community members are already verifying benchmark results. [Check them out here on X.](https://x.com/SUPRA_Labs)

{% hint style="success" %}
Join the conversation. **$2,500 is on the line for the first dev who proves us wrong.**
{% endhint %}

***

### Core Design Principles

**Conflict-Aware Parallelism**: Transactions with known access specifications undergo static conflict analysis to detect dependencies early.

**Optimistic Execution**: Transactions lacking access metadata are executed optimistically using lightweight STM.

**Adaptive Execution**: The scheduler dynamically switches between sequential, optimistic, and conflict-aware modes based on block characteristics.

**Conflict Analyzer**: The current version uses a simplified analyzer. Upcoming versions will include:

* Full conflict graph extraction
* Fine-grained dependency tracking
* Dynamic adaptive schedulers (Sequential, iBTM, dBTM and oBTM)

**Read more in detail about our Research:** [https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.AFT.2025.29](https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.AFT.2025.29)

***

### System Architecture

```
┌───────────────────────────────────────────────────┐
│ Block Input                                       │
│ (Transactions, Metadata)                          │
└─────────────────────────┬─────────────────────────┘
                          │
                          ▼
┌───────────────────────────────────────────────────┐
│ Access Specification Parser                       │
│ → Detects known read/write sets                   │
│ → Annotates transactions with dependency hints    │
└─────────────────────────┬─────────────────────────┘
                          │
                          ▼
┌───────────────────────────────────────────────────┐
│ Conflict Analyzer (iBTM)                          │
│ → Builds dependency graph                         │
│ → Tags dependent transactions for ordered execution│
└─────────────────────────┬─────────────────────────┘
                          │
                          ▼
┌───────────────────────────────────────────────────┐
│ Executor (Parallel STM Engine)                    │
│ → Executes independent transactions concurrently  │
│ → Aborts & replays on conflict detection          │
│ → Commits with deterministic ordering             │
└───────────────────────────────────────────────────┘
```

***
