# Rest API

Rest API enables users to request the latest price update for multiple data pairs or historical prices up to 1 month for a single data pair. We will enable historical data going back up to 6 months in the near future.\
History data response will contain OHLC data (Open price, High price, Low price, Close price) for the requested time resolution which will facilitate building candlestick charts.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

\
Base URL

```
https://prod-kline-rest.supra.com/history
```

### Endpoint: Get Historical Price Data for Trading Pair

#### Request URL

```
GET/history
```

\
**Headers**

<table><thead><tr><th width="179">Header</th><th>Value</th></tr></thead><tbody><tr><td>x-api-key</td><td>Your Key ( Please request your key <a href="https://docs.google.com/forms/d/e/1FAIpQLSfHZr8BHLTY8q_MjbuKZPpdgQS0oGmRIrsn9H-HrieH9eQjFw/viewform">here</a>)</td></tr></tbody></table>

### Query Parameters

| Parameter     | Type    | Description                                                                                                            | Example                     |
| ------------- | ------- | ---------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| trading\_pair | String  | The trading pair for which to retrieve historical data. Example: btc\_usd, eth\_usd, etc.                              | btc\_usd                    |
| startDate     | Integer | The starting timestamp for the data range in Unix milliseconds (UTC). This determines the beginning of the data range. | 1732014893723               |
| endDate       | Integer | The ending timestamp for the data range in Unix milliseconds (UTC). This determines the end of the data range.         | 1732014910000               |
| Resolution    | Integer | The time interval between data points in seconds. Typical intervals could be 15, 30, 60, 3600(1 hour)                  | 86400 ( for 24H resolution) |

#### Example Request

{% code overflow="wrap" %}
```javascript
GET https://prod-kline-rest.supra.com/history?
trading_pair=btc_usd&startDate=1732014893723&endDate=1732014910000&int
erval=5
```
{% endcode %}

#### Response

The response contains historical price data for the given trading pair in the specified time range and interval. The response is typically in JSON format.

```json
{
  "status": "success",
  "data": [
	{
  	"timestamp": 1732014893723,
  	"open": 20000.5,
  	"high": 20100.0,
  	"low": 19950.0,
  	"close": 20050.0,
  	"volume": 15.2
	},
	{
  	"timestamp": 1732014898723,
  	"open": 20050.0,
  	"high": 20150.0,
  	"low": 20000.0,
  	"close": 20100.0,
  	"volume": 18.1
	},
	...
  ]
}

```

### Endpoint: Get Latest Price Data for Trading Pair

Request URL

```
GET /latest
```

\
Parameters

| Parameter     | Type   | Description                                              | Example   |
| ------------- | ------ | -------------------------------------------------------- | --------- |
| trading\_pair | String | The trading pair for which to retrieve latest price data | btc\_usdt |

\
\
Example Request

{% code overflow="wrap" %}
```javascript
GET https://prod-kline-rest.supra.com/latest?trading_pair=btc_usdt
```
{% endcode %}

Response

```json
{
    "currentPage": 1,
    "totalPages": 1,
    "totalRecords": 1,
    "pageSize": 10,
    "instruments": [
        {
            "time": "1732175999048",
            "timestamp": "2024-11-21 07:59:59",
            "currentPrice": "96913.07250000001",
            "24h_high": "97849.9",
            "24h_low": "92720",
            "24h_change": "5.5326790336496980",
            "tradingPairs": "btc_usdt"
        }
    ]
}
```

\
Status Codes

* 200 OK: The request was successful, and data is returned.
* 400 Bad Request: The request was invalid or improperly formatted.
* 404 Not Found: The requested trading pair or data could not be found.
* 500 Internal Server Error: A server error occurred while processing the request.\
