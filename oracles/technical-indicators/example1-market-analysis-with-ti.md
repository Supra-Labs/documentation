# Examples : Market Analysis with TI

Below examples have been added just to showcase what could be potentially done with this new form of information on chain. Possibilities are endless.  This is just a start.<br>

#### Example 1: Trend Signaling

```move
const ONE_HOUR: u64 = 3_600_000;
const TOLERANCE_10_PCT: u64 = 1000;

public fun check_trend_signal(pair_id: u32): bool {
    let (fast_ema, _, _) = supra_oracle_ti::compute_ema(
        pair_id,
        9,              // 9-period EMA
        ONE_HOUR,
        TOLERANCE_10_PCT
    );
    
    let (slow_ema, _, _) = supra_oracle_ti::compute_ema(
        pair_id,
        50,             // 50-period EMA
        ONE_HOUR,
        TOLERANCE_10_PCT
    );
    
    if (option::is_some(&fast_ema) && option::is_some(&slow_ema)) {
        let fast = option::extract(&mut fast_ema);
        let slow = option::extract(&mut slow_ema);
        return fast > slow  // Bullish when fast > slow
    };
    false
}
```

#### Example 2: Overbought/Oversold Detection

```move
const FOUR_HOURS: u64 = 14_400_000;
const TOLERANCE_5_PCT: u64 = 500;

public fun check_rsi_extreme(pair_id: u32): u8 {
    let (rsi, _) = supra_oracle_ti::compute_rsi(
        pair_id,
        14,             // 14-period RSI
        FOUR_HOURS,
        TOLERANCE_5_PCT
    );
    
    if (option::is_some(&rsi)) {
        let rsi_value = option::extract(&mut rsi);
        
        if (rsi_value < 30) return 1      // Oversold
        else if (rsi_value > 70) return 2  // Overbought
    };
    0  // Neutral
}
```

#### Example 3: Multi-Timeframe Confirmation

```move
const ONE_HOUR: u64 = 3_600_000;
const FOUR_HOURS: u64 = 14_400_000;
const TOLERANCE: u64 = 1000;

public fun check_multi_timeframe_trend(pair_id: u32): bool {
    // Check 1-hour and 4-hour trends align
    let (ema_1h, _, _) = supra_oracle_ti::compute_ema(
        pair_id, 20, ONE_HOUR, TOLERANCE
    );
    let (ema_4h, _, _) = supra_oracle_ti::compute_ema(
        pair_id, 20, FOUR_HOURS, TOLERANCE
    );
    let sma_1h = supra_oracle_ti::compute_sma(
        pair_id, 20, ONE_HOUR, TOLERANCE
    );
    let sma_4h = supra_oracle_ti::compute_sma(
        pair_id, 20, FOUR_HOURS, TOLERANCE
    );
    
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

#### Example 4: Historical Analysis

```move
const ONE_HOUR: u64 = 3_600_000;

public fun analyze_recent_volatility(pair_id: u32): bool {
    // Get last 20 candles from 1-hour timeframe
    let candles = supra_oracle_ti::get_latest_candles(
        20,         // num_of_candles
        pair_id,
        ONE_HOUR
    );
    
    let len = vector::length(&candles);
    if (len < 20) return false;
    
    // Calculate price range
    let mut highest = 0u128;
    let mut lowest = 340282366920938463463374607431768211455u128; // max u128
    
    let mut i = 0;
    while (i < len) {
        let candle_info = vector::borrow(&candles, i);
        let candle = &candle_info.candle;
        
        if (candle.high.value > highest) highest = candle.high.value;
        if (candle.low.value < lowest) lowest = candle.low.value;
        i = i + 1;
    };
    
    let range = highest - lowest;
    let avg_price = (highest + lowest) / 2;
    let volatility_pct = (range * 100) / avg_price;
    
    volatility_pct > 10  // Return true if >10% volatility
}
```
