---
description: >-
  Generate a key profile and view the public/private keys for use by the Supra
  CLI.
---

# Create a Supra Account

{% hint style="danger" %}
On May 2nd, 2025 a new version of the CLI was released. The new image (v9.0.12) introduced an improved profile feature which deprecated the key command of the previous version (v6.3.0) along with an update to the key file structure.\
\
If you have just upgraded, you must execute the following command to migrate your key files to the new version: `supra profile migrate` After migration, you must modify the profiles as shown in step 3.
{% endhint %}

{% stepper %}
{% step %}
### Generate a new key/profile

Execute the following command to generate a new profile/set of keys.&#x20;

{% code title="execute me!" %}
```
supra profile new accountA --network testnet
```
{% endcode %}
{% endstep %}

{% step %}
### Or Import an existing key/profile

If you already have a private key on hand that you wish to use, you can opt to import it rather than generating a new one. To import an existing private key, use the following command:

{% hint style="warning" %}
Profile names must be unique. If you attempt to generate or import a profile using a name that has already been taken, the CLI will throw an error.
{% endhint %}

```
supra profile new accountA <PRIVATE_KEY> --network testnet
```
{% endstep %}
{% endstepper %}

***

## Additional Commands

<details>

<summary>Modify the profile</summary>

The new version of profiles contain the `rpc_url`, `faucet_url`, and `chain_id` associated with the profile. CLI commands will use the stored values of the calling profile for the respective command parameters unless manually overwritten. The above commands to create a new profile or import an existing profile will set the initial values to that of the testnet. If you wish to use the profile on mainnet, you must modify the profile with the mainnet values.\
\
To modify a profile, use the following command:

```
supra profile modify accountA --network <localnet/testnet/mainnet/cust
```

</details>

<details>

<summary>Change the active profile</summary>

{% hint style="info" %}
CLI commands will be executed from the active profile.
{% endhint %}

To activate another profile, use the following command:

```
supra profile activate accountA
```

</details>

<details>

<summary>View stored profile public keys</summary>

{% hint style="info" %}
The active profile will be displayed with a `(*)` by the profile name.
{% endhint %}

If you wish to view existing keys within your current directory, use the following command:

```
supra profile -l
```

</details>

<details>

<summary>View stored profile private keys</summary>

{% hint style="danger" %}
This will print the private keys of all profiles to your terminal. Make sure you are not live streaming, recording, screensharing, etc. before executing this command to prevent compromising your keys.
{% endhint %}

If you wish to view the private key of your profile(s), use the following command:

```
supra profile -l -r
```

</details>
