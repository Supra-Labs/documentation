# CLI Improvements

The following changes have been made in **CLI v0.5.0**:

| Legacy Command   | New Command / Syntax                |
| ---------------- | ----------------------------------- |
| key              | profile                             |
| generate-profile | profile new -i                      |
| import-profile   | profile new -i (interactive import) |
| --url            | --rpc-url                           |

\
&#x20;**Note:**

* **You should begin using --rpc-url in all your CLI commands.**

### RPC API Enhancements

* Clients are required to migrate to **Version 3 RPC APIs** as soon as possible.
* **Version 1 APIs will be deprecated in a future release.**
