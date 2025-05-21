# Step 3

## Setting Up a Docker Container on the New Instance

Execute the following Docker command to initialize the container with the latest Supra Validator Docker image for your target environment on the new instance.

### Latest Validator Docker Images:

```
# Testnet
asia-docker.pkg.dev/supra-devnet-misc/supra-testnet/validator-node:v8.0.2
# Mainnet
asia-docker.pkg.dev/supra-devnet-misc/supra-mainnet/validator-node:v7.1.7
```

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

### Deploy the Container

```
docker run \  
  --name "$CONTAINER_NAME" \  
  -v ./supra_configs:/supra/configs \  
  -e "SUPRA_HOME=/supra/configs" \  
  -e "SUPRA_LOG_DIR=/supra/configs/supra_node_logs" \  
  -e "SUPRA_MAX_LOG_FILE_SIZE=4000000" \  
  -e "SUPRA_MAX_UNCOMPRESSED_LOGS=5" \  
  -e "SUPRA_MAX_LOG_FILES=20" \  
  --net=host \  
  -itd "$LATEST_DOCKER_IMAGE"
```

\
\\
