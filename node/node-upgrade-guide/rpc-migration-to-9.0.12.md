# RPC Migration to 9.0.12

Run the following command to migrate the database and configuration files of your RPC node to the latest version.&#x20;

The database migration requires syncing a snapshot that we have already updated and may take several hours, so you may want to consider running the process in the background.

```
./migrate_to_v9.0.x.sh rpc <container_name> <host_supra_home> <validator_ip>
```

After the script completes:

1. Transfer your custom settings from `config_v8.0.x.toml` to `config.toml.`
2. Enter the updated Docker container and attempt to start the node interactively:

```
docker exec -it <container_name> /bin/bash
./rpc_node start
```

Wait for a couple of minutes to see if the process terminates with an error.&#x20;

If it does, then please contact [Supra Support](../creating-supradesk-tickets-via-email.md) and share a screenshot or text copy of the error.

If no error is shown, stop the node again and follow the [normal steps](starting-the-supra-node.md) to start it again&#x20;

Finally, follow the steps in the[ Monitor Node Synchronization article](monitor-node-synchronization.md)  to check if your node is in sync.

\
\
