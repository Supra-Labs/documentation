# The Problem

### Protocols Leave Money on the Table Every Day

In the current DeFi landscape, oracles provide the price data that triggers liquidations, but the protocols consuming that data rarely capture any of the resulting value. Instead, that value leaks to external actors.

#### Liquidation Fees Are Captured by MEV Bots

When a position becomes undercollateralized and eligible for liquidation, external MEV bots race to execute the liquidation call and claim the associated fee. These bots monitor mempools, frontrun transactions, and extract value that rightfully belongs in the protocol's revenue stack.

#### Oracles Trigger Liquidations, but Protocols Don't Benefit

The oracle is the entity that determines whether a position is underwater. It provides the signed price update that moves a health factor below the liquidation threshold. Despite being the catalyst for the liquidation event, neither the oracle provider nor the protocol captures the fee. The value flows entirely to third-party bots.

#### No In-House MEV Execution

Building an in-house liquidation system is a significant engineering lift. It requires deep expertise in off-chain indexing, transaction bundling, oracle integration, and MEV-aware execution. Most DeFi teams are focused on core product development and lack the resources or specialization to compete with dedicated MEV operations.

#### The Result: Revenue Leakage Every Day

For protocols with meaningful TVL, liquidation events happen regularly. Annual liquidation volume typically ranges from 3 to 7% of TVL, with liquidation fees in the 5 to 15% range per event. That adds up to a substantial recurring revenue stream that protocols are currently forfeiting entirely.

#### The Opportunity

OVC flips this dynamic. By combining Supra's oracle infrastructure with coordinated execution, protocols can convert liquidation events from a cost center into a revenue line:

* **Liquidation fees become recurring revenue** rather than value extracted by bots
* **3 to 7% annual liquidation volume** across protocol TVL represents a significant addressable pool
* **5 to 15% liquidation fees** on each event create meaningful per-event revenue
* **3 to 5x potential revenue increase** for protocols that adopt OVC, based on conservative modeling
