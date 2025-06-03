# Canceling Your Automation Task

#### Why Cancel?

Canceling a task prevents it from executing in the future.

**Useful when:**

* Task logic is no longer needed.
* You made a mistake in the task parameters.
* You want to avoid draining your wallet from recurring executions.

{% stepper %}
{% step %}
#### Run the Cancel Command

```powershell
supra move automation cancel \
  --task-index <TASK_INDEX> \
  --rpc-url <TESTNET or MAINNET>
```

**Parameter Guide**

`--task-index:` The unique identifier of the task you want to cancel. Each task is assigned an index when registered.

**How do you find your `--task-index`?**

To find your task index:

1. Go to[ SupraScan](https://suprascan.io)
2. Enter your deployer address
3. Navigate to the “Tasks” tab
4. Locate the active task you want to cancel
5. Find the Task ID value listed with the task details

**You’ll use this index in your cancel command.**

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Understand Parameters

```powershell
supra move automation cancel \
  --task-index 347 \
  --rpc-url https://rpc-testnet.supra.com
```

**If you see this:**

<kbd>"vm\_status": "Executed successfully"</kbd>

You have successfully cancelled the automation task!
{% endstep %}
{% endstepper %}

### After Canceling

* The task is no longer active
* No additional executions will occur
* You won’t be charged automation or gas fees for that task anymore

### Post-Cancel: Sanity Check

* Visit [SupraScan](https://suprascan.io/)
* Search your deployer address
* Navigate to the Tasks tab or check your function call history
* Confirm that the automated function is no longer executing

{% hint style="warning" %}
If you still see executions, double-check that the correct task was cancelled using right `--task-index`
{% endhint %}
