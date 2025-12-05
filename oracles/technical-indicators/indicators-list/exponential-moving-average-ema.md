# Exponential Moving Average (EMA)

**What It Is**\
EMA is a weighted moving average that gives more importance to recent prices. It responds faster to price changes than SMA, making it useful for capturing emerging trends earlier.

**Formula**

```
Initial EMA = SMA for the first calculation

For subsequent periods:
EMA(t) = α × ClosePrice(t) + (1 - α) × EMA(t-1)
```

Where:

* `α` (alpha) = smoothing factor = 2 / (n + 1)
* `n` = number of periods
* `t` = current time period

**How It Works**\
EMA applies a multiplier (alpha) that gives more weight to recent prices. The smoothing factor decreases as the period length increases, meaning a 200-period EMA responds much slower than a 9-period EMA.<br>

**Example Calculation**

Using the same example price data from SMA:<br>

**Step 1: Calculate Initial EMA (Period 5)**\
The first EMA equals the SMA:

```
EMA₅ = SMA₅ = 22.0
```

_Available after Period 5 closes (on Period 6)_

**Step 2: Calculate Smoothing Factor**

```
α = 2 / (n + 1) = 2 / (5 + 1) = 0.333
```

**Step 3: Calculate EMA for Period 6**

```
EMA₅(Period 6) = α × ClosePrice(Period 6) + (1 - α) × EMA₅(Period 5)
                = 0.333 × 26 + (1 - 0.333) × 22.0
                = 8.658 + 14.674
                = 23.33
```

_Available after Period 6 closes (on Period 7)_

**Step 4: Calculate EMA for Period 7 (ClosePrice = 27)**

```
EMA₅(Period 7) = α × ClosePrice(Period 7) + (1 - α) × EMA₅(Period 6)
                = 0.333 × 27 + (1 - 0.333) × 23.33
                = 8.991 + 15.56
                = 24.55
```

**Key Characteristics**

* **Responsiveness**: Reacts faster to price changes than SMA
* **Trend Sensitivity**: Better at identifying trend changes early
* **Complexity**: Carries forward historical data through the exponential calculation
* **Memory**: Each new EMA depends on the previous EMA value
