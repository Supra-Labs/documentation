# How OVC Works

### From Oracle Data to Captured Revenue in Five Steps

OVC bridges oracle data with on-chain actions, turning price feeds into monetizable execution pathways. The system operates through a structured pipeline that moves from partner identification through to revenue distribution.

***

### Step 1: Target Compatible Protocols

OVC begins by identifying dApps that already integrate Supra's oracle data feeds (e.g., ETH/USDC, BTC/ETH). Ideal partners are protocols with collateralized positions that require liquidations to remain solvent.

This includes:

* **Lending markets** where borrowers post collateral against loans
* **Leveraged trading and perpetuals platforms** where positions are maintained against margin
* **Any protocol using Supra Oracles** that has liquidation mechanics

Supra currently provides these price feeds. OVC positions Supra not just as a data provider but as a value-generating partner with the protocols that consume its oracle infrastructure.

Integration requirements are minimal. Protocols need only grant index permissioning and agree to a revenue-sharing arrangement.

***

### Step 2: Real-Time Position Indexing

Once a protocol is onboarded, OVC deploys indexing infrastructure to continuously scan the partner's smart contracts. The system tracks:

* **Collateral ratios** across all active positions
* **Borrow amounts** relative to posted collateral
* **Health factors** indicating proximity to liquidation thresholds
* **Liquidation thresholds** as defined by the protocol's parameters

Liquidations are triggered when an oracle price update pushes a position below its required collateralization level. OVC monitors these conditions in real time, performing the heavy computational work off-chain before submitting results on-chain.

This off-chain computation, on-chain execution model ensures that the system operates efficiently without congesting the target chain.

***

### Step 3: Cryptographic Triggering

When a signed oracle price update flags a liquidation event, OVC initiates execution. The process follows three steps:

1. **Verify the price** — The oracle-signed price feed is cryptographically verified to ensure authenticity and freshness.
2. **Execute the liquidation** — The liquidation call is submitted to the protocol's smart contract.
3. **Capture the fee** — The liquidation fee (typically 5 to 15% of the liquidated collateral) is captured as part of the transaction.

This process is designed to be **frontrun-resistant**. Because Supra's decentralized oracle is the entity that dictates the current price and subsequently determines whether a position is underwater, OVC has a structural execution advantage over external MEV bots that must rely on observing the same data with additional latency.

***

### Step 4: Compound Execution

Execution is handled through OpenBlocks.ai, which provides the transaction infrastructure for OVC. Key capabilities include:

* **Atomic bundles** — The price verification, liquidation call, and fee capture are composed into a single bundled transaction. Either the entire sequence succeeds or it reverts.
* **No mempool exposure** — Transactions are submitted through private channels, eliminating the frontrunning vector that MEV bots exploit.
* **Optimized routing** — OpenBlocks.ai selects the most efficient execution path for each liquidation event.

The target capture rate is **90% of eligible liquidations**, which is multiple times higher than passive approaches run by third-party liquidation bots. This is achievable because Supra's oracle is the source of truth for the price data that determines liquidation eligibility.

***

### Step 5: Revenue Distribution

All captured liquidation fee revenue is aggregated into a shared pool and distributed according to a straightforward split:

| Recipient             | Share     |
| --------------------- | --------- |
| Protocol              | 66% (2/3) |
| Supra + OpenBlocks.ai | 33% (1/3) |

The expected outcome is a **3 to 5x increase in protocol revenue**, since liquidation fees are often an unrealized portion of TVL turnover that protocols are not currently capturing at all.

***

### Zero Developer Lift

A key design principle of OVC is that partner protocols do not need to build or maintain any MEV infrastructure. By adopting OVC, protocols avoid the cost and complexity of:

* **Running MEV bots** to compete for liquidation opportunities
* **Operating server infrastructure** for off-chain monitoring and execution
* **Conducting ongoing research** into MEV strategies and transaction ordering
* **Maintaining custom code** for liquidation systems across protocol upgrades

OVC is a plug-in monetization layer. Protocols focus on their product; Supra and OpenBlocks.ai handle execution and value capture.
