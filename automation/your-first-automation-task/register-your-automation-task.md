---
description: >-
  Make sure the contract is published before registering automation — otherwise,
  the system won’t be able to find your function.
---

# Register your Automation Task

Before registering for real, test the task using `--simulate:`

```powershell
supra move automation register --simulate \
  --task-max-gas-amount 5000 \
  --task-gas-price-cap 200 \
  --task-expiry-time-secs 1748035485 \
  --task-automation-fee-cap 1440000000\
  --function-id "0x123::auto_incr::auto_increment" \
  --rpc-url https://rpc-testnet.supra.com
```

**If you see:**

```powershell
"vm_status": "Executed successfully"
```

{% hint style="success" %}
**You’re good to go, remove `--simulate` and run it for to get Automation Task Registered.**
{% endhint %}

## Post-Deployment: Sanity Check

Once you've registered your automation task, you’ll want to confirm it’s actually working.

* **Use SupraScan**
  * Visit [SupraScan](https://suprascan.io/)
  * Search your deployer address
  * Check Tasks Tab.
  * Confirm your task is executing at the expected intervals

<figure><img src="../.gitbook/assets/Screenshot 2025-05-19 165122.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="warning" %}
Task will not become active until the next epoch.\
Users may not be able to confirm the task is executing right away.
{% endhint %}

* **Run the View Function**

To check your counter value, run this CLI command:

```powershell
supra move tool view \
  --function-id '0x123::auto_incr::get_counter_value' \
  --args address:0x123 \
  --rpc-url https://rpc-testnet.supra.com
```

Before the automation task starts:

```powershell
{
"result": [
"0"
]
}
```

After the automation task starts:

```powershell
{
  "result": [
    "1"
  ]
}

```

{% hint style="success" %}
Check again after automation runs — if the value increases, your automation task is working perfectly.
{% endhint %}

