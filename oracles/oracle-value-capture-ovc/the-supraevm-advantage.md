# The SupraEVM Advantage

### From Off-Chain Add-On to Native Infrastructure

OVC works today as an off-chain execution layer on any chain where Supra Oracles are integrated. But for protocols seeking the best possible economics and the deepest integration, SupraEVM offers a fundamentally better architecture.

SupraEVM is Supra's high-performance, EVM-compatible execution environment designed specifically for DeFi scalability. On SupraEVM, OVC is not an external service. It is a system-level feature built into the chain itself.

***

### Native AutoLiquidations

On SupraEVM, liquidations are triggered automatically at the protocol layer. There is no need for off-chain monitoring, no external bots, and no bundled transaction coordination. When a position crosses its liquidation threshold, the system handles it natively.

This eliminates the entire class of MEV extraction that exists on other chains, where bots compete to execute liquidation calls through mempool observation and frontrunning.

***

### 100% Fee Retention

Because AutoLiquidations are native to SupraEVM, there is no external execution layer taking a cut. Protocols on SupraEVM retain **100% of all liquidation fees**. There is no revenue share with Supra or OpenBlocks.ai for liquidation execution on SupraEVM.

Compare this to the OVC model on external chains, where the split is 66% protocol / 33% Supra + OpenBlocks. On SupraEVM, the protocol keeps everything.

***

### No Off-Chain Infrastructure

On other chains, OVC requires off-chain indexing, monitoring, and execution infrastructure operated by Supra and OpenBlocks.ai. On SupraEVM, this entire layer is unnecessary. The chain's native oracle integration means that price updates and liquidation triggers are part of the consensus process itself.

This reduces operational complexity, eliminates potential points of failure, and removes latency between price updates and liquidation execution.

***

### Additional SupraEVM Benefits

Beyond the OVC-specific advantages, SupraEVM provides:

**Low Gas Costs** — Transaction fees are minimized through Supra's high-throughput architecture, making frequent liquidation execution economically viable even for smaller positions.

**High Throughput** — SupraEVM's parallel execution model handles high transaction volumes without congestion, critical during market volatility when liquidation events spike.

**Oracle-Native Security** — Price data is integrated at the chain level rather than delivered through external contracts. This eliminates oracle manipulation vectors that exist when price feeds are consumed through intermediary smart contracts.

**Simple Maintenance** — With no off-chain infrastructure to manage, protocol teams face a significantly reduced operational burden compared to running OVC on external chains.

***

### OVC as an On-Ramp

OVC on external chains serves as a practical introduction to the Supra ecosystem. Protocols can begin capturing liquidation revenue immediately on their current chain, experience the value of Supra's oracle infrastructure firsthand, and then evaluate SupraEVM migration when the timing is right.

The adoption path from OVC to SupraEVM is designed to be incremental, not disruptive. Migration to SupraEVM is always optional, and OVC continues to deliver value on any supported chain.
