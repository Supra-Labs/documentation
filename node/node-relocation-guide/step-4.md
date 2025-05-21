# Step 4

## Sync the latest snapshot on the new instance before updating the validator address

1. Please sync the latest snapshot before updating your validator's network address.
2. Download the snapshot data from the Supra Validator node in your target environment on the new instance.
3. Include the `smr_storage folder` and ensure the snapshot matches the new epoch data.

### Install rclone

```
curl https://rclone.org/install.sh | sudo bash
mkdir -p ~/.config/rclone/
touch ~/.config/rclone/rclone.conf
```

### Define Variables

```
SUPRA_HOME="path_of_supra_home_folder"
```

#### Testnet

Run the following command to configure rclone, which allows secure transfer and synchronization of files between your system and remote storage.

```
cat <<EOF >> ~/.config/rclone/rclone.conf
[cloudflare-r2]
type = s3
provider = Cloudflare
access_key_id = 229502d7eedd0007640348c057869c90
secret_access_key = 799d15f4fd23c57cd0f182f2ab85a19d885887d745e2391975bb27853e2db949
region = auto
endpoint = https://4ecc77f16aaa2e53317a19267e3034a4.r2.cloudflarestorage.com
acl = private
no_check_bucket = true
EOF
```

Execute the following command to download the snapshot for the new instance.

#### Sync Testnet Snapshot

```
rclone sync cloudflare-r2:testnet-snapshot/snapshots/store "$SUPRA_HOME/smr_storage/" --progress
```

### Mainnet

```
cat <<EOF >> ~/.config/rclone/rclone.conf
[cloudflare-r2]
type = s3
provider = Cloudflare
access_key_id = c64bed98a85ccd3197169bf7363ce94f
secret_access_key = 0b7f15dbeef4ebe871ee8ce483e3fc8bab97be0da6a362b2c4d80f020cae9df7
region = auto
endpoint = https://4ecc77f16aaa2e53317a19267e3034a4.r2.cloudflarestorage.com
acl = private
no_check_bucket = true
EOF
```

Execute the following command to download the snapshot for the new instance.

### Sync Mainnet Snapshot

```
rclone sync cloudflare-r2:mainnet-snapshot/snapshots/store "$SUPRA_HOME/smr_storage/" --progress
```

**Note:**

* **We need to verify if the snapshot data has been fully downloaded into \`smr\_storage\` for the `mainnet` or `Testnet`**
* **Verification: Run the clone command multiple times until you observe that the downloaded data is zero bytes.**
* **This will allow your validator to rejoin the network more quickly once the address update is complete, as the sync process will ensure that it has the latest data before the update.**

\
\\
