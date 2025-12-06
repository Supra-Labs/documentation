# Example 2:  Complete Trading Bot

This is a smaple trading bot made with technical indicators

```move
module my_address::trading_bot {
    use supra_oracle::supra_oracle_ti;
    use std::option;
    
    const ONE_HOUR: u64 = 3_600_000;
    const TOLERANCE: u64 = 1000;  // 10% missing candles tolerance
    
    public entry fun execute_strategy(pair_id: u32) {
        // Get multiple indicators
        let (rsi, _) = supra_oracle_ti::compute_rsi(
            pair_id,
            14,         // 14-period RSI
            ONE_HOUR,
            TOLERANCE
        );
        
        let (fast_ema, _, _) = supra_oracle_ti::compute_ema(
            pair_id,
            9,          // 9-period fast EMA
            ONE_HOUR,
            TOLERANCE
        );
        
        let (slow_ema, _, _) = supra_oracle_ti::compute_ema(
            pair_id,
            21,         // 21-period slow EMA
            ONE_HOUR,
            TOLERANCE
        );
        
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
            execute_long_position(pair_id);
        } else if (fast < slow && rsi_val > 30) {
            // Bearish signal + not oversold
            execute_short_position(pair_id);
        }
    }
    
    fun execute_long_position(pair_id: u32) {
        // Your position logic here
    }
    
    fun execute_short_position(pair_id: u32) {
        // Your position logic here
    }
}
```
