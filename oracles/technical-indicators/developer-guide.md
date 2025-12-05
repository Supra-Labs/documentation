# Developer Guide

This guide shows you how to integrate on-chain technical indicators (SMA, EMA, RSI) into your Supra L1 smart contracts. All indicators are calculated from Supra's DORA oracle price feeds and stored on-chain for immediate access.

**Supported in Phase 1:**

* **Assets**: BTC, SUPRA, ETH, SOL, BNB
* **Indicators**: SMA, EMA, RSI
* **Timeframes**: 5m, 15m, 1h, 4h, 1d
* **Supported Periods**:
  * SMA/EMA: 9, 20, 50, 200
  * RSI: 7, 14, 21

***

### Data Architecture

#### Candle Structure

Each candle contains OHLC data:

```move
TimestampedValue: Basic structure combining a value with its timestamp
    open: u128,      // First price when period starts
    high: u128,      // Highest price during period
    low: u128,       // Lowest price during period
    close: u128,     // Last price before period ends (used for all indicator calculations)
    timestamp: u64   // Unix timestamp in milliseconds
}
```

**Important**: All technical indicators use the **closing price** for calculations.

#### Storage Design

Technical indicator data is organized using **Ring Buffers**: Circular data structures that automatically overwrite old data when capacity is reached. This provides efficient storage for historical candles.

**Smart Tables**: Data organized by:

* Asset pair (e.g., BTC\_USDT)
* Timeframe (e.g., 5m, 1h, 1d)
* Indicator type (SMA, EMA, RSI)

**Historical Values**: Previous EMA values and RSI smoothed averages are stored for recursive calculations.

#### Data Availability

Indicators become available after the candle closes:

