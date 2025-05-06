# Recovery Guide

If your node falls too far behind the rest of the network or its database becomes corrupted then you will need to recover it from the latest state snapshot. The Supra Foundation maintains the following publicly accessible snapshots for each production network:

#### Testnet

| **Node Type** | **Scope**             | **Version** | **Snapshot Source**                                         | **Default** |
| ------------- | --------------------- | ----------- | ----------------------------------------------------------- | ----------- |
| RPC           | Full Historical State | v8.0        | testnet-snapshot                                            | Y           |
| <p><br></p>   | Full Historical State | v9.0        | testnet-archive-snapshot                                    | <p><br></p> |
| Pruned RPC    | Latest 1008 Epochs¹   | Coming Soon | Coming Soon                                                 | Y           |
| Validator     | Latest 84 Epochs²     | v9.0        | testnet-validator-snapshot (current default for validators) | Y           |

¹ ≈ 3 months &#x20;

² ≈ 1 week &#x20;

#### Mainnet

| **Node Type** | **Scope**             | **Version** | **Snapshot Source** | **Default** |
| ------------- | --------------------- | ----------- | ------------------- | ----------- |
| RPC           | Full Historical State | v7.1        | mainnet             | Y           |
| Pruned RPC    | Latest 1008 Epochs¹   | Coming Soon | Coming Soon         | Y           |
| Validator³    | Latest 84 Epochs²     | Coming Soon | Coming Soon         | Y           |

\
¹ ≈ 3 months &#x20;

² ≈ 1 week &#x20;

³ Validators can be synced from RPC snapshots. Use the \`mainnet\` snapshot source until validator-specific snapshots are available.

The `manage_supra_nodes.sh sync` command can be used to retrieve the latest snapshot. This command currently defaults to the `testnet-snapshot` bucket for testnet RPC nodes and `testnet-validator-snapshot` for testnet validators, and the `mainnet` bucket for both types of mainnet nodes. A specific bucket can be specified via the `--snapshot-source` optional argument.

After selecting the snapshot source that is relevant to your node’s version and the network that it is participating in, follow the steps below to sync your node.

### Syncing the Latest Snapshot

First, stop your node:

```
docker stop "$CONTAINER_NAME"
```

Then sync the latest snapshot from your selected snapshot source. You can omit the `--snapshot-source` optional parameter if you wish to sync from the default source for the type, version and network of your node. For validators, run:

```
./manage_supra_nodes.sh sync --snapshot-source <snapshot_source> validator <host_supra_home> <network>
```

And for RPC nodes, run:

```
./manage_supra_nodes.sh sync --snapshot-source <snapshot_source> rpc <host_supra_home> <network>
```

After the command completes, [restart](starting-the-supra-node.md) your node and [monitor](monitor-node-synchronization.md) its logs  to ensure that it is syncing the latest blocks correctly. If your node produces an error, first refer to the [Troubleshooting](recovery-guide.md#troubleshooting-guide)  section below, then, if your issue cannot be resolved by the instructions provided there, reach out to [Supra Support](../creating-supradesk-tickets-via-email.md).

## Troubleshooting Guide

### Resolving Database Corruption Errors

The sync script will occasionally leave the database in an inconsistent state after terminating. You will see an error similar to the below upon starting your node when this happens:

```
RpcNode error: Store error: Database Change error:
DBError{Error { message: "Corruption:IO error: No such file or directory:
./configs/rpc_store/453665.sst
./configs/rpc_store/MANIFEST-446443 may be corrupted."}}
```

This can generally be resolved by repeating the sync with the addition of the `--exact-timestamps` optional argument. This argument will cause the script to perform a more thorough sync that will retrieve all files from the remote snapshot that have last-modified timestamps that differ from the timestamps of the corresponding local files. This will generally take longer than a normal sync. It will also continue to sync the same files if run repeatedly due to a limitation in the third-party tools that we rely on for this process. Accordingly, we do not recommend repeatedly syncing with this argument active.

```
./manage_supra_nodes.sh sync --exact-timestamps --snapshot-source <snapshot_source> <node_type> <host_supra_home> <network>
```

Should the error persist, try a standard sync (i.e. without `--exact-timestamps`) one more time. This should now complete quickly.

```
./manage_supra_nodes.sh sync --snapshot-source <snapshot_source> <node_type> <host_supra_home> <network>
```

If the error still persists after the final sync, please contact[ Supra Support](../creating-supradesk-tickets-via-email.md).

\
\


\
\
\
