# Getting Started

Before diving into the technical details, let's understand how dVRF works and how to subscribe to consume random numbers.

{% hint style="info" %}
VRF 3.0 and UI based subscription manager still not live on SUPRA L1 or SUPRA EVM.\
\
If you re building on SUPRA please refer to section "BUILD on SUPRA L1 with dVRF" for developer documentation.&#x20;
{% endhint %}

### Subscription Model

Think of it like a prepaid phone plan, but for random numbers. You deposit funds upfront, and Supra uses them to pay gas fees for your VRF callbacks.\


* **Predictable costs**: Set gas limits upfront, no surprises
* **Simplified contracts**: Your VRF consumer contracts don't need to handle payments
* **Bulk management**: One subscription can serve multiple contracts
* **Reliability**: Reserved minimum balance ensures your requests don't fail due to insufficient funds

### How dVRF works?

Understanding the complete dVRF process flow will help you integrate more effectively:



<figure><picture><source srcset="../.gitbook/assets/Untitled diagram _ Mermaid Chart-2025-08-25-191854.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Untitled diagram _ Mermaid Chart-2025-08-25-191451.png" alt="" width="375"></picture><figcaption></figcaption></figure>

**Why use subscriptions?**

* You create a subscription with your wallet address as the manager
* Deposit funds into your subscription account
* Register (whitelist) your smart contracts under this subscription
* When your contracts request random numbers, Supra automatically pays the callback gas fees from your subscription balance
* No need to handle gas payments in your contract code - it's all automated!



\
