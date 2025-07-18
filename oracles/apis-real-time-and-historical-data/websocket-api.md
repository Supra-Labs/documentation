# WebSocket API

WebSocket API streams price feeds data real time. Users can configure the query based on the preferred resolution to receive OHLC( Open price, High Price, Low Price, Close price). data required to build candlestick charts.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}

### WebSocket Connection URL

WebSocket connection should be established to the above URL to start receiving real-time data.\
\
**Endpoint URL**

```
wss://prod-kline-ws.supra.com

```

**Header**

<table><thead><tr><th width="179">Header</th><th>Value</th></tr></thead><tbody><tr><td>x-api-key</td><td>Your Key ( Please request your key <a href="https://docs.google.com/forms/d/e/1FAIpQLSfHZr8BHLTY8q_MjbuKZPpdgQS0oGmRIrsn9H-HrieH9eQjFw/viewform">here</a>)</td></tr></tbody></table>

### Message Payload Structure

To initiate a subscription to the data feed, a message is sent to the WebSocket server. The message must be in the following JSON format:

```json
{
  "action": "subscribe",
  "channels": [
    {
      "name": "ohlc_datafeed",
      "resolution": 5,
      "datapairs": ["btc_usd"]
    }
  ]
}
```

#### Parameters

* **action**: "subscribe" : This field tells the server that the client is subscribing to a data channel.
* **channels**: This is an array of channel configurations to which the client wishes to subscribe. Each channel has the following parameters:
  * **name**: "ohlc\_datafeed" This refers to the data feed channel for receiving historical open/close/low data updates.
  * **resolution**: 5\
    This is the resolution (in minutes) at which data updates will be delivered. A value of 5 means the server will send ohlc (Open price, High price, Low price, close price) updates every 5 minutes. Changing this value will adjust the resolution of updates.\
    Example: 1 =1 minute resolution(bar), 5=5 minutes resolution(bar)
  *   **dataPairs**:

      A list of data pairs to subscribe to. This can include single or multiple pairs:

       **Single pair:** \["btc\_usd"] – Subscribes to the Bitcoin to USD data feed.

       **Multiple pairs:** \["btc\_usdt", "eth\_usdt"] – Subscribes to multiple data pairs.

      Example:

       Single pair subscription: "dataPairs": \["btc\_usd"]

       Multiple pairs subscription: "dataPairs”: \["btc\_usd", "eth\_usd",btc\_usdt"]

### Response Data structure

Once the subscription is successful, the server will respond with data at the specified frequency. The format of the response for each update is as follows:\
\
Example Response

```json
{
  "event": "ohlc_datafeed",
  "payload": [
	{
  	"time": "1732081150000",
  	"timestamp": "2024-11-20 05:39:10",
  	"open": "92420.15",
  	"high": "92420.15",
  	"low": "92412.3",
  	"close": "92412.3",
  	"dataPair": "btc_usd",
  	"currentPrice": 92412.3
	}
  ]
}

```

#### Response Parameters

* **event**: "ohlc\_datafeed"\
  This indicates the type of event for which data is being sent, which is the historical open/high/low/close data feed.
* **payload**:\
  This is an array of data objects representing updates for the subscribed data pair(s). Each object contains:
  * **time**: 1732081150000\
    The time in Unix timestamp format (milliseconds). It represents when the data was recorded.
  * **timestamp**: "2024-11-20 05:39:10"\
    The human-readable timestamp for the data point.
  * **open**: "92420.15"\
    The opening price for the given time period.
  * **high**: "92420.15"\
    The highest price during the time period.
  * **low**: "92412.3"\
    The lowest price during the time period.
  * **close**: "92412.3"\
    The closing price for the given time period
  * **dataPair**: "btc\_usd"\
    The data pair for which this data is provided.
  * **currentPrice**: 92412.3\
    The most recent price of the data pair at the time of data transmission.

Time resolution of updates

Time resolution parameter in the subscription message determines how often data will be sent. For instance:

* If resolution is set to 5:\
  The server will send a data update every 5 seconds.
* If resolution is set to 10:\
  The server will send a data update every 10 seconds.\\

Example:\
\
1\. Client Subscription\
Client and sends the following subscription message to receive real-time ohlc (open price, high price, low price, close price) data updates every 5 minutes for the "btc\_usdt" data pair:

```json
{
  "action": "subscribe",
  "channels": [
    {
      "name": "hocl_datafeed",
      "resolution": 5,
      "dataPairs": ["btc_usdt"]
    }
  ]
}
```

2. Server Response\
   After subscribing, the client will start receiving data every 5 minutes (based on the requested resolution for the "btc\_usdt" data pair in the following format:

```json
{
  "event": "ohlc_datafeed",
  "payload": [
    {
      "time": "1732081150000",
      "timestamp": "2024-11-20 05:39:10",
      "open": "92420.15",
      "high": "92420.15",
      "low": "92412.3",
      "close": "92412.3",
      "dataPair": "btc_usdt",
      "currentPrice": 92412.3
    }
  ]
}
```

\
