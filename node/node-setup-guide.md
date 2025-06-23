---
hidden: true
---

# Node Setup Guide

{% hint style="danger" %}
Please note that Node Operation is currently permissioned to select node operators and is not available to the public at this time.
{% endhint %}

### Introduction

Welcome to Supra! This manual will guide you through the process of setting up and operating a node on our blockchain network for our inaugural Testnet. Our system leverages Docker for ease of deployment and a Chrome extension wallet for managing your test tokens.

### Prerequisites

* Ensure you are a member of our [Supra Discord Testnet Communication Channel](https://discord.com/channels/850682587273625661/1200549269237661726)
* Ask the Supra team in Discord channel to grant read access to the Validator node image repository.
* Install Docker Engine or Docker Desktop on your machine using the following [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/).
* You might need to follow the post installation steps ([https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)) too.
* Install Google Cloud CLI (gcloud CLI) using this [https://cloud.google.com/sdk/docs/install#linux](https://cloud.google.com/sdk/docs/install#linux).
* Install _wget_, _shasum_ and _jq_.
* Run the following commands to authorize gcloud and Docker to pull the image from the image repository:
  * _gcloud auth login_
  * _gcloud auth configure-docker asia-docker.pkg.dev_
* Minimum Hardware Requirements:
  * Cores: 32
  * RAM: 64G
  * CPU: Intel Xeon (or) AMD EPYC CPU @ 3.2GHz speed or higher
  * Architecture: x86/64
  * Disk Type: SSD
  * Minimum Disk Size: 2TB
  * Network Bandwidth: 2 Gbps or more

## The onboarding process has been divided into 5 phases.

1. **Select Phase I - Generate Operator Keys**

* smr and validator (SK, PK) are generated.
* Operator keys are uploaded to the node-Ops data repo.

2. **Select Phase II - Perform Signing Operation**

* Supra\_committee.json is signed.
* The generated signature will need to be uploaded to the nodeOp data repo.

3. [**Select Phase III - Create Genesis Blob**](node-setup-guide.md#select-phase-iii-create-genesis-blob)

* GenesisBlob is created and required to start the node.

4. [**Select Phase IV - Start the node and other services**](node-setup-guide.md#select-phase-iv-start-the-node-and-other-services)

* Node and Grafana services are started.

5. [Select Phase V - Restart the network using snapshot](node-setup-guide.md#select-phase-v-restart-the-network-using-a-snapshot)

##

## Step 1: Download the Onboarding Script

* You can download script[ ](https://raw.githubusercontent.com/Entropy-Foundation/supra-nodeops-data/master/scripts/prepare_dkgs_definition.sh)onboarding\_round\_6.sh by running the following command, and save the script in the current working directory (location doesn’t matter).
* Ensure you’ve installed prerequisites \`wget\` as per your OS distributions.

```bash
wget https://raw.githubusercontent.com/Entropy-Foundation/supra-nodeops-data/master/scripts/onboarding_round_6.sh
chmod +x onboarding_round_6.sh
```

## Step 2: Executing the Onboarding script

* Run the script
* An example of successful execution

Command:

```
./onboarding_round_6.sh
```

### More Information on Prerequisites

* The script will check the installation of the following on your machine
  * expect
  * gCloud
    * gcloud auth login
    * gcloud auth configure-docker asia-docker.pkg.dev
  * Docker
  * toml-cli
  * Openssl
  * Zip
* Make sure, to not run the script with the root user.
* If all checks are successful then it will show **All Checks Passed: ✔**

Output:

```
CHECKING PREREQUISITES

All Checks Passed: ✔ 

1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase v - Restart the network using snapshot
6. Exit

Enter your choice:
```

**And since all of you have completed Phase 1 and 2 already, let's begin directly from Phase 3.**

## Select Phase III - Create Genesis Blob

Note:

1. Please start phase 3, once announced by Supra Team on Discord.
2. Make sure you are on the root path where the onboarding\_round\_6.sh script is present

Command :

<pre class="language-bash"><code class="lang-bash"><strong>wget https://raw.githubusercontent.com/Entropy-Foundation/supra-nodeops-data/refs/heads/master/scripts/update_version_round_6_1.sh
</strong>chmod +x update_version_round_6_1.sh
</code></pre>

Command :

```
./update_version_round_6_1.sh
```

**Note**: Make sure you have run the above commands before starting the script to avoid the issue at the time of running.

Command :

```
./onboarding_round_6.sh
```

Output:

```
All Checks Passed: ✔ 


1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase v - Restart the network using snapshot
6. Exit

Enter your choice:
```

Now **Enter 3 to enter into Phase3**

**Note** : Make sure that you don’t exit the process, as generating genesis will take at least 40 minutes

Output:

```
✔ Container is running.
 
Hashes have been written to Hashmap_phase_1_latest.toml
Performing hash check for phase 1


✔ Success: Verification of phase 1
Checking if phase 2 files are present
✔ Success: Verification of phase 2
Checking Phase 3 File is present
Genesis file is not present
Phase 3 fresh start
Please provide a GitHub repo link (.zip format):
```

Enter the following GitHub zip file link:

```
https://github.com/Entropy-Foundation/supra-nodeops-data/archive/refs/heads/master.zip
```

Output:

```
Generate Genesis Blob
...........................
Successfully verified signature
'genesis.blob' file written to disk at "/supra/configs/genesis.blob"

____________________________________________________________________________________________________________
                                         ✔ Phase 3: Completed                                                    
____________________________________________________________________________________________________________
```

\\

## Select Phase IV - Start the node and other services

Note: Make sure you are on the root path where the onboarding\_round\_6.sh script is present

Command :

```
./onboarding_round_6.sh
```

Output:

```
All Checks Passed: ✔ 

1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase V - Restart the network using snapshot
6. Exit

Enter your choice:

```

Now **Enter 4 to enter into Phase4**

Output :

```
Enter your choice: 4
Adding Grafana dashboard...
Select your system type
1. Ubuntu/Debian Linux
2. Amazon Linux/Centos Linux
Enter your system type: 
```

Select Your System Type: 1 (Ubuntu/Debian Linux) or 2 (Amazon Linux/Centos Linux)

Output :

```
Adding Grafana dashboard...
........................................
........................................
Title name is Logs-smr-moonshot-benchmark-5-34.131.137.223
Please enter the log file path:


Command : 
Enter Your Log File Path: {/home/ubuntu/supra_configs/supra_node_logs}
Output : 
You entered: /home/ubuntu/supra_configs/supra_node_logs
Is this correct? (y/n) 
```

Enter Your "y" to Confirm

Output :

```
Is this correct? (y/n) y
Log file path confirmed: /home/ubuntu/supra_configs/supra_node_logs
.........................................
.........................................
Creating Dashboard
{"folderUid":"cfdd4bae-eaab-4e4b-a60e-ebb63bcc9ada","id":996,"slug":"metric-smr-moonshot-benchmark-5-34-131-137-223","status":"success","uid":"17ff5dd5-bbcd-469b-97c4-ccbdbe958534","url":"/d/17ff5dd5-bbcd-469b-97c4-ccbdbe958534/metric-smr-moonshot-benchmark-5-34-131-137-223","version":1}
Please specify e-mail for dashboard access: "Your Email Id for the Grafana"
```

Enter Your e-mail for Dashboard Access: "Your Email Id for the Grafana"

Output : \\

```
Grafana dashboard url:  https://monitoring.services.supra.com/dashboards/f/cfdd4bae-eaab-4e4b-a60e-ebb63bcc9ada
Starting the Node
Starting logger runtime at localhost:41371. Use `nc localhost 41371` to change log level.
Build Version: 
pkg_version:0.4.0
tag:v5.0.0
commit_hash:fbac7fd5
build_time:2024-06-26 17:27:42 +00:00
build_env:rustc 1.78.0 (9b00956e5 2024-04-29),stable-x86_64-unknown-linux-gnu
Enter your password: 
Network started, waiting for enough peers to connect...
Validator is running...

Press Enter to continue...

```

**Note**: To check the logs, we need to exit the script by pressing Enter.

\
Output:

```
All Checks Passed: ✔ 

1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase V - Restart the network using snapshot
6. Exit

Enter your choice:

```

Now press 6 to exit the script.

## Select Phase V - Restart the network using a snapshot

**Note**: Make sure you are on the root path where the onboarding\_round\_6.sh script is present

```
./onboarding_round_6.sh
```

Output:

```
All Checks Passed: ✔ 

1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase V - Restart the network using snapshot
6. Exit

Enter your choice:

```

Now Enter 5 to enter into Phase5

```
Starting the Node
Starting logger runtime at localhost:41371. Use `nc localhost 41371` to change log level.

Build Version: 

pkg_version:0.4.0
tag:v6.0.0
commit_hash:fbac7fd5
build_time:2024-06-26 17:27:42 +00:00

build_env:rustc 1.78.0 (9b00956e5 2024-04-29),stable-x86_64-unknown-linux-gnu

Enter your password: 
Network started, waiting for enough peers to connect...
Validator is running...

Press Enter to continue...
```

**Note**: To check the logs, we need to exit the script by pressing Enter.

```
All Checks Passed: ✔ 

1. Select Phase I - Generate Operator Keys
2. Select Phase II - Perform Signing Operation
3. Select Phase III - Create Genesis Blob
4. Select Phase IV - Start the node and other services
5. Select Phase V - Restart the network using snapshot
6. Exit

Enter your choice:

```

Now press 6 to exit the script.

\
Step 3: Identifying Successful Onboarding
-----------------------------------------

Please make sure the following keywords are present in the logs. You can use a text editor or a command line tool to search the logs.

1. Consensus check:

```
Timeout reached for View
```

If present, this indicates that your node has successfully joined the network and is moving along with the consensus rounds.\\

2. Block production check:

```
Block height 
```

This indicates that your node is producing blocks along the network and has successfully onboarded.

## Step 4: Install the Chrome Extension Wallet

1. Obtain the extension from a Supra team member.
2. Extract the extension package
3. Open Chrome->Settings->Extensions
4. Click “_**Load Unpacked**_" (top-left corner)
5. Select the folder where the extension has been unpacked

## Step 5: Create a Wallet Address

1. Open the installed Chrome extension.
2. Follow the instructions to create a new wallet.
3. Write down and securely store the mnemonic phrase provided. This phrase is essential for recovering your wallet down the road.

## Step 6: Collect Test Tokens from the Testnet Faucet

Wallet Interface:

1. Select _**SUPRA**_ network from the list of the networks in the wallet.
2. Click on the _**Collect**_ button to collect tokens.

Swagger Interface:

1. Access our Faucet API documentation via the following link: [https://rpc-testnet.supra.com/rpc/v1/wallet/faucet/{account\_address}](https://rpc-testnet.supra.com/rpc/v1/wallet/faucet/%7Baccount_address%7D)
2. Experiment with the API endpoint by inputting your wallet address and requesting tokens.
3. To keep track of your wallet balance, refer to the Balance API Documentation found here: [https://rpc-testnet.supra.com/rpc/v1/accounts/{account\_address}/resources/0x1%3A%3Acoin%3A%3ACoinStore%3C0x1%3A%3Asupra\_coin%3A%3ASupraCoin%3E](https://rpc-testnet.supra.com/rpc/v1/accounts/%7Baccount_address%7D/resources/0x1%3A%3Acoin%3A%3ACoinStore%3C0x1%3A%3Asupra_coin%3A%3ASupraCoin%3E).

Note: Make sure to select the Testnet endpoint

### Troubleshooting and Support

If you encounter any issues, please feel free to contact us on our Discord channel.

### Conclusion

Thank you for participating as a node operator in the Supra ecosystem. We sincerely appreciate your contribution to the strength and security of our network.

{% hint style="danger" %}
Important: Please make sure you read and understand [Terms of Use](https://supra.com/terms-of-use/) before start using Supra products and services.
{% endhint %}
