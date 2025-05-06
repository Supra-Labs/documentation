---
description: >-
  The Supra CLI Cheat Sheet provides a quick reference for developers to
  efficiently navigate and use key commands in Supra’s CLI.
---

# Cheat Sheet

{% hint style="danger" %}
On May 2nd, 2025 a new version of the CLI was released. The new image (v9.0.12) introduced a number of changes, including an improved profile feature which deprecated the key command of the previous version (v6.3.0).\
\
Please be advised that this page has not yet been updated.
{% endhint %}

***

{% hint style="info" %}
Not all CLI commands are listed here. Use the `--help` option to see the full list.
{% endhint %}

## Profiles Commands

Manage and interact with profiles, configure keys, and set up secure blockchain identities.

## List Profiles

```powershell
supra key list-profile [OPTIONS]
```

* `-r, --reveal-secrets`: Reveal secret keys to the screen.\
  **Description**: Displays all profiles currently available in your environment.

## Active Profile

Sets the specified profile as the active one for CLI commands.

```powershell
supra key activate-profile <NAME>
```

`<NAME>`: Name of the profile to activate.

## Generate Profile

Creates a new profile and generates a public/private key pair.

```
supra key generate-profile <NAME>
```

`<NAME>`: Name of the profile to generate.

## Import Profile

Imports an existing profile using its private key.

```powershell
supra key import-profile <NAME> <PRIVATE_KEY>
```

`<NAME>`: Name of the profile to import.

`<PRIVATE_KEY>`: Hexadecimal format private key.

## Remove Profile

Deletes a profile from your environment.

```powershell
supra key remove-profile <PROFILE_NAME>
```

`<PROFILE_NAME>`: Name of the profile to remove.

***

## Accounts Commands

Perform essential operations such as creating accounts, funding them, checking balances, and transferring assets.

## Create Account

Creates a new on-chain account within the Supra blockchain.

```powershell
supra move account create --profile <PROFILE_NAME> --url <RPC_URL>
```

`<PROFILE_NAME>`: Profile to use.

`<RPC_URL>`: Full node URL on the network.

## Fund Account with Faucet

Funds the specified account with testnet $SUPRA tokens.

```powershell
supra move account fund-with-faucet --profile <PROFILE_NAME> --url <RPC_URL>
```

`<PROFILE_NAME>`: Profile to use.

`<RPC_URL>`: Full node URL on the network.

## Check Balance

Checks the balance of the specified account.

```powershell
supra move account balance --profile <PROFILE_NAME> --url <RPC_URL>
```

`<PROFILE_NAME>`: Profile to use.

`<RPC_URL>`: Full node URL on the network.

## Transfer Tokens

Transfers tokens from one account to another.

```powershell
supra move account transfer --account <ACCOUNT> --amount <AMOUNT> --url <RPC_URL>
```

`<ACCOUNT>`: Address to send SUPRA to.

`<AMOUNT>`: Amount of Quants (10^-8 SUPRA) to transfer.

`<RPC_URL>`: Full node URL on the network.

***

## Tools Commands

Handle tasks related to building, compiling, testing, and deploying Move packages and smart contracts.

## Compile Move Package

Compiles the Move package in the specified directory.

```powershell
supra move tool compile --package-dir <PACKAGE_DIR>
```

`<PACKAGE_DIR>`: Path to a Move package (folder with a `Move.toml` file).

## Publish Move Package

Publishes the compiled Move package to the blockchain.

```powershell
supra move tool publish --profile <PROFILE_NAME> --package-dir <PACKAGE_DIR> --url <RPC_URL>
```

`<PROFILE_NAME>`: Profile to use for publishing.

`<PACKAGE_DIR>`: Path to a Move package.

`<RPC_URL>`: Blockchain node URL.

## Run Smart Contract Function

Executes a smart contract function with the provided arguments.

```powershell
supra move tool run --function-id ‘<YOUR_ADDRESS>::<SMART_CONTRACT_NAME>::<FUNCTION_NAME>’ --args <ARGS> --url <RPC_URL>
```

`<YOUR_ADDRESS>`: Address executing the function.

`<SMART_CONTRACT_NAME>`: Contract containing the function.

`<FUNCTION_NAME>`: Function being executed.

`<ARGS>`: Arguments for the function.

`<RPC_URL>`: Blockchain node URL.

## Run Unit Tests

Runs unit tests for the Move package in the specified directory.

```powershell
supra move tool test --package-dir <PACKAGE_DIR>
```

`<PACKAGE_DIR>`: Path to a Move package.

## View Smart Contract Data

Executes a read-only view function to query contract data.

```
supra move tool view --function-id ‘<YOUR_ADDRESS>::<SMART_CONTRACT_NAME>::<VIEW_FUNCTION>’ --args <ARGS> --url <RPC_URL>
```

`<YOUR_ADDRESS>`: Address making the query.

`<SMART_CONTRACT_NAME>`: Contract containing the view function.

`<VIEW_FUNCTION>`: Read-only function to retrieve data.

`<ARGS>`: Query-specific arguments.

`<RPC_URL>`: Blockchain node URL.
