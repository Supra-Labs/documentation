# Calculate task-expiry-time & task-automation-fee

### View the command

```powershell
supra move automation register --help
```

### Understand parameters

```powershell
supra move automation register \
  --task-max-gas-amount XXXX \
  --task-gas-price-cap XXX \
  --task-expiry-time-secs XXXXXXXXXX \
  --task-automation-fee-cap XXXXXXXXXX \
  --function-id "0xYourAddress::your_module::function_name" \
  --rpc-url <TESTNET or MAINNET>
```

{% hint style="info" %}
Check [Network Details](https://docs.supra.com/network/move/network-information) for RPC URL.
{% endhint %}

## How to Calculate --task-expiry-time-secs

Supra tasks only run after the next epoch. Here’s how to calculate it.

{% stepper %}
{% step %}
#### Get the last epoch start

```powershell
curl -X 'GET' \
'https://rpc-testnet.supra.com/rpc/v2/accounts/1/resources/0x1%3A%3Areconfiguration%3A%3AConfiguration' \
 -H 'accept: application/json'
```

**Look for Something like:**

```powershell
"last_reconfiguration_time": "1747163851854115"
```

{% hint style="warning" %}
Above Number is in microseconds, you have to convert it into Seconds before calculating the task expiry time via the formula given.\
\
**Example:**  1747163851854115 Microseconds **->** 1747163851 Seconds
{% endhint %}
{% endstep %}

{% step %}
#### Get the epoch interval (epoch duration)

```powershell
curl -X 'GET' \
  'https://rpc-testnet.supra.com/rpc/v2/accounts/1/resources/0x1%3A%3Ablock%3A%3ABlockResource' \
  -H 'accept: application/json'
```

**Look for Something like:**

```powershell
"epoch_interval": "7200000000"
```
{% endstep %}

{% step %}
#### Final Formula to Calculate Task Expiry Time: (Add a buffer of 300 = 5 mins or 120 = 2 mins)

{% hint style="success" %}
`buffer` is the duration that you wish to set the automation task to run for. Please note that the max duration that your task may be registered for is 7 days from the time of registration.
{% endhint %}

```powershell
task-expiry-time-secs = 
(last_reconfiguration_time/1000000) + (epoch_interval/1000000) + buffer
```

**Example**:

```
task-expiry-time-secs = 1748027985 + 7200 + 300 = 1748035485
```

**Use**:

`--task-expiry-time-secs 1748035485`
{% endstep %}
{% endstepper %}

## How to Estimate the --task-automation-fee-cap

**Input your max gas:** The input argument should be the maximum gas you expect your task to use. Let’s represent this as **\<max-gas>**.

<pre class="language-powershell"><code class="lang-powershell"><strong>curl --request POST \
</strong>  --url https://rpc-testnet.supra.com/rpc/v2/view \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{
  "function": "0x1::automation_registry::estimate_automation_fee",
  "type_arguments": [],
  "arguments": ["&#x3C;max-gas>"]
}'
</code></pre>

{% hint style="info" %}
This represents the expected **--task-automation-fee-cap** based on the currently registered task.&#x20;

You may set a slightly higher automation fee cap to allow for buffering.
{% endhint %}

### Example calculation:

If your task’s max gas is 100:

```powershell
"arguments": ["100"]
```

#### Output:

`{"result":["720000000"]}`&#x20;

Your fee cap is 720,000,000 quants - (7.2 supra per epoch)

### Example calculation:

If your task’s max gas is 50,000:

```
"arguments": ["50000"]
```

#### Output:

`{"result":["393872320800"]}`&#x20;

Your fee cap is 393,872,320,800 quant - (393.87 supra per epoch)

{% hint style="info" %}
The value you input (max gas) should reflect how much gas you expect your task to use. It’s not fixed; adjust as needed based on your task complexity.
{% endhint %}

### Parameter Guide

| --task-max-gas-amount                       | Max gas your function can use                                                                                                                                                                                    |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --task-gas-price-cap                        | Max gas price per unit to avoid spikes                                                                                                                                                                           |
| --task-expiry-time-secs                     | Deadline for task (UNIX time)                                                                                                                                                                                    |
| --task-automation-fee-cap                   | Max fee (in microSUPRA) for automation                                                                                                                                                                           |
| --function-id                               | Target public entry function to trigger                                                                                                                                                                          |
| --args (optional, per target function)      | Arguments to pass to the function                                                                                                                                                                                |
| --task-max-gas-amount                       | Run a dry run to check for success                                                                                                                                                                               |
| task\_duration\_cap\_in\_secs               | Maximum allowable duration (in seconds) from the registration time that an automation task can run, If the expiration time exceeds this duration, the task registration will fail.                               |
| registry\_max\_gas\_cap                     | Maximum gas allocation for automation tasks per epoch, exceeding this limit during task registration will cause failure and is used in fee calculation.                                                          |
| automation\_base\_fee\_in\_quants\_per\_sec | Base fee per second for the full capacity of the automation registry, measured in quants/sec. The capacity is considered full if the total committed gas of all registered tasks equals registry\_max\_gas\_cap. |
| flat\_registration\_fee\_in\_quants         | Flat registration fee charged by default for each task.                                                                                                                                                          |
| congestion\_threshold\_percentage           | Relative to registry\_max\_gas\_cap. Beyond this threshold, congestion fees apply.                                                                                                                               |
| congestion\_base\_fee\_in\_quants\_per\_sec | Base fee per second for the full capacity of the automation registry when the congestion threshold is exceeded.                                                                                                  |
| congestion\_exponent                        | The congestion fee increases exponentially based on this value, ensuring higher fees as the registry approaches full capacity                                                                                    |
| task\_capacity                              | Maximum number of tasks that registry can hold                                                                                                                                                                   |
