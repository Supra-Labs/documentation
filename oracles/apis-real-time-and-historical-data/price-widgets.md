# Price Widgets

Supra provides embeddable widgets for displaying cryptocurrency data on websites. These widgets can be customised and be integrated using HTML iframes.&#x20;

### Pre requisites

Users who plans to display these widgets need to have access to a SUPRA API key.  Please feel free to request your key [here](https://docs.google.com/forms/d/e/1FAIpQLSfHZr8BHLTY8q_MjbuKZPpdgQS0oGmRIrsn9H-HrieH9eQjFw/viewform).

### Customisation options

* Number of price feeds
* Widget size
* Text colour
* Background colour

### **Horizontal Moving Price Ticker**

This ticker can be customised to add any number of asset prices, and prices are updated real time. All price tickers are moving left side. &#x20;

<figure><img src="../.gitbook/assets/Horizontal Ticker.png" alt=""><figcaption></figcaption></figure>

Simply copy paste the code snippet below.

{% code overflow="wrap" %}
```html
<iframe src="https://supra.com/data/widgets?widgetType=horizontal&instrumentPairs=btc_usdt,eth_usdt,supra_usdt&x-api-key=YOUR_API_KEY" 
    width="100%" 
    height="400px" 
    frameborder="1"
    scrolling="no"
></iframe>
```
{% endcode %}



### Vertical Price Widget

Vertical ticker real time prices and the tickers are static,  scrollable and can customise for any number of asset prices.

<img src="../.gitbook/assets/ticker2 (1).png" alt="" data-size="original">

\
Copy and paste following snippet.&#x20;

{% code overflow="wrap" %}
```html
<iframe 
src="https://supra.com/data/widgets?widgetType=vertical&instrumentPairs=btc_usdt,eth_usdt&x-api-key=YOUR_API_KEY" 
    width="100%" 
    height="800px" 
    frameborder="0"
    scrolling="no"
></iframe>
```
{% endcode %}



### Customisation Options

1.  textColor :  Sets the colour of the text.\
    Example:\
    `Hex: textColor=black`\
    &#x20;`RGB: textColor=rgb(0, 0, 0)`


2. subTextColor  : Sets the colour of the sub-text.\
   Example:\
   `Hex: subTextColor=%23444 (This sets the color to #444)`\
   &#x20;`RGB: subTextColor=rgb(68, 68, 68)`<br>
3. gridBg : Sets the background colour of the grid.\
   Example:\
   `Hex: gridBg=yellow`\
   &#x20;`RGB: gridBg=rgb(255, 255, 0)`<br>
4. gridBgHover :  Sets the background colour of the grid on hover.\
   Example:\
   `Hex: gridBgHover=green`\
   &#x20;`RGB: gridBgHover=rgb(0, 255, 0)`<br>
5. mainBG :  Sets the main background colour.\
   Example:\
   `Hex: mainBG=red`\
   &#x20;`RGB: mainBG=rgb(255, 0, 0)`<br>
6. chartBgColor :  Sets the background color of the chart.\
   Example:\
   `Hex: chartBgColor=white`\
   `RGB: chartBgColor=rgb(255, 255, 255)`



### Hex Color Codes and URL Encoding

When using Hex values in the URL, ensure that you encode the hash (#) as %23. For example:

* Hex color #444 becomes %23444.
* Hex color #ffffff becomes %23ffffff.<br>

#### Example with Encoded Colors

If you are using Hex values, here’s how the URL would look with the parameters applied:

{% code overflow="wrap" %}
```html
<iframe src="https://supra.com/data/widgets?textColor=white&subTextColor=%23AAAAAA&gridBg=%232D2D2D&gridBgHover=%23F39C12&widgetType=vertical&mainBG=%231D1F27&chartBgColor=%23FFFFFF&x-api-key=YOUR_API_KEY" 
    width="100%" 
    height="600px" 
    frameborder="0"
    scrolling="no"
></iframe>
```
{% endcode %}

#### Example with RGB Colors

{% code overflow="wrap" %}
```html
<iframe src="https://supra.com/data/widgets?textColor=rgb(255,255,255)&subTextColor=rgb(170,170,170)&gridBg=rgb(45,45,45)&gridBgHover=rgb(243,156,18)&widgetType=vertical&mainBG=rgb(29,31,39)&chartBgColor=rgb(255,255,255)&x-api-key=YOUR_API_KEY" 
    width="100%" 
    height="600px" 
    frameborder="0"
    scrolling="no"
></iframe>
```
{% endcode %}
