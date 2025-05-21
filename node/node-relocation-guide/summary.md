# Summary

1. Copy your Supra configuration files from the old instance to the new instance. These files are located in SUPRA\_HOME and include:

* Your Operator CLI profile and the following:

```
smr_public_key.json
smr_secret_key.pem
```

* Your Node Identity files:

```
latest_validator_info.json
node_identity.pem
validator_public_identity.toml

```

* The TLS files for the connection between your Validator and RPC nodes:

```
ca_certificate.pem
server_supra_certificate.pem
server_supra_key.pem
```

* The genesis configuration files:

```
enesis.blob
genesis_configs.json
supra_committees.json
```

* The Validator configuration file:

```
smr_settings.toml
```

* Optionally, copy the recent history of your validator. These files may be very large and are not required for your validator to function correctly.

```
supra_history
supra_node_logs
```

* Create a Docker Container from the latest Supra Validator Docker Image for the environment that you are targeting (Testnet or Mainnet), on the new instance.
* Sync the latest database snapshot to the new instance.

The sync process is diff-based, so performing this sync before updating the network address of your Validator and syncing again, will enable it to rejoin the network much sooner once you update the network address.

* If you are using the same DNS name as the network address of your new validator.

1. Update the IP address of the DNS record to that of the new instance.
2. Stop the Validator running on the old instance.

* If you are using an IP address or a new DNS name as the network address of your new validator:

1. Rotate the network address of your Validator using the Supra CLI. This update will be applied when the next epoch begins.
2. Wait for the next epoch to begin.
3. Stop the Validator running on the old instance.
4. Wait for the first state snapshot to be taken after the new epoch begins. Snapshots are taken every half hour.
5. Sync the latest database snapshot to the new instance.
6. Start the Validator on the new instance.
