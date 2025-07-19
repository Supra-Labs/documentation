# Supra’s Interoperability Model (HyperNova vs HyperLoop)

SupraNova integrates two types of bridging models depending on the destination chain and required trust assumptions:

| Protocol  | Use Case                                                        | Validation Model                                                          |
| --------- | --------------------------------------------------------------- | ------------------------------------------------------------------------- |
| HyperNova | Trustless bridging between major L1s (e.g., Ethereum to Supra). | Cryptographic proofs (Sync Committee signatures, Merkle proofs, Ancestry) |
| HyperLoop | Fast bridging across Layer2 rollups or high-throughput  chains. | Multi-signature validator committee with game-theoretic security          |

Both models are modular and selected based on use case.

HyperNova is used where trust minimization and security are critical and on chain cryptographic verification of source chain events are possible, while HyperLoop is optimized for speed and cost in environments where a middle network of bridge nodes that are proven to be game theoretically secure needs to be trusted.\
