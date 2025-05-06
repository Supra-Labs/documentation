---
description: >-
  Learn how to configure, manage, and interact with multiple key profiles in the
  Supra CLI.
---

# Profiles

{% hint style="danger" %}
On May 2nd, 2025 a new version of the CLI was released. The new image (v9.0.12) introduced a number of changes, including an improved profile feature which deprecated the key command of the previous version (v6.3.0).\
\
Please be advised that this page has not yet been updated.
{% endhint %}

## What Are CLI Key Profiles?

Profiles in the Supra CLI are configurations that allow you to manage multiple key pairs within your environment. Each profile is tied to a unique public/private key pair and serves as an identity for various operations, such as running a validator node, managing accounts, or accessing specific resources. Profiles can be created, activated, imported, or removed as needed, providing flexibility and security when working with different roles or use cases.

***

## CLI Commands for Profiles

Here's how to use the Supra CLI commands to work with profiles:

{% hint style="info" %}
Not all CLI commands are listed here. Use the `--help` option to see the full list.
{% endhint %}

## List Profiles

View all available profiles in your current environment:

```powershell
supra key list-profile [OPTIONS]
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>] = -r: Reveal secret keys to screen

**Example:**

```powershell
supra key list-profile
```

**Result:**

<pre class="language-powershell"><code class="lang-powershell">------------------------------------ Profile List -----------------------------------
<strong>         exampleAddress
</strong>SmrPublicKey { account_address: "0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d", ed25519_public_key
: 08b957cbd5c17bcca5769c8349a9bc9c5e235a19b2170d23d61360f2d8b7fb1d }
--------------------------------------------------------------------------------------
</code></pre>

## Active a Profile

Set a specific profile as active, so it is used for subsequent CLI commands:

```powershell
supra key activate-profile [OPTIONS] <NAME>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>] = -f: Run without password prompt but read from the \`NODE\_OPERATOR\_KEY\_PASSWORD\` environment variable.
* <mark style="color:orange;"><</mark>NAME<mark style="color:orange;">></mark> = Name of profile to activate

**Example:**

```powershell
supra key activate-profile exampleAddress
```

**Result:**

<pre class="language-powershell"><code class="lang-powershell">------------------------------------ Profile List -----------------------------------
        (*)exampleAddress
SmrPublicKey { account_address: "0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d", ed25519_public_key
: 08b957cbd5c17bcca5769c8349a9bc9c5e235a19b2170d23d61360f2d8b7fb1d }
<strong>--------------------------------------------------------------------------------------
</strong></code></pre>

## Generate a Profile

Create a new profile with a fresh key pair:

```powershell
supra key generate-profile [OPTIONS] <NAME>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>] = -f: Run without password prompt but read from the \`NODE\_OPERATOR\_KEY\_PASSWORD\` environment variable.
* <mark style="color:orange;"><</mark>NAME<mark style="color:orange;">></mark> = Name of the profile to generate

**Example:**

```powershell
supra key generate-profile anotherExampleAddress
```

**Result:**

<pre class="language-powershell"><code class="lang-powershell"><strong>------------------------------------ Profile List -----------------------------------
</strong>        (*)exampleAddress
SmrPublicKey { account_address: "0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d", ed25519_public_key
: 08b957cbd5c17bcca5769c8349a9bc9c5e235a19b2170d23d61360f2d8b7fb1d }

         anotherExampleAddress
SmrPublicKey { account_address: "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d", ed25519_public_key
: 680fffd96e017cc0dacf9350b7258e971e02d27c2e2686483bd2dbee5c13f5f5 }
--------------------------------------------------------------------------------------
</code></pre>

## Import an Existing Profile

Add an existing profile using its private key:

```powershell
supra key import-profile [OPTIONS] <NAME> <PRIVATE_KEY>
```

**Additional Options:**

* \[<mark style="color:orange;">OPTIONS</mark>] = -f: Run without password prompt but read from the \`NODE\_OPERATOR\_KEY\_PASSWORD\` environment variable.
* <mark style="color:orange;"><</mark>NAME<mark style="color:orange;">></mark> = Name of the profile for the newly imported key
* <mark style="color:orange;"><</mark>PRIVATE\_KEY<mark style="color:orange;">></mark> = A private key is a secret code that secures and controls access to a blockchain account.

**Example:**

```powershell
supra key import-profile anotherOneAddress 0x4c88b4a9e5c8924d36a8c9f8e2f9a7d4e6b9f1a2c3d4b5e6f7a8c9d012345678
```

**Result:**

<pre class="language-powershell"><code class="lang-powershell">------------------------------------ Profile List -----------------------------------
        (*)exampleAddress
SmrPublicKey { account_address: "0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d", ed25519_public_key
: 08b957cbd5c17bcca5769c8349a9bc9c5e235a19b2170d23d61360f2d8b7fb1d }

         anotherExampleAddress
SmrPublicKey { account_address: "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d", ed25519_public_key
: 680fffd96e017cc0dacf9350b7258e971e02d27c2e2686483bd2dbee5c13f5f5 }

         anotherOneAddress
SmrPublicKey { account_address: "0x608d604fd055b5428bb81c9a0591cfc62509f9965b88e3c633341e24b09982fb", ed25519_public_key
: 5ac625dee2f520734462774d60c8f9e02ab65b5a8815540e900c86b1cff80b39 }
<strong>--------------------------------------------------------------------------------------
</strong></code></pre>

## Delete Profile

Delete an existing profile from your environment:

```powershell
supra key remove-profile <NAME>
```

**Additional Options:**

* <mark style="color:orange;"><</mark>NAME<mark style="color:orange;">></mark> = Name of the profile to be removed

**Example:**

```powershell
supra key remove-profile anotherOneAddress
```

**Result:**

```powershell
------------------------------------ Profile List -----------------------------------
        (*)exampleAddress
SmrPublicKey { account_address: "0xcdf7ac195644bd834c2f127bf17f970cdc90fec69414fe33fa461c7b8baa668d", ed25519_public_key
: 08b957cbd5c17bcca5769c8349a9bc9c5e235a19b2170d23d61360f2d8b7fb1d }

         anotherExampleAddress
SmrPublicKey { account_address: "0xec07bc3d5c30f85fb8a4c799aba5e2f185e420a2b223f4c1fc2a2a8eeef8192d", ed25519_public_key
: 680fffd96e017cc0dacf9350b7258e971e02d27c2e2686483bd2dbee5c13f5f5 }
--------------------------------------------------------------------------------------
```