* **SMA**: Available after n periods close
* **EMA**: Available after n periods close (initial EMA = SMA, then exponentially smoothed)
* **RSI**: Available after n periods close (uses Wilder's smoothing)

**Example**: For a 5-minute timeframe with 20-period SMA, the indicator is first available after 100 minutes (20 candles × 5 minutes).

***

### Accessing Technical Indicators

#### Get SMA

```move
public fun get_sma(
    pair: u32,
    timeframe: u8,
    period: u64
): Option<u128>
```

**Returns**: The latest SMA value, or `None` if insufficient data.

**Example**:

```move
// Get 20-period SMA for BTC on 1-hour timeframe
let btc_sma = get_sma(1, 2, 20);

if (option::is_some(&btc_sma)) {
    let sma_value = option::extract(&mut btc_sma);
    // Use sma_value in your logic
}
```

#### Get EMA

```move
public fun get_ema(
    pair: u32,
    timeframe: u8,
    period: u64
): Option<u128>
```

**Returns**: The latest EMA value, or `None` if insufficient data.

**Example**:

```move
// Get 9-period and 50-period EMA for ETH on 15-minute timeframe
let fast_ema = get_ema(3, 1, 9);
let slow_ema = get_ema(3, 1, 50);

// Check for golden cross (fast EMA crosses above slow EMA)
if (option::is_some(&fast_ema) && option::is_some(&slow_ema)) {
    let fast = option::extract(&mut fast_ema);
    let slow = option::extract(&mut slow_ema);
    
    if (fast > slow) {
        // Bullish signal
    }
}
```

#### Get RSI

```move
public fun get_rsi(
    pair: u32,
    timeframe: u8,
    period: u64
): Option<u128>
```

**Returns**: The latest RSI value (0-100), or `None` if insufficient data.

**Example**:

```move
// Get 14-period RSI for SUPRA on 4-hour timeframe
let rsi = get_rsi(2, 3, 14);

if (option::is_some(&rsi)) {
    let rsi_value = option::extract(&mut rsi);
    
    if (rsi_value < 30) {
        // Oversold condition
    } else if (rsi_value > 70) {
        // Overbought condition
    }
}
```

***

### Asset Pair IDs

Map asset names to pair IDs in your contract:

```
```

### Timeframe IDs

```
```

***

### Querying Candle Data

Three query methods are available:

#### Method 1: Time Range Query

Get all candles within a specific time window.

```move
public fun get_candles_by_time_range(
    pair: u32,           // Asset pair ID
    timeframe: u8,       // Timeframe (5m=0, 15m=1, 1h=2, 4h=3, 1d=4)
    from_time: u64,      // Start timestamp (milliseconds)
    to_time: u64         // End timestamp (milliseconds)
): vector<Candle>
```

#### Method 2: Historical Count from Time

Get a specific number of candles backwards from a timestamp.

```move
public fun get_historical_candles(
    pair: u32,
    timeframe: u8,
    count: u64,          // Number of candles to retrieve
    from_time: u64       // Timestamp to count backwards from
): vector<Candle>
```

#### Method 3: Most Recent Candles

Get the latest n closed candles.

```move
public fun get_recent_candles(
    pair: u32,
    timeframe: u8,
    count: u64           // Number of recent candles
): vector<Candle>
```

***

### Common Patterns

#### Pattern 1: Trend Following Strategy

```move
public fun check_trend_signal(pair: u32): bool {
    let fast_ema = get_ema(pair, 2, 9);   // 9-period EMA, 1h
    let slow_ema = get_ema(pair, 2, 50);  // 50-period EMA, 1h
    
    if (option::is_some(&fast_ema) && option::is_some(&slow_ema)) {
        let fast = option::extract(&mut fast_ema);
        let slow = option::extract(&mut slow_ema);
        return fast > slow  // Bullish when fast > slow
    };
    false
}
```

#### Pattern 2: Overbought/Oversold Detection

```move
public fun check_rsi_extreme(pair: u32): u8 {
    let rsi = get_rsi(pair, 3, 14);  // 14-period RSI, 4h
    
    if (option::is_some(&rsi)) {
        let rsi_value = option::extract(&mut rsi);
        
        if (rsi_value < 30) return 1      // Oversold
        else if (rsi_value > 70) return 2  // Overbought
    };
    0  // Neutral
}
```

#### Pattern 3: Multi-Timeframe Confirmation

```move
public fun check_multi_timeframe_trend(pair: u32): bool {
    // Check 1-hour and 4-hour trends align
    let ema_1h = get_ema(pair, 2, 20);
    let ema_4h = get_ema(pair, 3, 20);
    let sma_1h = get_sma(pair, 2, 20);
    let sma_4h = get_sma(pair, 3, 20);
    
    // Both timeframes showing uptrend (EMA > SMA)
    if (option::is_some(&ema_1h) && option::is_some(&sma_1h) &&
        option::is_some(&ema_4h) && option::is_some(&sma_4h)) {
        
        let e1 = option::extract(&mut ema_1h);
        let s1 = option::extract(&mut sma_1h);
        let e4 = option::extract(&mut ema_4h);
        let s4 = option::extract(&mut sma_4h);
        
        return (e1 > s1) && (e4 > s4)
    };
    false
}
```

#### Pattern 4: Historical Analysis

```move
public fun analyze_recent_volatility(pair: u32): bool {
    // Get last 20 candles from 1-hour timeframe
    let candles = get_recent_candles(pair, 2, 20);
    let len = vector::length(&candles);
    
    if (len < 20) return false;
    
    // Calculate price range
    let mut highest = 0u128;
    let mut lowest = 340282366920938463463374607431768211455u128; // max u128
    
    let mut i = 0;
    while (i < len) {
        let candle = vector::borrow(&candles, i);
        if (candle.high > highest) highest = candle.high;
        if (candle.low < lowest) lowest = candle.low;
        i = i + 1;
    };
    
    let range = highest - lowest;
    let avg_price = (highest + lowest) / 2;
    let volatility_pct = (range * 100) / avg_price;
    
    volatility_pct > 10  // Return true if >10% volatility
}
```

***

### Edge Cases and Best Practices

#### Missing Candles

Candles may be missing if there were no trades during that period. The system only stores real candles with actual trades—no synthetic candles are created.

**Best Practice**: Always check if the returned option has data before using it.

```move
let indicator = get_sma(pair, timeframe, period);
if (option::is_none(&indicator)) {
    // Handle missing data case
    return default_value
};
```

#### Indicator Lag

All indicators are calculated **after** the candle closes. For real-time decision making, your contract executes based on the most recent closed candle, not the current forming candle.

**Example**: At 10:07 AM on a 5-minute timeframe, you can access indicators up to the 10:05 AM candle. The 10:05-10:10 candle is still forming.

#### Gas Optimization

Querying large amounts of historical data costs gas. Optimize by:

* Only querying the data you need
* Caching frequently accessed values
* Using the most efficient query method for your use case

```move
// ❌ Inefficient: Querying 200 candles every transaction
let all_candles = get_recent_candles(pair, 2, 200);

// ✅ Efficient: Use pre-calculated indicators
let sma_200 = get_sma(pair, 2, 200);
```

#### Decimal Handling

Price values are stored as `u128` with implied decimals. Check the specific pair's decimal precision in your implementation.

**Example**: If BTC\_USDT uses 8 decimals:

* Stored value: `4250000000000`
* Actual price: `42,500.00000000`

***

### Testing Your Integration

#### 1. Start with Recent Data

Test with `get_recent_candles()` to verify data access.

#### 2. Test Each Indicator

Independently verify SMA, EMA, and RSI calculations.

#### 3. Handle Edge Cases

Test behavior when indicators return `None`.

#### 4. Simulate Market Conditions

Test your logic across different market scenarios (trending, ranging, volatile).

#### 5. Gas Profiling

Measure gas costs of your queries and optimize as needed.

***

### Example: Complete Trading Bot

```move
module my_address::trading_bot {
    use supra_oracle::technical_indicators;
    
    public entry fun execute_strategy(pair: u32) {
        // Get multiple indicators
        let rsi = technical_indicators::get_rsi(pair, 2, 14);      // 1h RSI
        let fast_ema = technical_indicators::get_ema(pair, 2, 9);   // 1h fast EMA
        let slow_ema = technical_indicators::get_ema(pair, 2, 21);  // 1h slow EMA
        
        // Check all indicators are available
        if (option::is_none(&rsi) || 
            option::is_none(&fast_ema) || 
            option::is_none(&slow_ema)) {
            return  // Not enough data yet
        };
        
        // Extract values
        let rsi_val = option::extract(&mut rsi);
        let fast = option::extract(&mut fast_ema);
        let slow = option::extract(&mut slow_ema);
        
        // Trading logic
        if (fast > slow && rsi_val < 70) {
            // Bullish signal + not overbought
            execute_long_position(pair);
        } else if (fast < slow && rsi_val > 30) {
            // Bearish signal + not oversold
            execute_short_position(pair);
        }
    }
    
    fun execute_long_position(pair: u32) {
        // Your position logic here
    }
    
    fun execute_short_position(pair: u32) {
        // Your position logic here
    }
}
```

***
