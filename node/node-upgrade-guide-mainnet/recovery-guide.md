# Recovery Guide

### Syncing the Latest Snapshot

First, stop your node:

```
docker stop <container_name>
```

Then sync the latest snapshot from your selected snapshot source.You wish to sync from the default source for your node's type, version, and network. For validators, run:

```
./manage_supra_nodes.sh sync --exact-timestamps --snapshot-source mainnet-validator-snapshot validator <host_supra_home> <network>
```

Start the Validator node manually:

```
./manage_supra_nodes.sh start validator <container_name> <host_supra_home>
```

And for RPC nodes, run:

```
./manage_supra_nodes.sh sync --exact-timestamps --snapshot-source mainnet-archive-snapshot rpc <host_supra_home> <network>
```

Start RPC node manually:

```
./manage_supra_nodes.sh start rpc <container_name> <host_supra_home>
```

After the command completes, [restart](broken-reference) your node and [monitor](monitor-node-synchronization.md) its logs  to ensure that it is syncing the latest blocks correctly. If your node produces an error, first refer to the [Troubleshooting](recovery-guide.md#troubleshooting-guide)  section below, then, if your issue cannot be resolved by the instructions provided there, reach out to [Supra Support](../creating-supradesk-tickets-via-email.md).

\
\


\
\
\
