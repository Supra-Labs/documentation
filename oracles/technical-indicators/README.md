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

* **Assets**: BTC\_USDT, SUPRA\_USDT , ETH\_USDT, SOL\_USDT, BNB\_USDT
* **Indicators**: SMA, EMA, RSI
* **Timeframes**: 5m, 15m, 1h, 4h, 1d
* **Supported Periods**:
  * SMA/EMA: 9, 20, 50, 200
  * RSI: 7, 14, 21

**Data Source**: All calculations use the closing price of each timeframe. The closing price is determined as the last verified price available before the period closes, sourced directly from Supra's DORA oracle feeds.

### Looking Ahead

Phase 1 establishes the foundation with SMA, EMA, and RSI. Future phases may include:

* **Bollinger Bands**: Volatility-based bands around moving averages
* **MACD**: Moving Average Convergence Divergence for trend momentum
* **Stochastic Oscillator**: Momentum indicator comparing closing price to price range



The vision is to create a comprehensive technical analysis layer on Supra L1, empowering developers to build sophisticated, autonomous trading systems and DeFi protocols that react intelligently to market conditions—all without leaving the blockchain.
