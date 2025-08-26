---
hidden: true
---

# Request Random Numbers

This guide covers how to request random numbers using Supra dVRF after completing the initial setup and whitelisting process.

### Prerequisites

Before requesting random numbers, ensure you have:

* ✅ Whitelisted wallet address with configured gas parameters
* ✅ Deposited sufficient funds in the Deposit Contract
* ✅ Whitelisted your consumer contract address



{% tabs %}
{% tab title="EVM" %}
### Contract Architecture&#x20;

Supra dVRF uses a two-contract system for EVMs:

* **Router Contract**: Handles VRF requests and delivers random numbers to your callback function
* **Deposit Contract**: Manages user funds, whitelisting, and gas configurations\
  \

{% endtab %}

{% tab title="Supra L1" %}

{% endtab %}
{% endtabs %}

### Contract Architecture&#x20;

Supra dVRF uses a two-contract system for EVMs:

* **Router Contract**: Handles VRF requests and delivers random numbers to your callback function
* **Deposit Contract**: Manages user funds, whitelisting, and gas configurations

however In Supra L1,  dVRF is a Single contract
