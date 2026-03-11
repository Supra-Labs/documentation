# Oracle Value Capture (OVC)

### Convert Oracle-Triggered Liquidations into Guaranteed Revenue

Oracle Value Capture (OVC) is Supra's mechanism for enabling DeFi protocols to reclaim liquidation fees that are currently captured by external MEV bots. By combining Supra's real-time oracle infrastructure with coordinated execution via OpenBlocks.ai, OVC transforms passive price feeds into active revenue engines.

OVC addresses a core inefficiency in DeFi: oracles trigger liquidations, yet the protocols relying on those oracles rarely capture the resulting value. Liquidation fees, typically 5 to 15% of liquidated collateral, flow instead to third-party MEV bots that frontrun the opportunity.

With OVC, that value is reclaimed, shared equitably with the protocol, and channeled through a system designed to be atomic, front run resistant, and operationally zero-lift for partner teams.

#### What OVC Delivers

**Reclaim Liquidation Fees** — Capture the revenue that MEV bots currently siphon from your protocol's liquidation events.

**Stop MEV Leakage** — Supra's signed price updates and bundled execution provide a structural advantage over passive liquidation approaches.

**Unlock New Revenue** — Liquidation fees represent a recurring, previously untapped income stream proportional to your protocol's TVL.

#### Who Is OVC For?

OVC is designed for any protocol that relies on oracle price feeds to trigger liquidations. Ideal partners include:

* Lending markets with collateralized positions
* Leveraged trading and perpetuals platforms
* Any protocol currently integrating Supra Oracles

#### How It Works (Summary)

OVC operates through a five-step pipeline: identify compatible protocols, index positions in real time, trigger liquidations cryptographically using signed oracle data, execute via atomic bundled transactions, and share the resulting revenue with the protocol. For a detailed technical walkthrough, see How OVC Works.

#### Supra x OpenBlocks.ai

OVC is a joint initiative between Supra and OpenBlocks.ai. Supra provides the oracle infrastructure and cryptographic price feeds. OpenBlocks.ai handles the execution layer, including atomic transaction bundling, optimized routing, and mempool-free delivery.
