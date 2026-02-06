Supra Automation
===

We all know that a smart contract needs to be triggered via an external trigger. The trigger can not come from within the blockchain itself. Many of the classical automation solution sits outside of chain, fetches every block of the target chain, checks for the condition of interest and then sends a trigger to the chain if the condition is true.

So what are the limitations of this approach?
1. By the time the automation nodes fetch the block, checks for condition and then send a trigger, the target chain might have moved ahead a few blocks. You encouter the classic _time to check vs time to execute_ problem. It is possible that by the time your trigger lands on-chain, the condition you are interested may not be _true_. Even if the resultant condition is still true, it is possible that due to this delay you might have lost a huge financial opportunity. For example, a liquidation action which is too late can result in a huge financial loss.
2. As a user, the user has to park some funds with the automation service provider for gas expense. Additionally, a user might have to trust the automation nodes to take action on their behalf, or develop a smart contract which upon receiving the trigger, acts on users' behalf.


With Supra Native automation you can bid good-bye to these limitations. The action happens as soon as the condition becomes _true_ and, it executes with the same authority as the submitter.

You might ask that Supra Automation does not seem to have `if-this-than-that` structure. Well, yes and no. From the infrastructure side, once your task becomes active, validator nodes execute the submitted task at the end of every block. Howeve, one can write the smart contract in a manner so that you achieve `if-this-than-that` effect.

``` rust
public entry fun my_automation_task(user: &signer) {
    if (check_condition_of_interest()) {
        perform_action_of_interest();
    }
}
```

Now, if the condition is _false_, the action is not triggered. Typically, the gas requirements for checking condition is negligible. In all automation solution, these conditions must be check in a kind of _busy-wait_ fashion, we are just making it explicit.

Why did we design it in this fashion? Well, so that if a user wants to perform something at every block, _unconditionally_, they can still do so.


### But I only want to check my conditions once every 5 seconds

This can also be achieved via smart contract programming.

```rust 


struct AutomationState {
    last_checked : u64;
    check_interval: u64;
}

public entry fun my_automation_task(user: &signer) acquires AutomationState {
    let automation_state = borrow_global<AutomationState>(signer::address_of(user));
    if(timestamp::now_seconds() - automation_state.lc < automation_state.check_interval ) {
        return;
    }

    //Now check the condition of interest
    if(check_condition_of_interest()) {
        perform_action_of_interest();
    }
}


```


### What if the condition remains true for several blocks, I only want to execute the action 5 times

Again, this can also be achieved via clever programming.

```rust

struct AutomationState{
    execution_counter: u64;
    task_id : Option<u64>;
}


public entry fun my_automation_task(user:&signer) acquires AutomationState {


let automation_state = borrow_global_mut<AutomationState>(signer::address_of(user));
if(automation_state.execution_counter==0) { return;
// OR you can just stop the task
if (option::is_some(automation_state.task_id)) {
0x1::automation_registry::stop_tasks(user,vector[task_id]);
}
}

if(check_condition_of_interest()) {
    perform_action_of_interest();
    automation_state.execution_counter = automaiton_state.execution_counter - 1;    
}
}


public entry fun change_state(user:&signer, exec_counter: u64, task_id: u64) acquires AutomationState {

    let automation_state = borrow_global_mut<AutomaionState>(signer::address_of(user));
    automation_state.execution_counter = exec_counter;
    automation_state.task_id = option::some(task_id);
} 

public entry fun init_state(user:&signer, exec_counter: u64) {

    move_to(user,AutomationState {execution_counter: exec_counter, task_id : option::none()})

}

```

You do not know the `task_id` before registration. So it can be kept as `Option` and populated later via sending a regular transaction. Other part of the state can also be changed via a regular transaction.

In fact, by doing `init_state` via a regular transaction, before registering your automation would greatly help is reducing `max-gas` parameter that you supply at the time of registration. Why?  Because, the automation registry reserves the gas for your task in the block space, therefore, the _automation fee_ is in proportion to the `max-gas`. By doing operations which creates new storage in a regular transaction, `max-gas` requirement can be greatly reduced for automaiton tasks.


### I want to change behaviour of my automation task without cancelling and re-registering

Well, good news is that your automation task has two ways in which it receives inputs. One is via parameters and other is via global storage. So the behaviour can be dynamically be changed by changing the global state. For example,

```rust

struct AutomationState {
    state : u64;
}

public entry fun my_automation_task(user: &signer) {

    if (state == 1) {
        perform_action1();
    }
    else if (state ==2) {
        perform_action2();
    }
    // ...
    // ...
}

public entry fun change_state(user: &signer, in_state: u64) {

    let automation_state = borrow_global_mut<AutomationState>(signer:;address_of(user));
    automation_state.state = in_state;
}

```

With this trick, the behaviour of the automation task can be changed by sending a regular transaction that changes the input state based on which the action is performed.

Of course, depending upon the need, you may want to perform better access control to your `public entry` methods, such as ensuring that only certain white listed users can change/initialize state or perform certain action.


Note, that `my_automation_task`, the task which is registered with the registry, is always given the `signer` of the submitter. 


### How do I reduce fee for my automation task?

As mentioned above, one of the very important parameter is `max-gas`. By doing accruate gas estimate of the _longest execution path_ in your automation task and supplying that estimate as `max-gas` can greatly reduce the fee charged to your task. 

Creating new storage slots is one of the most expensive operations in Move. Reason being that once a global storage slot is created, it becomes a liability on the network till eternity. Even if later it is `drop`-ed, the archive will have to maintain it.

By moving storage slot creations to a regular transaction, the gas cost of _longest execution path_ can be greatly reduced.

Additionally, doing gas optimization of your automation task would also help in reduction of execution fees charged.