# Table of contents

* [Overview](README.md)
* [Getting Started](getting-started/README.md)
  * [Introduction to Docker](getting-started/docker.md)
  * [Setup Supra CLI](getting-started/supra-cli-with-docker.md)
  * [Create a Supra Account](getting-started/create-a-supra-account.md)
  * [Testnet Faucet](getting-started/testnet-faucet.md)
  * [Create a Move Package](getting-started/create-a-move-package/README.md)
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
* [Move Book](move-book/README.md)
  * [Getting Started](move-book/getting-started/README.md)
    * [Modules and Scripts](move-book/getting-started/modules-and-scripts.md)
    * [Move Tutorial](move-book/getting-started/move-tutorial.md)
  * [Primitive Types](move-book/primitive-types/README.md)
    * [Integers](move-book/primitive-types/integers.md)
    * [Bool](move-book/primitive-types/bool.md)
    * [Address](move-book/primitive-types/address.md)
    * [Vector](move-book/primitive-types/vector.md)
    * [Signer](move-book/primitive-types/signer.md)
    * [References](move-book/primitive-types/references.md)
    * [Tuples and Unit](move-book/primitive-types/tuples-and-unit.md)
  * [Basic Concepts](move-book/basic-concepts/README.md)
    * [Local Variables and Scope](move-book/basic-concepts/local-variables-and-scope.md)
    * [Equality](move-book/basic-concepts/equality.md)
    * [Abort and Assert](move-book/basic-concepts/abort-and-assert.md)
    * [Conditionals](move-book/basic-concepts/conditionals.md)
    * [While, For, and Loop](move-book/basic-concepts/while-for-and-loop.md)
    * [Functions](move-book/basic-concepts/functions.md)
    * [Structs and Resources](move-book/basic-concepts/structs-and-resources.md)
    * [Constants](move-book/basic-concepts/constants.md)
    * [Generics](move-book/basic-concepts/generics.md)
    * [Type Abilities](move-book/basic-concepts/type-abilities.md)
    * [Uses and Aliases](move-book/basic-concepts/uses-and-aliases.md)
    * [Friends](move-book/basic-concepts/friends.md)
    * [Packages](move-book/basic-concepts/packages.md)
    * [Package Upgrades](move-book/basic-concepts/package-upgrades.md)
    * [Unit Tests](move-book/basic-concepts/unit-tests.md)
  * [Global Storage](move-book/global-storage/README.md)
    * [Structure](move-book/global-storage/structure.md)
    * [Operators](move-book/global-storage/operators.md)
  * [Reference](move-book/reference/README.md)
    * [Standard Library](move-book/reference/standard-library.md)
    * [Coding Conventions](move-book/reference/coding-conventions.md)
* [Aptos to Supra Cheatsheet](aptos-to-supra-cheatsheet.md)
* [Ethereum to Supra Move Cheatsheet](ethereum-to-supra-move-cheatsheet.md)
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
* [Supra Multisig Guide](supra-multisig-guide.md)
* [Supra Fungible Asset (FA) Module](supra-fungible-asset-fa-module.md)
* [Binary Canonical Serialization (BCS) Standard Guide](binary-canonical-serialization-bcs-standard-guide.md)

## Developer Resources <a href="#dev" id="dev"></a>

* [Supra Dapp Templates](dev/supra-dapp-templates.md)
* [Supra Move VS Code Extension](dev/supra-move-vs-code-extension.md)

## Native Oracles

* [Data Feeds (Push)](https://docs.supra.com/oracles/data-feeds/push-oracle#tab-supra-l1-move)
* [dVRF (Randomness)](https://docs.supra.com/oracles/dvrf/v2-guide#tab-supra-move)
* [Automation](https://docs.supra.com/automation)
* [SupraNova Bridge](https://docs.supra.com/supranova)
