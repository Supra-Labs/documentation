# Simple Moving Average (SMA)

**What It Is**\
SMA calculates the arithmetic mean of closing prices over a specified number of periods. It smooths out price volatility to reveal underlying trends.

**Formula**

```
SMA = (P₁ + P₂ + ... + Pₙ) / n
```

Where:

* `n` = number of periods (e.g., 9, 20, 50, 200)
* `P` = closing price of each period<br>

**How It Works**\
SMA gives equal weight to all prices in the calculation window. As new prices come in, the oldest price drops out of the calculation, creating a "rolling" average.<br>

**Example Calculation**

Given this price data:

<table><thead><tr><th width="207.10546875">Period</th><th>Closing Price</th></tr></thead><tbody><tr><td>1</td><td>20</td></tr><tr><td>2</td><td>22</td></tr><tr><td>3</td><td>21</td></tr><tr><td>4</td><td>23</td></tr><tr><td>5</td><td>24</td></tr><tr><td>6</td><td>26</td></tr></tbody></table>

**5-Period SMA on Period 5:**

```
SMA₅ = (20 + 22 + 21 + 23 + 24) / 5 = 110 / 5 = 22.0
```

_Note: This can only be calculated after Period 5 closes, meaning the result is available on Period 6_

**5-Period SMA on Period 6:**

```
SMA₅ = (22 + 21 + 23 + 24 + 26) / 5 = 116 / 5 = 23.2
```

**Key Characteristics**

* **Lag**: SMA reacts slowly to sudden price changes because it averages all periods equally
* **Smoothness**: Provides clean trend lines with minimal noise
* **Minimum Data**: Requires n periods of data before calculation is possible<br>

### Reading Simple Moving Average (SMA) on Supra L1

```move
#[view]
public fun compute_sma(
    pair_id: u32,                                  // Unique identifier of the trading pair
    period: u64,                                   // Number of candles to average [9, 20, 50, 200]
    candle_duration: u64,                          // Duration in milliseconds
    missing_candles_tolerance_percentage: u64      // Max missing candles (two-decimal fixed-point)
): Option<u128>
```

**Returns**:

* `some(sma)` if the indicator can be computed (scaled by DECIMAL\_BUFFER)
* `none` if insufficient history, invalid period, or tolerance exceeded

**Parameters**:

* `missing_candles_tolerance_percentage`: Expressed with two-decimal precision (e.g., 5000 = 50.00%, 1000 = 10.00%)

**Example**:

```move
use supra_oracle::supra_oracle_ti;
use std::option;

// Get 20-period SMA for BTC on 1-hour timeframe with 10% tolerance
let sma = supra_oracle_ti::compute_sma(
    1,            // pair_id (BTC)
    20,           // period
    3_600_000,    // candle_duration (1 hour)
    1000          // 10.00% missing candles tolerance
);

if (option::is_some(&sma)) {
    let sma_value = option::extract(&mut sma);
    // Use sma_value in your logic
```
