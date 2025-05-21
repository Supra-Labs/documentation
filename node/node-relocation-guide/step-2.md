# Step 2

## Copy Files from the Old Instance

### Define Variables

```
# SSH Private Key Path
SSH_KEY_PATH="ssh_private_key_path"  
# The path to your SSH private key used for secure authentication.  
# This key allows passwordless login to the remote instance.  
# Example: `~/.ssh/id_rsa`

# Supra Validator Home Directory
SUPRA_HOME="path_of_supra_home_folder"  
# The directory where all Supra Validator configuration, key files, and logs are stored.  
# Example: `/home/ubuntu/supra`

# Remote Server Username
REMOTE_USER="username"  
# The username used for SSH login on the remote instance.  
# Example: `ubuntu`
  
# Remote Server IP Address
REMOTE_IP="your_remote_ip"  
# The IP address of the remote machine where the validator is being migrated.  
# Example: `192.168.1.100` 
```

### Transfer Essential Files using rsync

```
# Replace "$SUPRA_HOME/validator_identity.pem" with   "$SUPRA_HOME/node_identity.pem" in the below command if your existing node is running v8.0.2 or higher.
rsync -varzh -e "ssh -i \"$SSH_KEY_PATH\"" \
  "$SUPRA_HOME/smr_public_key.json" \
  "$SUPRA_HOME/smr_secret_key.pem" \
  "$SUPRA_HOME/latest_validator_info.json" \
  "$SUPRA_HOME/validator_identity.pem" \
  "$SUPRA_HOME/validator_public_identity.toml" \
  "$SUPRA_HOME/ca_certificate.pem" \
  "$SUPRA_HOME/server_supra_certificate.pem" \
  "$SUPRA_HOME/server_supra_key.pem" \
  "$SUPRA_HOME/genesis.blob" \
  "$SUPRA_HOME/genesis_configs.json" \
  "$SUPRA_HOME/supra_committees.json" \
  "$SUPRA_HOME/smr_settings.toml" \
  "$REMOTE_USER@$REMOTE_IP:$SUPRA_HOME/"

```

**Note:**

* **You can use other tools to transfer the data. Please keep a backup of the files.**
* **If you want to keep a recent history of your validator node then you copy the remaining files and folder mentioned below.**

**a. `supra_history`**

**b. `Supra_node_logs`**

### Optional: Copy Validator History Files

```
rsync -varzh -e "ssh -i \$SSH_KEY_PATH\"" \
"$SUPRA_HOME/supra_history"\
"$SUPRA_HOME/supra_node_logs"\
"$REMOTE_USER@$REMOTE_IP:$SUPRA_HOME/"
```

**Note:**

* **You can use other tools to transfer the data from the old to the new Instance. Please keep a backup of the files somewhere.**

\
\\
