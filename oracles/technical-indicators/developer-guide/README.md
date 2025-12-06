# Developer Guide

This guide shows you how to integrate on-chain technical indicators (SMA, EMA, RSI) into your Supra L1 smart contracts. All indicators are calculated from Supra's DORA oracle price feeds and stored on-chain for immediate access.



**Supported in Phase 1:**

* **Assets**: BTC\_USDT, SUPRA\_USDT , ETH\_USDT, SOL\_USDT, BNB\_USDT
* **Indicators**: SMA, EMA, RSI
* **Timeframes**: 5m, 15m, 1h, 4h, 1d
* **Supported Periods**:
  * SMA/EMA: 9, 20, 50, 200
  * RSI: 7, 14, 21

***

### Price Candles Data Structure

**TimestampedValue**: Basic structure combining a value with its timestamp

```move
struct TimestampedValue has copy, drop, store {
    value: u128,        // Price or other numerical value
    timestamp: u64      // Unix timestamp in milliseconds
}
```

**Candle**: OHLC candlestick data structure

```move
struct Candle has copy, drop, store {
    low: TimestampedValue,      // Lowest price in the time period
    high: TimestampedValue,     // Highest price in the time period
    open: TimestampedValue,     // Opening price of the time period
    close: TimestampedValue     // Closing price of the time period
}
```

**CandleInfo**: Complete candle information returned by queries

```move
struct CandleInfo has copy, drop {
    startTime: u64,     // Timestamp marking the beginning of the candle
    endTime: u64,       // Timestamp marking the end of the candle
    candle: Candle      // The aggregated candle data
}
```

**Important**: All technical indicators use the **closing price** for calculations. All timestamps are in milliseconds.

#### Data Availability

Indicators become available after the candle closes. **Example**: For a 5-minute timeframe with 20-period SMA, the indicator is first available after 100 minutes (20 candles × 5 minutes).

***

### Querying Candle Data

#### Method 1: Get Latest Candles

Get the most recent "n" closed candles.&#x20;

```move
#[view]
public fun get_latest_candles(
    num_of_candles: u64,      // Number of recent candles to return
    pair_id: u32,             // Unique identifier for the trading pair
    candle_duration: u64      // Candle duration in milliseconds
): vector<CandleInfo>
```

**Returns**: Vector of CandleInfo, ordered from newest to oldest.

#### Method 2: Get Latest Candles from Specific Time

Get candles generated after a specific timestamp.

```move
#[view]
public fun get_latest_candles_from_specific_time(
    num_of_candles: u64,      // Number of candles requested
    pair_id: u32,             // Unique trading pair identifier
    candle_duration: u64,     // Candle duration in milliseconds
    start_timestamp: u64      // Lower bound (inclusive), in milliseconds
): vector<CandleInfo>
```

**Returns**: Vector of CandleInfo whose close times are >= start\_timestamp.

**Example**:

```move
// Get candles for ETH on 15-min timeframe after a specific time
let start_time = 1704067200000; // Jan 1, 2024 00:00:00 UTC
let candles = supra_oracle_ti::get_latest_candles_from_specific_time(
    50,           // num_of_candles
    3,            // pair_id (ETH)
    900_000,      // candle_duration (15 min)
    start_time
);
```

***

### Reading Technical Indicators

Please refer below links or specific Indicator subpage for detailed examples.

* [Simple Moving Average(SMA) ](relative-strength-index-rsi.md)
* [Exponential Moving Average(EMA)](exponential-moving-average-ema.md)
* [Relative Strength Index(RSI)](relative-strength-index-rsi.md)

***

### Asset Pair IDs

Map asset names to Supra oracle pair IDs in your contract:

| Price Pair  | Pair ID |
| ----------- | ------- |
| BTC\_USDT   | 0       |
| SUPRA\_USDT | 500     |
| ETH\_USDT   | 1       |
| SOL\_USDT   | 10      |
| BNB\_USDT   | 49      |

### Candle Durations

Use these values for `candle_duration` parameter (in milliseconds):

