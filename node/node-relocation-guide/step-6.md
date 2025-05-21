# Step 6

## Using a different DNS name or an IP address

If you are using a different DNS name or IP address, follow the steps outlined below on the new instance:

### Retrieve the account address of your validator’s delegation pool:

Find the value of the account\_address field in the `latest_validator_info.json` file. This is the address of the delegation pool that is associated with your validator. Use it to set the `DELEGATION_POOL_ADDRESS` variable in the following command.

### Rotate the Network Address.

Run the following command on the new instance to rotate the network address of your validator.

#### Define Variables

```
# Define Network Type
NETWORK="testnet"  # Change to "mainnet" if running on mainnet

# Specify the IP address.
IP_ADDRESS="your_ip_address"


# New Validator Node Address (IP or DNS)
NEW_ADDRESS="your_ip_or_dns"
# The new instance's address where the validator will be migrated.
# This can be either an IP address (e.g., 192.168.1.100) or a DNS name (e.g., validator.example.com) 

# Operator Profile Name
PROFILE_NAME="your_profile_name" 
# The name of the CLI profile that contains the operator's account address.
# Retrieve this by running on v8.0.2: `supra profile -l`.


# RPC Endpoint
RPC_URL="your_rpc_url"  
# The RPC URL for network communication.  
# Use the appropriate endpoint for testnet or mainnet.


# Delegation Pool Address
DELEGATION_POOL_ADDRESS="your_delegation_pool_address"  
# The delegation pool address associated with your validator.


# Determine the container name based on network type
if [ "$NETWORK" = "mainnet" ]; then
  CONTAINER_NAME="supra_mainnet_$IP_ADDRESS"
else
  CONTAINER_NAME="supra_$IP_ADDRESS"
  fi
```

#### Command to rotate the network address with a new IP address (If changing IP Address)

```
docker exec -it "$CONTAINER_NAME" /supra/supra node rotate-network-address \ --profile "$PROFILE_NAME" --url "$RPC_URL" -s "$NEW_ADDRESS":25000 \ --delegation-pool-address "$DELEGATION_POOL_ADDRESS"
```

**Note:**

* For those who have upgraded to the latest version, v8.0.2, please use the below command to rotate the network address with a new IP address (If changing IP Address)

```
docker exec -it "$CONTAINER_NAME" /supra/supra node rotate-network-address \ --profile "$PROFILE_NAME" --rpc-url "$RPC_URL" -s "$NEW_ADDRESS":25000 \ --delegation-pool-address "$DELEGATION_POOL_ADDRESS"
```

#### Command to rotate the network address with a new DNS name (If changing DNS Name)

```
docker exec -it "$CONTAINER_NAME" /supra/supra node rotate-network-address \ --profile "$PROFILE_NAME" --url "$RPC_URL" -d "$NEW_ADDRESS":25000 \ --delegation-pool-address "$DELEGATION_POOL_ADDRESS"
```

**Note:**

* For those who have upgraded to the latest version, v8.0.2, please use the command to rotate the network address with a new DNS name (If changing DNS Name).

```
docker exec -it "$CONTAINER_NAME" /supra/supra node rotate-network-address \ --profile "$PROFILE_NAME" --rpc-url "$RPC_URL" -d "$NEW_ADDRESS":25000 \ --delegation-pool-address "$DELEGATION_POOL_ADDRESS"
```

\
The change will take effect at the start of the next epoch, so wait for it to begin.

After the new epoch begins, stop the supra validator node running on the old instance.

```
pkill supra
```

**Note:**

* **We must wait until the current epoch ends after completing the transaction.**

\
\\
