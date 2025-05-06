# FAQs

### How is Supra’s automation different from third-party bots or keepers?

Supra’s automation is built into the validator layer. There are no off-chain agents, bots, or centralized relayers. All task evaluation and execution happens on-chain, natively, and deterministically.

### Can I automate any smart contract?

Yes, as long as the target public entry function specified in the payload transaction during registration is deployed on Supra and matches the argument types of the deployed function.

### What happens if my task exceeds its gas cap or fee cap?

The task will not be executed. If the gas price for a particular block is higher than the `gas_price_cap`, it will not be executed for that block. If the automation fee for the epoch exceeds your fee cap, the task is cancelled.

### Can I cancel a task after registration?

Yes. You can cancel a task using the CLI or by submitting a transaction to the `cancel_task` function. The task is removed at the start of the next epoch.

**NOTE:**

* The **registration fee is non-refundable.**
* If the task is still pending, it is removed immediately without incurring further cost.
* If the task has already become active, the **automation fee for that epoch is still charged** and is **non-refundable**.

### Is there a way to simulate tasks before committing them?

Yes. The Supra CLI supports a `--simulate` option that allows you to preview the outcome of a registration without executing it.This is useful for validating arguments and function calls before submitting the transaction.

**Example**:

```
bash
supra move automation register --simulate \
--task-max-gas-amount 50000 \
--task-gas-price-cap 200 \
--task-expiry-time-secs <TIMESTAMP> \
--task-automation-fee-cap 10000 \
--function-id "0x1::your_module::function_name" \
--args address:<recipient> u64:<amount>
```

### How frequently are tasks evaluated?

Tasks are evaluated at the end of every block. If the condition becomes true during block processing, the task is executed in the same block.

### How is pricing determined during high congestion?

A congestion threshold is set by governance. If the `max_gas` of all the registered tasks exceed this threshold, additional fees are applied based on how much the threshold is surpassed and `max_gas` for each task. In the current charging model, the fee increases polynomially with congestion. Though the degree of the polynimal can be set to a high value by the governance.

### Can I use automation across chains?

At present, automation is scoped to the Supra network. However, cross-chain automation will be supported through SupraNova, allowing source conditions and target actions to occur across different blockchains.

### What happens when a task expires?

It is automatically removed during the next epoch cleanup. No additional actions are needed from the user.

### Are refunds given if a task is not fully used during an epoch?

Yes. If the actual epoch duration is shorter than expected, partial refunds are processed for unused automation time.
