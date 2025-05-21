# Step 8

## Start the Supra Validator Node

Run the following command to start the Supra Validator Node for your target environment on the new instance.

### Define Variables

```
# Define Network Type
NETWORK="testnet"  # Change to "mainnet" if running on mainnet


# Specify the IP address.
IP_ADDRESS="your_ip_address"


# Determine the container name based on network type
if [ "$NETWORK" = "mainnet" ]; then
  CONTAINER_NAME="supra_mainnet_$IP_ADDRESS"
else
  CONTAINER_NAME="supra_$IP_ADDRESS"
fi
```

### Command

```
docker exec -it "$CONTAINER_NAME" /supra/supra node smr run -r true
```

\
\\
