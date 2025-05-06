# Table of contents

* [Overview](README.md)
* [Getting Started](getting-started/README.md)
  * [Install Supra CLI with Docker](getting-started/supra-cli-with-docker.md)
  * [Create a Supra Account](getting-started/create-a-supra-account.md)
  * [Testnet Faucet](getting-started/testnet-faucet.md)
  * [Create a Move Package](getting-started/create-a-move-package/README.md)
    * [Initialize a Package](getting-started/create-a-move-package/initialize-a-package.md)
    * [Write a Module](getting-started/create-a-move-package/write-a-module.md)
    * [Compile and Publish](getting-started/create-a-move-package/compile-and-publish.md)
    * [Interact with a Package](getting-started/create-a-move-package/interact-with-a-package.md)
  * [Create a dApp with StarKey](getting-started/your-first-dapp-with-starkey.md)
* [Network Information](network-information.md)
* [Token Standards](token-standards.md)
* [Learn Move 101](learn-move/README.md)
  * [Getting Started with Move](learn-move/move-101.md)
  * [Unsigned Integers in Move](learn-move/unsigned-integers-in-move.md)
  * [Math Operations in Move](learn-move/math-operations-in-move.md)
  * [Using Vectors in Move](learn-move/using-vectors-in-move.md)
  * [Reading Resource Data with borrow\_global](learn-move/reading-resource-data-with-borrow_global.md)
  * [Passing Data in Move: Value vs. Reference](learn-move/passing-data-in-move-value-vs.-reference.md)
  * [Adding Elements with vector::push\_back](learn-move/adding-elements-with-vector-push_back.md)
  * [Emitting Events with event::emit](learn-move/emitting-events-with-event-emit.md)
* [CLI Commands](cli-commands/README.md)
  * [Cheat Sheet](cli-commands/cheat-sheet.md)
  * [Profiles](cli-commands/profiles.md)
  * [Accounts](cli-commands/accounts.md)
  * [Tools](cli-commands/tools.md)
* [TypeScript SDK](typescript-sdk/README.md)
  * [Guides](typescript-sdk/guides/README.md)
    * [Create Supra Accounts](typescript-sdk/guides/create-supra-accounts.md)
    * [Publish a Package](typescript-sdk/guides/publish-a-package.md)
  * [Documentation](https://sdk-docs.supra.com/index.html)
  * [Repository](https://github.com/Entropy-Foundation/supra-l1-sdk/tree/master)
* [Rest API](rest-api/README.md)
  * [Mainnet](rest-api/mainnet/README.md)
    * ```yaml
      type: builtin:openapi
      props:
        models: false
      dependencies:
        spec:
          ref:
            kind: openapi
            spec: supra-mainnet-api
      ```
  * [Testnet](rest-api/testnet/README.md)
    * ```yaml
      type: builtin:openapi
      props:
        models: false
      dependencies:
        spec:
          ref:
            kind: openapi
            spec: supra-testnet-api
      ```

## Developer Resources <a href="#dev" id="dev"></a>

* [Supra Dapp Templates](dev/supra-dapp-templates.md)
* [Supra Move VS Code Extension](dev/supra-move-vs-code-extension.md)

## Links

* [Supra DevHub](https://github.com/Entropy-Foundation/supra-dev-hub/tree/main)
* [SupraScan Block Explorer](https://suprascan.io)
* [StarKey Wallet](https://www.starkey.app)
* [Live Data Feeds](https://supraoracles.com/data)
* [Whitepapers](https://supraoracles.com/whitepapers)
* [Security Audits](https://github.com/Entropy-Foundation/security-audits)
* [Supra's Official GitHub](https://github.com/Entropy-Foundation/)
