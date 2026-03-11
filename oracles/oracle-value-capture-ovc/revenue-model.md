# Revenue Model

### How OVC Generates Protocol Revenue

OVC converts liquidation events into a recurring revenue stream for partner protocols. The economics are straightforward: liquidation fees that previously leaked to MEV bots are captured and shared between the protocol and the OVC infrastructure providers.

***

### Revenue Split

| Recipient             | Share     | Role                                                         |
| --------------------- | --------- | ------------------------------------------------------------ |
| Protocol              | 66% (2/3) | Receives the majority share of all captured liquidation fees |
| Supra + OpenBlocks.ai | 33% (1/3) | Provides oracle infrastructure, indexing, and execution      |

This split applies to all liquidation fee revenue captured through OVC. The protocol receives its share without needing to build, operate, or maintain any of the underlying infrastructure.

***

### Worked Example

Consider a lending protocol with the following parameters:

| Parameter                 | Value                        |
| ------------------------- | ---------------------------- |
| Total Value Locked (TVL)  | $100M                        |
| Annual liquidation volume | 5% of TVL                    |
| Average liquidation fee   | 10% of liquidated collateral |

**Calculation:**

$100M TVL x 5% liquidation volume x 10% fee = **$500K in total annual liquidation fees**

With OVC's 66/33 revenue split, the protocol receives approximately **$330K per year** in previously uncaptured revenue.

This is a conservative estimate. During periods of high volatility, liquidation volume increases significantly, and the captured fees scale accordingly.

***

### Why This Revenue Matters

Most DeFi protocols generate revenue through borrowing spreads, trading fees, or protocol-owned liquidity. Liquidation fees represent an entirely separate revenue category that is proportional to TVL but typically flows to external actors.

For a protocol that currently earns modest fee revenue, adding OVC-captured liquidation fees can produce a **3 to 5x increase in total protocol revenue**. This revenue is recurring, scales with TVL, and requires no additional product development from the protocol team.

***

### On SupraEVM: 100% Fee Retention

For protocols that migrate to SupraEVM, the economics improve further. On SupraEVM, AutoLiquidations are a native system-level feature. Protocols on SupraEVM retain **100% of liquidation fees** with no revenue share required, since OVC operates as built-in infrastructure rather than an external service.

See The SupraEVM Advantage for details.
