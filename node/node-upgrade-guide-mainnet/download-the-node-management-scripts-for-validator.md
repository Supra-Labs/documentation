# Download the Node Management Scripts

Before proceeding with the upgrade, download the latest node management scripts.

To download the script, run the following command:

```
wget -O install_management_scripts.sh https://raw.githubusercontent.com/Entropy-Foundation/supra-nodeops-data/refs/heads/master/node_management/install_management_scripts.sh
```

Then make it executable by running the following command:

```
chmod +x ./install_management_scripts.sh
```

Now run the script using the following command:

```
./install_management_scripts.sh
```

This installs the following scripts:

| `manage_supra_nodes.sh` | This handles node updates, syncing, and restarts.                      |
| ----------------------- | ---------------------------------------------------------------------- |
| `migrate_to_v8.0.2.sh`  | This is a migration script for upgrading the database and CLI profile. |
| `migrate_to_v9.0.x.sh`  | This is a migration script for upgrading the database and CLI profile. |

**Note:**

* **The migrate\_to\_8.0.2.sh is kept, as mainnet is yet to be upgraded to v8.**
