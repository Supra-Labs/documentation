# Relative Strength Index (RSI)

**What It Is**\
RSI is a momentum oscillator that measures the speed and magnitude of price changes. It oscillates between 0 and 100, helping identify overbought (>70) or oversold (<30) conditions. Supra's implementation uses Wilder's smoothing method, which applies exponential smoothing to average gains and losses for more stable readings.

**Formula**

```
Step 1: Calculate price changes for each period
Step 2: Separate gains and losses
Step 3: Initial Average Gain = Sum of first n Gains / n
        Initial Average Loss = Sum of first n Losses / n
Step 4: For subsequent periods, apply Wilder's smoothing:
        Current Avg Gain = ((Previous Avg Gain × (n-1)) + Current Gain) / n
        Current Avg Loss = ((Previous Avg Loss × (n-1)) + Current Loss) / n
Step 5: RS = Average Gain / Average Loss
Step 6: RSI = 100 - (100 / (1 + RS))
```

Where:

* `n` = number of periods (typically 7, 14, or 21)

**How It Works**\
RSI compares the magnitude of recent gains to recent losses using Wilder's smoothing method. This approach gives more weight to recent data while maintaining a memory of historical movements, creating smoother RSI values that are less susceptible to erratic price swings. When prices are rising strongly, RSI approaches 100. When prices are falling sharply, RSI approaches 0.

**Example Calculation**

Let's use an extended price dataset to demonstrate Wilder's smoothing:

**Step 1: Compute Price Changes**<br>

| Period | Price | Change | Gain | Loss |
| ------ | ----- | ------ | ---- | ---- |
| 1      | 20    | -      | -    | -    |
| 2      | 22    | +2     | 2    | 0    |
| 3      | 21    | -1     | 0    | 1    |
| 4      | 23    | +2     | 2    | 0    |
| 5      | 24    | +1     | 1    | 0    |
| 6      | 26    | +2     | 2    | 0    |
| 7      | 25    | -1     | 0    | 1    |
| 8      | 27    | +2     | 2    | 0    |

**Step 2: Calculate Initial Averages (5-period, using periods 2-6)**

```
Initial calculation uses simple average of first n periods:

Total Gains = 2 + 0 + 2 + 1 + 2 = 7
Total Losses = 0 + 1 + 0 + 0 + 0 = 1

Initial Average Gain = 7 / 5 = 1.4
Initial Average Loss = 1 / 5 = 0.2
```

**Step 3: Calculate First RSI (Period 6)**

```
RS = Average Gain / Average Loss = 1.4 / 0.2 = 7.0

RSI = 100 - (100 / (1 + RS))
    = 100 - (100 / (1 + 7.0))
    = 100 - (100 / 8)
    = 100 - 12.5
    = 87.5
```

_This first RSI value is available after Period 6 closes_

**Step 4: Apply Wilder's Smoothing for Period 7**

```
Period 7 shows a loss of 1 (price went from 26 to 25)
Current Gain = 0
Current Loss = 1

Using Wilder's smoothing formula:
Current Avg Gain = ((Previous Avg Gain × (n-1)) + Current Gain) / n
                 = ((1.4 × 4) + 0) / 5
                 = 5.6 / 5
                 = 1.12

Current Avg Loss = ((Previous Avg Loss × (n-1)) + Current Loss) / n
                 = ((0.2 × 4) + 1) / 5
                 = 1.8 / 5
                 = 0.36

RS = 1.12 / 0.36 = 3.11

RSI = 100 - (100 / (1 + 3.11))
    = 100 - (100 / 4.11)
    = 100 - 24.33
    = 75.67
```

_Notice how the RSI decreased from 87.5 to 75.67 as the loss was incorporated_

**Step 5: Apply Wilder's Smoothing for Period 8**

```
Period 8 shows a gain of 2 (price went from 25 to 27)
Current Gain = 2
Current Loss = 0

Current Avg Gain = ((1.12 × 4) + 2) / 5
                 = 6.48 / 5
                 = 1.296

Current Avg Loss = ((0.36 × 4) + 0) / 5
                 = 1.44 / 5
                 = 0.288

RS = 1.296 / 0.288 = 4.5

RSI = 100 - (100 / (1 + 4.5))
    = 100 - (100 / 5.5)
    = 100 - 18.18
    = 81.82
```



### **Why Wilder's Smoothing?**

Wilder's smoothing provides several advantages:

1. **Reduced Volatility**: Smoothed averages prevent RSI from jumping erratically on single price moves
2. **Historical Memory**: Each new calculation incorporates the entire price history through the smoothed average
3. **Consistency**: This is the standard method used in traditional technical analysis, making on-chain RSI comparable to off-chain indicators

**On-Chain Storage**: The smart contract stores the previous smoothed average gain and average loss for each asset/timeframe combination. These values are retrieved and updated with each new candle close, maintaining continuity across the entire price history.

**Interpreting RSI Values:**

* **RSI > 70**: Potentially overbought (price may be due for a correction)
* **RSI < 30**: Potentially oversold (price may be due for a bounce)
* **RSI = 50**: Neutral momentum

**Key Characteristics**

* **Bounded**: Always between 0 and 100
* **Momentum Indicator**: Measures the velocity of price changes with smoothing
* **Mean Reversion**: Useful for identifying extreme conditions
* **Divergence**: Can signal trend reversals when RSI direction differs from price direction
* **Smoothed Values**: Less reactive to noise, more reliable signals