| Timeframe | Duration ( in milliseconds) |
| --------- | --------------------------- |
| 5 mins    | 300\_000                    |
| 15 mins   | 900\_000                    |
| 1 hour    | 3\_600\_000                 |
| 4 hours   | 14\_400\_000                |
| 1 day     | 86\_400\_000                |



***

### Edge Cases and Best Practices

#### Missing Candles

Candles may be missing for several reasons:

1. Oracle feeds down
2. Supra network not producing blocks
3. No active trades during the period

**Supra's Approach**: Contract logic will strictly adhere to the users time parameters and will check with the missing candles tolerance percentage user has specified.   If the requested calculation period has more than the specified percentage of missing candles query returns `none` .  Please Set tolerance based on your strategy's sensitivity to data gaps. Always check if the returned option has data before using it.

The `missing_candles_tolerance_percentage` parameter controls how the system handles gaps and uses two-decimal fixed-point precision.

**Example**: `1000`  means 10.00%  and `5000` means 50%  tolerance for missing candles.

```move
let sma = supra_oracle_ti::compute_sma(pair_id, 20, ONE_HOUR, 1000);
if (option::is_none(&sma)) {
    // Handle missing data case - insufficient history or too many missing candles
    return default_value
};
```

#### Understanding Return Values

**SMA**: Returns a single `Option<u128>` with the latest SMA value.

**EMA**: Returns a tuple `(Option<u128>, Option<u64>, Option<u64>)`:

* First value: Latest EMA
* Second value: Number of missing candles since last update
* Third value: Total candles from first to latest

**RSI**: Returns a tuple `(Option<u128>, Option<u64>)`:

* First value: RSI value (0-100 range, scaled)
* Second value: Number of missing candles in the window

**Example**:

```move
let (ema_value, missing_candles, total_candles) = supra_oracle_ti::compute_ema(
    1, 20, ONE_HOUR, 1000
);

if (option::is_some(&missing_candles)) {
    let missing = option::extract(&mut missing_candles);
    if (missing > 5) {
        // Handle case with significant data gaps
    }
}
```

#### Indicator Lag

All indicators are calculated **after** the candle closes. For real-time decision making, your contract executes based on the most recent closed candle, not the current forming candle.

**Example**: At 10:07 AM on a 5-minute timeframe, you can access indicators up to the 10:05 AM candle. The 10:05-10:10 candle is still forming.

#### Period Validation

Only specific periods are supported:

* **SMA/EMA**: 9, 20, 50, 200
* **RSI**: 7, 14, 21

Requesting unsupported periods will return `none`.

```move
// ✅ Valid
let sma = supra_oracle_ti::compute_sma(1, 20, ONE_HOUR, 1000);

// ❌ Invalid - period 25 not supported
let sma = supra_oracle_ti::compute_sma(1, 25, ONE_HOUR, 1000);  // Returns none
```

#### Gas Optimization

Querying large amounts of historical data costs gas. Optimize by:

* Only querying the data you need
* Caching frequently accessed values
* Using pre-calculated indicators instead of raw candles when possible

```move
// ❌ Inefficient: Querying 200 candles every transaction
let all_candles = supra_oracle_ti::get_latest_candles(200, pair_id, ONE_HOUR);

// ✅ Efficient: Use pre-calculated indicators
let sma_200 = supra_oracle_ti::compute_sma(pair_id, 200, ONE_HOUR, 1000);
```

#### Decimal Handling

All returned values (SMA, EMA, RSI) are scaled by `DECIMAL_BUFFER` for precision. Make sure to handle the scaling factor in your calculations.

**Example**: If `DECIMAL_BUFFER = 10^8`:

* Returned SMA value: `4250000000000`
* Actual SMA: `42,500.00000000`

Check the specific scaling used in your deployment and adjust calculations accordingly.

***

### Testing Your Integration

1\. Start with Recent Data :  Test with `get_recent_candles()` to verify data access.

2\. Test Each Indicator: Independently verify SMA, EMA, and RSI calculations.

3\. Handle Edge Cases: Test behavior when indicators return `None`.

4\. Simulate Market Conditions :Test your logic across different market scenarios (trending, ranging, volatile).

5\. Gas Profiling: Measure gas costs of your queries and optimize as needed.
