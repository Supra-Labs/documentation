# Technical Indicators

### Introduction

Technical indicators are mathematical calculations based on historical price data that help traders and automated systems analyze market trends, momentum, and potential price reversals. Traditionally used in off-chain trading platforms, Supra L1 now brings these powerful analytical tools directly on-chain, enabling smart contracts to make data-driven decisions in real-time.<br>

### Why Technical Indicators On-Chain?

Bringing technical indicators on-chain unlocks entirely new possibilities for decentralized applications:

**Trustless Automation**: Smart contracts can execute trading strategies based on verified, tamper-proof technical signals without relying on external services or centralized platforms.

**Composability**: Any protocol on Supra L1 can integrate these indicators into their logic, creating sophisticated DeFi products that react to market conditions autonomously.

**Transparency**: All calculations and historical data are verifiable on-chain, eliminating the "black box" problem of traditional trading algorithms.

**Real-Time Decision Making**: dApps can access fresh technical indicators calculated directly from Supra's oracle price feeds, with data freshness in sub-second ranges.<br>

### Supported Assets and Timeframes

**Phase 1 Coverage:**

* **Assets**: BTC, SUPRA, ETH, SOL, BNB
* **Indicators**: SMA, EMA, RSI
* **Timeframes**: 5 minutes, 15 minutes, 1 hour, 4 hours, 1 day
* **Configurable Periods**: Users can configure indicators for different periods (e.g., 9, 20, 50, 200) based on available timeframe data

**Data Source**: All calculations use the closing price of each timeframe. The closing price is determined as the last verified price available before the period closes, sourced directly from Supra's DORA oracle feeds.<br>

### Edge Cases and Data Handling

#### Missing Candles

Candles may be missing for several reasons:

1. Oracle fees down
2. Supra network not producing blocks
3. No active trades during the period

**Supra's Approach**: The on-chain logic only considers candles with actual trades. No backfilling or creation of synthetic candles occurs. Technical indicators are calculated based on available real candles, not constrained by calendar time.

**Example**: If calculating a 5-period SMA and two candles are missing from the most recent timeframe, the logic goes further back in time to collect the last 5 actual candles with trades.



### Looking Ahead

Phase 1 establishes the foundation with SMA, EMA, and RSI. Future phases may include:

* **Bollinger Bands**: Volatility-based bands around moving averages
* **MACD**: Moving Average Convergence Divergence for trend momentum
* **Stochastic Oscillator**: Momentum indicator comparing closing price to price range



The vision is to create a comprehensive technical analysis layer on Supra L1, empowering developers to build sophisticated, autonomous trading systems and DeFi protocols that react intelligently to market conditions—all without leaving the blockchain.
