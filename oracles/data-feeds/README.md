# Data Feeds

Oracle feeds play a pivotal role in delivering dependable and precise real-world data to blockchain networks, unlocking a multitude of valuable use cases. The Supra team acknowledges the current constraints of conventional oracles, including issues like reliance on trusted intermediaries, higher latency, and slower on-chain finality. That's why Supra took a rigorous, research-based approach to forge a superior oracle solution. This culminated in the creation of DORA, short for Supra's Distributed Oracle Agreement, an advancement in the field grounded in peer-reviewed research.

### **Distributed Oracle Agreement (DORA)**

DORA natively employs the most performant consensus algorithm in to the oracle space, and aggregates representative price data (S-value) for a wide array of assets and commodities. To do this, the protocol validates and reports prices derived from up to 21 data sources with Byzantine Fault Tolerant algorithms. Employing a unique Tribe-Clan architecture, DORA is further strengthened and safeguarded by anti-collusion randomness algos (via our decentralised Verifiable Random Function, or dVRF) to ensure the decentralisation qualities of the network persist over time. Likewise, Supra deploys multiple fault-tolerant techniques during the data collection, aggregation, and computation stages to ensure the highest fidelity price feeds and detect abnormal behaviours before they could affect outcomes.

### **Computation of S-Value**

Every node obtains data from multiple data sources and computes the median from the set of values obtained from its assigned sources. To begin, nodes are randomly assigned tasks and later reassigned periodically via Supra's decentralised Verifiable Random Function as part of its deliberate, anti-collusion approach.

<figure><img src="../.gitbook/assets/image 1.png" alt=""><figcaption><p>Figure 1:  Node level price data aggregation</p></figcaption></figure>

Then, Supra’s calculation methodology identifies a _coherent cluster_ formed within the _agreement distance_ parameter. This ensures that different nodes’ median values have not abnormally deviated from each other before computing the arithmetic mean (as a proposed S-value), which is subsequently signed by all nodes in the network.

<figure><img src="../.gitbook/assets/image 2.png" alt=""><figcaption><p>Figure 2:  Aggregator nodes calculate the mean of median values proposed by nodes of the clan.</p></figcaption></figure>

Not deep enough? You can check our [lite paper](https://supraoracles.com/news/dora-distributed-oracle-agreement/) for a better understanding and our [white paper](https://supra.com/documents/SupraOracles-DORA-Whitepaper.pdf) for an even deeper dive. Our white paper provides an extremely in-depth explanation of our decentralised Oracle (DORA ) protocol, the industry's leading oracle service, and is ideal for developers who want to use and test our innovative products.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}
