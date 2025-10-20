# SupraEVM Beta

SupraEVM Beta powered by **SupraBTM** (Supra's conflict specification-aware Block Transactional Memory) - a parallel transaction execution framework that achieves breakthrough performance in EVM transaction processing.

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

### Release Information:&#x20;

#### v1 (SupraBTM + RocksDB)

* Integration of iBTM with Hydrangea
* **Persistent EVM execution with SupraBTM + RocksDB**
* Binary-only release via Docker
* Historical Ethereum block dataset
* **Monad Comparison**: Final testing against Monad 2PE.

{% hint style="info" %}
**Important Notes for v1**:

* Binary-only release via Docker
* Developers cannot deploy or test their own smart contracts in this version, for Performancing benchmarking only.
{% endhint %}

#### v0 (In-Memory Beta)

* Stable and fully functional integration of iBTM with [Hydrangea](https://x.com/SUPRA_Labs/status/1933726886393221246)
* **In-memory EVM execution with SupraBTM**
* Binary-only release
* Historical Ethereum block dataset
* Performance benchmarking tools (**Sequential vs SupraBTM**)

{% hint style="info" %}
**Important Notes for v0**:\


* **In-Memory Execution Only**: This version operates entirely in-memory and does NOT include persistent storage.
* **No Custom Contract Testing**: Developers cannot deploy or test their own smart contracts in this version
{% endhint %}

***

### Minimum System Requirements

* Linux (Ubuntu 20.04+, Debian 11+)
* Windows with WSL2
* gdown (for dataset download)
* **Docker**: Required for running the pre-compiled binary
* **Networking**: 2 × 10 Gbps NICs
* **CPU**: AMD 4564P, 16 cores @ 4.5 GHz with 128 GB
* **Storage**: 2 × 480 GB NVMe + 2 × 1.9 TB NVMe
* AMD Ryzen 9950x, AMD Ryzen 7950x, AMD EPYC 4584PX, etc.

***

### SupraBTM Binary Installation & Setup

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
# Download the dataset 
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
 --cpuset-cpus="0-7" \
 -v ./data_bdf:/data \
 -v "$PWD/stats:/out" \
 rohitkapoor9312/ibtm-image:latest\
 --data-dir /data \
 --output-dir /out
```

{% hint style="info" %}
\--cpuset-cpus="0-7" creates affinity for cpu cores, and can be adjusted according to experiment.
{% endhint %}
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

### Monad 2PE vs SupraBTM Benchmarking

{% hint style="info" %}
This section continues forward after setting up SupraBTM Binary following above setup
{% endhint %}

{% stepper %}
{% step %}
**Setting up the Benchmark Environment**

```bash
# Create a directory to store all benchmark-related files & Navigate in it.
mkdir monad-bench
cd ./monad-bench
```
{% endstep %}

{% step %}
**Download the shell script**

```bash
# Download the shell script 
gdown 1JF9K7_nXMsptlTNHB9Jf7mV4SUwCsza-
```
{% endstep %}

{% step %}
**Make the script executable and run it.**

{% hint style="info" %}
Please note that the process may take some time as it clones the Monad execution repository and sets up the full benchmarking environment.
{% endhint %}

```bash
chmod +x monad_full_setup.sh
./monad_full_setup.sh monad
```

It will set up the Monad 2PE experiment, basically, it automates the entire setup process as follows:

1. **Repository Setup** – Clones the Monad Execution repository at a specific commit.
2. **Submodule Initialization** – Initializes all required submodules.
3. **Benchmark Data Preparation** – Downloads benchmark and test files needed for execution.
4. **Dataset Handling** – Downloads the dataset via gdown (skips if already present).
5. **Code Updates** – Applies local patches for error handling and CMake configuration updates.
6. **Docker Image Build** – Builds the docker image for the benchmark.
{% endstep %}

{% step %}
**For Execution move to the root directory to run the docker image**

```bash
cd ./monad

sudo docker run -it --rm \
--privileged \
-v $(pwd):/workspace \
monad-dev-image
```
{% endstep %}

{% step %}
**Above command will take you inside Docker, go to the workspace by running.**&#x20;

```bash
cd ./workspace

CC=gcc-15 CXX=g++-15 CFLAGS="-march=haswell" CXXFLAGS="-march=haswell" ASMFLAGS="-march=haswell" \
./scripts/configure.sh && ./scripts/build.sh

POOL_THREADS=8 POOL_FIBERS=2 taskset -c 0-7 ./build/test/ethereum_test/monad-ethereum-test --fork Prague
```

{% hint style="info" %}
You can change the number of threads by setting `POOL_THREADS` and `POOL_FIBERS` to compare performance at different configurations of 2PE.

Copies all benchmark results and log files from the container to the host machine under the `monad/monad_2pe_logs.txt` directory for analysis and record-keeping.
{% endhint %}
{% endstep %}
{% endstepper %}

#### Final Analysis SupraBTM vs Monad 2PE.

**Get logs for both iBTM and 2PE in one repository:**

```
./stats/
├── monad_2pe_logs.txt
└── execution_time.txt
```

**Download and move the python script into `./stats` and run**

```bash
# Download the python script in ./stats/
gdown 1shdmZWrZHgz0bxyjkn9efzL-soHuWRn7
# Run
python3 analysis.py execution_time.txt monad_2pe_logs.txt
```

**The script outputs both console and file summaries.** Results are stored in:&#x20;

* Consolidated file saved in output/consolidated\_th8\_fib2.csv → per-block metrics
* &#x20;summary/summary\_th8\_fib2.txt → aggregated statistics

#### Sample output

<figure><img src=".gitbook/assets/unknown.png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/unknown (1).png" alt=""><figcaption></figcaption></figure>

***

#### What's Next?

Community members are already verifying benchmark results. [Check them out here on X.](https://x.com/SUPRA_Labs/status/1977835502645772781)

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
