# Prerequisites & Epoch Timing

## Prerequisites

* Supra CLI Setup and Profile Created.
* Sufficient funds in your wallet to cover:
  * Gas fees
  * Automation execution fees

## Understanding Epoch Timing

In Web3, automation tasks are often influenced by time-based intervals. On Supra, an epoch is a 2-hour interval (7200 seconds) used for internal updates like task registration, cancellation, and fee collection.&#x20;

Howeve&#x72;**, automation tasks themselves are executed at the end of each block.** This means:

* Once your task is registered, it can be triggered by block activity, as frequently as every block.
* Registration timing still matters: if your task is registered too close to its expiry time, it may not have a chance to run.

Epochs primarily affect when registrations are picked up, not when the task is executed.

**Understanding this helps you:**

* Avoid confusion if a task doesn’t run immediately after registering
* Plan automation that reacts quickly to block-level activity
* Set `--task-expiry-time-secs` with enough buffer to allow time for task pickup and execution

{% hint style="success" %}
**Always give your task some buffer time after registration by calculating your expiry like this:**\
expiry = (last\_reconfiguration\_time / 1,000,000) + 7200 + buffer \
\
**For Example:**  <kbd>expiry = (last\_reconfiguration\_time / 1,000,000) + 7200 + 300</kbd>&#x20;

\
**While epochs matter for registration, task execution runs block-by-block, so you’ll get more frequent and responsive automation than you might expect.**
{% endhint %}
