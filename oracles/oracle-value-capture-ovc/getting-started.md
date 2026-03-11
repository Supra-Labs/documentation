# Getting Started

### Adoption Path for Protocols

OVC is designed for fast onboarding with minimal integration effort. The adoption path moves from initial conversation through to live revenue capture in a structured sequence.

***

### Step-by-Step Onboarding

#### Step 1: Intro Call

An introductory conversation to understand your protocol's architecture, oracle usage, and liquidation mechanics. This helps us assess fit and estimate the revenue potential specific to your TVL and market exposure.

#### Step 2: Free Exposure Audit

We conduct a no-cost audit of your protocol's current liquidation exposure. This analysis quantifies how much value is currently being captured by external MEV bots and estimates the revenue OVC would recover. The audit uses real on-chain data from your protocol's smart contracts.

#### Step 3: Approvals

Internal review and sign-off on the OVC partnership. This covers the revenue-sharing terms, data access scope, and operational responsibilities.

#### Step 4: Index Permissions

Your protocol grants OVC read access to the relevant smart contract state (collateral ratios, borrow amounts, health factors, liquidation thresholds). This is the primary technical integration point, and it is lightweight by design.

#### Step 5: Revenue Share Agreement

Formalize the revenue-sharing terms. The standard split is 66% to the protocol and 33% to Supra + OpenBlocks.ai. Custom arrangements may be available depending on the partnership scope.

#### Step 6: Deploy

OVC's indexing and execution infrastructure goes live against your protocol's contracts. From this point, eligible liquidations are captured and fees are accumulated.

#### Step 7: Dashboard

Access to real-time monitoring and analytics via OpenBlocks.ai. The dashboard provides visibility into captured liquidations, fee revenue, capture rates, and historical performance.

#### Step 8: Optional SupraEVM Migration

For protocols interested in maximizing their economics, SupraEVM migration is available as an optional next step. On SupraEVM, AutoLiquidations are native and protocols retain 100% of fees with no revenue share. See The SupraEVM Advantage for details.

***

### What You Need to Provide

* Read access to relevant smart contract state (index permissioning)
* Agreement on revenue-sharing terms
* A technical contact for onboarding coordination

That is the full scope of integration effort on the protocol side. OVC handles everything else: indexing infrastructure, execution logic, MEV protection, and revenue distribution.

***

### Get Started

If you are interested in an OVC partnership or want to see your protocol's liquidation exposure audit, reach out to the Supra team to schedule an intro call.
