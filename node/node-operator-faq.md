# Node Operator FAQ

## Onboarding & Setup

<details>

<summary>Should the onboarding script be run directly on the machine that will act as the validator?</summary>

Yes, you should run all the steps of Round onboarding for Public Testnet on the machine that will act as the validator.

</details>

<details>

<summary>What are the hardware requirements for running a Supra node?</summary>

Below are the hardware requirements:

* **Cores:** 16 to 32
* **RAM:** 64GB
* **CPU:** Intel Xeon @ 2.8GHz or higher
* **Architecture:** x86/64
* **Disk Type:** SSD
* **Minimum Disk Size:** 2TB
* **Network Bandwidth:** 1Gbps

</details>

<details>

<summary>My testnet RPC nodes are experiencing increasing disk usage. Are there any pruning options?</summary>

Yes, it’s expected for now. We'll release an update that enables pruning in the near future.

It's not currently possible.

</details>

<details>

<summary>There is no derived pool address in the latest_validator_info.json file. What should I do?</summary>

In your <mark style="color:green;">`latest_validator_info.json`</mark> file, your derived pool address is the value of <mark style="color:green;">`account_address`</mark>.

</details>

<details>

<summary>How do I move my node to a different server (RPC &#x26; Validator Testnet)?</summary>

Validator Testnet: [Relocating a Supra Validator](https://docs.google.com/document/d/16j8dq7glIf7CjlqC-gM8R-i_Ys2d6YLot_-Y5DTsUsQ/edit?usp=sharing)

RPC Testnet: [Move Node to New Server - RPC Testnet](https://docs.google.com/document/d/1ig7uuIXSbnSNHTc3pb3JFdNYDr5sZ99g5E6htNJTSpQ/edit?usp=sharing)

</details>

## Staking & Rewards

<details>

<summary>How do I view my current stake balance?</summary>

You can view your current stake balance using the following script:

<mark style="color:green;">`supra move tool view --function-id 0x1::pbo_delegation_pool::get_stake --args address:"$POOL_ADDRESS" address:"$DELEGATOR_ADDRESS" --url "$RPC_URL`</mark>

Example Response:

<mark style="color:green;">`{"result":["5854055647855","0","0"]}`</mark>

* The **first value** (5854055647855) represents your active stake
* The **second value** represents your inactive stake.
* The **third value** represents your pending inactive stake.

</details>

<details>

<summary>How do I claim rewards?</summary>

To claim rewards, you need to first unlock your stake and then withdraw your inactive stake.

Here are the steps:

* **Step 1: Unlock Stake Amount:** You need to first check your active stake using the provided script. Once you know your active stake, you can unlock an amount less than or equal to your active stake.\
  \
  Please make sure the AMOUNT\_TO\_UNLOCK is at least 100000000 (1 SUPRA).

<mark style="color:green;">`#!/bin/bash`</mark>

<mark style="color:green;">`RPC_URL="https://rpc-mainnet.supra.com/"`</mark>

<mark style="color:green;">`POOL_ADDRESS="0xdbd2155f2f5aee0e75bcf08b4aaab6f8332baf125623b839c3a3f2280099f27b" # Replace with your pool address`</mark>

<mark style="color:green;">`AMOUNT_TO_UNLOCK="100000000" # Replace with the amount you want to unlock`</mark>\
\ <mark style="color:green;">`supra move tool run --function-id 0x1::pbo_delegation_pool::unlock --args address:"$POOL_ADDRESS" u64:"$AMOUNT_TO_UNLOCK" --url "$RPC_URL`</mark>

* **Step 2: Withdraw Inactive Stake Amount**

**Note**: This step can only be executed during the next lockup cycle.

<mark style="color:green;">`#!/bin/bash`</mark>

<mark style="color:green;">`RPC_URL="https://rpc-mainnet.supra.com/"`</mark>

<mark style="color:green;">`POOL_ADDRESS="0xdbd2155f2f5aee0e75bcf08b4aaab6f8332baf125623b839c3a3f2280099f27b" # Replace with your pool address`</mark>

<mark style="color:green;">`AMOUNT_TO_WITHDRAW="100000000" # Replace with the amount you want to withdraw`</mark>

<mark style="color:green;">`supra move tool run --function-id 0x1::pbo_delegation_pool::withdraw --args address:"$POOL_ADDRESS" u64:"$AMOUNT_TO_WITHDRAW" --url "$RPC_URL`</mark>

**Important Notes:**

* You can only unlock an amount equal to or less than your active stake.
* You can only withdraw an amount equal to or less than your inactive stake.
* Once you unlock your stake, you must wait for the next lockup cycle before you can withdraw the inactive stake.

</details>

<details>

<summary>What is the smallest unit of SUPRA?</summary>

The smallest unit of SUPRA is **Quants**, where <mark style="color:green;">1 SUPRA = 100,000,000 Quants</mark>.

</details>

<details>

<summary>Can I withdraw rewards to a different address than the validator’s?</summary>

You can do this by calling <mark style="color:green;">`set_beneficiary_for_operator`</mark> to set a beneficiary address for your operator commission.

<mark style="color:green;">`!/bin/bash`</mark>

<mark style="color:green;">`BEN_ADDR=0xabcdef # Replace with the address of the beneficiary`</mark>

<mark style="color:green;">`RPC_URL = "https://rpc-mainnet.supra.com"`</mark>

<mark style="color:green;">`supra move tool run --function-id 0x1::pbo_delegation_pool::set_beneficiary_for_operator --args address:$BEN_ADDR --url $RPC_URL`</mark>

Replace <mark style="color:green;">`$BEN_ADDR`</mark>, with the address of the beneficiary in the command above. After executing the command above, while the <mark style="color:green;">`operator`</mark> would still be the address connected to the network as a validator, <mark style="color:green;">`$BEN_ADDR`</mark>would receive the stake commensurate with the commission earned by the <mark style="color:green;">`operator`</mark>. Use the steps provided in Q.2, above from the <mark style="color:green;">`$BEN_ADDR`</mark> profile (with the private key of <mark style="color:green;">`$BEN_ADDR`</mark>) to unlock/withdraw the stake that was earned as commission.

\\

</details>

## Troubleshooting & Issues

<details>

<summary>What should I do if blocks are frozen and my validator node is stuck?</summary>

<mark style="color:green;">`[2024-10-17T01:51:50.607720Z+00:00] WARN moonshot::core: Timeout reached for View { epoch_id: EpochId { chain_id: 6, epoch: 195 }, round: 2306 }`</mark>

<mark style="color:green;">`[2024-10-17T01:51:55.608661Z+00:00] WARN moonshot::core: Timeout reached for View { epoch_id: EpochId { chain_id: 6, epoch: 195 }, round: 2306 }`</mark>

<mark style="color:green;">`[2024-10-17T01:52:00.610197Z+00:00] WARN moonshot::core: Timeout reached for View { epoch_id: EpochId { chain_id: 6, epoch: 195 }, round: 2306 }`</mark>

A: If the epoch and round are stuck, you will need to restart the network using a snapshot:

Please follow the instructions provided in the ‘To Update Your RPC Node’ section in this document: [RPC Operator Manual (General)](https://docs.google.com/document/d/1ig7uuIXSbnSNHTc3pb3JFdNYDr5sZ99g5E6htNJTSpQ/edit?tab=t.0#heading=h.ao60n777ejup)

</details>

<details>

<summary>What should I do if I encounter an "IO Error" or database corruption?</summary>

<mark style="color:green;">`thread 'main' panicked at /home/ubuntu/smr-moonshot/consensus/node/src/bftnode.rs:57:14: fail to create rocksdb: DBError(Error { message: "Corruption: Corruption: IO error: No such file or directory: While open a file for random read: configs/smr_storage/000364.ldb: No such file or directory in file configs/smr_storage/MANIFEST-000369" }) note: run with RUST_BACKTRACE=1 environment variable to display a backtrace`</mark>

A: If you see the "Corruption: IO error," restart the network using the snapshot as mentioned in the previous solution. If the issue persists, ensure your environment is properly set up and your data paths are correct.

</details>

<details>

<summary>What should I do if there is an RPC error on startup?</summary>

Step 1: Open ports 26000 and 27000.

Step 2: Restart the container using <mark style="color:green;">`docker restart container-id`</mark>`.`

</details>

<details>

<summary>How can I resolve a "Grafana setup IP not whitelisted" error?</summary>

If your IP is not whitelisted, please provide your IPv4 to the Supra team on Discord to get access to the Grafana script.

</details>

<details>

<summary>How do I resolve the "API key is missing" error?</summary>

1. Export the API key with:

<mark style="color:green;">`export api_key=AIzaSyD2Byf2_yWYngvHnv6Ib7V6C2EpHY3LL0E`</mark>

2. Run the installation script for your distribution:

<mark style="color:green;">`sudo -E ./nodeops-monitoring-telegraf-centos.sh`</mark>

<mark style="color:green;">`# For CentOS`</mark>

or

<mark style="color:green;">`sudo -E ./nodeops-monitoring-telegraf.sh`</mark>

<mark style="color:green;">`# For other distributions`</mark>

</details>

<details>

<summary>How do I resolve the "RPC node out of sync" error?</summary>

`INFO rpc_node::listener: RPC node out of sync!`

A: To resolve the "RPC node out of sync" error, restart the node from the latest snapshot using the instructions provided for snapshots.

</details>

<details>

<summary>What should I do if I encounter a "Subscription execution failed" error?</summary>

<mark style="color:green;">`[2024-11-06T08:20:47.531043Z+00:00] ERROR sop2p::behaviour: Subscription execution failed: "Closed(..)"`</mark>\ <mark style="color:green;">`[2024-11-06T08:20:47.531922Z+00:00] ERROR sop2p::behaviour: Subscription execution failed: "Closed(..)"`</mark>\ <mark style="color:green;">`[2024-11-06T08:20:47.531961Z+00:00] ERROR sop2p::behaviour: Subscription execution failed: "Closed(..)"`</mark>

A: This issue can be resolved by increasing the system <mark style="color:green;">`ulimit`</mark> for the number of open files. Follow the steps below:

1. Increase the shell file descriptor limit:

<mark style="color:green;">`ulimit -n 65535`</mark>

2. Increase the limit for the user by editing <mark style="color:green;">`/etc/security/limits.conf:`</mark>

<mark style="color:green;">`soft nofile 65535`</mark>

<mark style="color:green;">`hard nofile 65535`</mark>

3. Update system limits in <mark style="color:green;">`/etc/sysctl.conf:`</mark>

<mark style="color:green;">`net.core.somaxconn=65535`</mark>

<mark style="color:green;">`sudo sysctl -p`</mark>

</details>

## General Information & Frequently Asked Questions

<details>

<summary>Why do the values of stakes differ among operators?</summary>

The differences in stake values could be due to various factors:

* All operators received 55M SUPRA from the Foundation at Genesis.
* Additional rewards generated since then are added directly into each operator’s delegation pool.
* If an operator has withdrawn some of their rewards, their stake may be lower.
* If another entity besides the Foundation has delegated a stake to an operator, that could also account for the differences.

</details>

<details>

<summary>Why can we only unlock active stake, and what is the Foundation’s role?</summary>

The Foundation keeps 55M SUPRA locked to ensure that validators continue to function properly. The rest of the stake, which is earned as a reward, can be unlocked or unstaked. However, you cannot unlock the 55M SUPRA delegated by the Foundation.

</details>

<details>

<summary>How long should I wait after unstaking before I can withdraw?</summary>

After unstaking, you must wait for the next lockup cycle, which is set to 48 hours, before you can withdraw your inactive stake.

</details>

<details>

<summary>How are earnings per node determined? Is it performance-based or random?</summary>

Earnings per node are based on performance. However, since we do not currently record failed proposals, the reward calculations are based on the number of successful proposals. We plan to track performance metrics in the future, which will affect rewards.

</details>

<details>

<summary>Where can I find my delegator address private key to import into the StarKey wallet?</summary>

You can find your delegator address private key by running the following command:

<mark style="color:green;">`./supra profile list --reveal-secrets`</mark>

The output <mark style="color:green;">`ed25519_secret_key`</mark> is the key to your operator's Move account.

</details>

<details>

<summary>How does stake, including inactive and pending inactive, work in Supra?</summary>

The function <mark style="color:green;">`0x1::pbo_delegation_pool::get_stake`</mark> returns a tuple with three values: active, inactive, and pending\_inactive stakes.

* **Active stake:** This is your current active stake.
* **Inactive stake:** This is the stake that has been unlocked but is not withdrawn yet.
* **Pending inactive stake:** This is the stake that has been unlocked and is awaiting the end of the lockup period before it becomes inactive.

The lockup duration is **48 hours**, which is configurable via governance.

</details>

<details>

<summary>Can I unlock more than my active stake?</summary>

No, you can only unlock an amount equal to or less than your active stake. Please report any issues if you find you can unlock more than your active stake.

</details>

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}
