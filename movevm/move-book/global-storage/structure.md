# Structure

The purpose of Move programs is to read from and write to tree-shaped persistent global storage. Programs cannot access the filesystem, network, or any other data outside of this tree.

In pseudocode, the global storage looks something like:

```move
module 0x42::example {
  struct GlobalStorage {
    resources: Map<(address, ResourceType), ResourceValue>,
    modules: Map<(address, ModuleName), ModuleBytecode>
  }
}
```

Structurally, global storage is a [forest](https://en.wikipedia.org/wiki/Tree_\(graph_theory\)) consisting of trees rooted at an account `address`. Each address can store both resource data values and module code values. As the pseudocode above indicates, each `address` can store at most one resource value of a given type and at most one module with a given name.
