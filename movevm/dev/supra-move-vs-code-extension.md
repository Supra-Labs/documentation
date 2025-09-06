# SupraMove VS Code Extension

Welcome to the official documentation for the **Supra Move VS Code Extension**, a comprehensive developer tool designed to streamline development on the Supra MoveVM.

## Quick Installation

### Via VS Code Marketplace

Visit the [VS Code Marketplace](https://marketplace.visualstudio.com/items/?itemName=Supra.supramove) and click **"Install"** to automatically open VS Code and install it.

### Method 2: Extensions Activity Bar

1. Open VS Code
2. Navigate to the **Extensions view** (`Ctrl+Shift+X` or click the Extensions icon in the Activity Bar)
3. Search for **"Supra Move"**
4. Click **Install** on the official Supra extension

{% hint style="info" %}
After installation, look for the **Supra logo** in your VS Code tray to access In-built explorer features!
{% endhint %}

## Getting Started

### **IntelliSense** and **Syntax Highlighting**

{% stepper %}
{% step %}
#### Create or Open a Move File

Create a new `.move` file or open an existing one in your workspace.

{% hint style="info" %}
**Verify that VS Code sets the file's language to "move" (check the bottom-right corner of the window)**
{% endhint %}
{% endstep %}

{% step %}
#### Start Coding

Begin typing Move code. Try this sample module to see the extension in action:

```
module your_addre::hellomessage {

    use supra_framework::  // IntelliSense will suggest available modules.
    use std::              // Auto-complete shows: vector, signer, table, etc.
    
struct TestStruct has copy // drop down box showing drop, key, store etc.
{ 
pub name: String,
pub age: u32,    
} 

}
```
{% endstep %}
{% endstepper %}

### Using Built-in Supra Explorer

{% hint style="success" %}
**NEW in v3.1.1** - Supra explorer functionality! Supra logo tray integration for easy access to all explorer features.
{% endhint %}

#### **Account Resource & Module Explorer**

* Fetch and display resources & Modules in real-time by account address.
* **Source code viewer** for on-chain modules by clicking on any module name.

#### **Event Explorer**

* **Real-time event tracking** by Tx hash.
* **Event details** and parameter inspection

## Support & Troubleshooting

#### **IntelliSense not activating?**

* Ensure your file has the `.move` extension
* Check that the language mode shows "move" in the status bar

#### **Explorer features not visible?**

* Look for the Supra logo in your VS Code interface
* Make sure you're running the latest version (3.1.1)
* Check the VS Code activity bar and side panels and make sure "SupraMove" is not hidden.

***

## Extension in Action

{% hint style="danger" %}
Demo coming soon!
{% endhint %}
