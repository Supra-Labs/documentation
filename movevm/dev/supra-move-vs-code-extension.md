# Supra Move VS Code Extension

Welcome to the official documentation for the **Supra Move VS Code Extension,** a one-stop developer tool designed to streamline development on the Supra MoveVM.

## Download & Installation

Visit the VS Code Marketplace and download the[ Supra Extension Here.](https://marketplace.visualstudio.com/items?itemName=Supra.supramove)

OR, **Install via VS Code Extensions Activity Bar:**

* Open VS Code.
* Navigate to the **Extensions view** (click the Extensions icon in the Activity Bar on the left).
* Click the search bar on the top and type name "Supra Move" and click Install.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FkaUNeiOUvY0TI9c9oVPw%2Fuploads%2F5Ine6OmTCcldyEUzMlyJ%2FRecording%202025-04-18%20164745.mp4?alt=media&token=530bd3b7-4392-4b24-8fcf-08c3b403a164" %}

## Getting Started

Once the extension is installed:

1. Create a new `.move` file or open an existing one.

{% hint style="warning" %}
Verify that VS Code sets the file’s language to “move” (check the bottom-right corner of the window). This ensures both syntax highlighting and IntelliSense are activated.
{% endhint %}

2. Start typing Move code. For instance, you can try the following sample module code:

```
module Hello::0xhello {

    use supra_framework::
    use std::

struct TestStruct has copy {
    pub name: String,
    pub age: u32,

}
```

#### As you type, you should see:

* **Syntax Highlighting:** Keywords, functions, namespaces, and variables are color-coded.
* **IntelliSense Suggestions:** When you type `use supra_framework::`, a drop-down appears with available functions such as `chain_id` and when u type `use std:`, a drop-down apperars with available functions as `signer`.

## Key Features

### Syntax Highlighting

* **Enhanced Readability:** Improve code clarity with color-coded syntax that highlights keywords, types, and functions.

### IntelliSense & Auto-Completion

* Get instant suggestions for namespaces, functions, and struct attributes as you type.

## What Developers No Longer Need to Do:

* Function signatures and current module dropdown suggestions are readily available as you code.
* Auto-completion minimizes typos, letting you focus on your application logic.
* Stay within VS Code without the need to reference external documentation repeatedly.

## Extension in Action

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FkaUNeiOUvY0TI9c9oVPw%2Fuploads%2FKPCp4YlECJyez2XG9jIo%2Fdddd.mp4?alt=media&token=9e10b752-774a-43be-be0e-a7e9a0dac1c9" %}
