# `interact_with_state`

> `pub fn interact_with_state<F, +Drop<F>, impl func: core::ops::FnOnce<F, ()>, +Drop<func::Output>>(contract_address: ContractAddress, f: F,) -> func::Output`

Allows to use `contract_state_for_testing` for a deployed contract, enabling interaction with its state in tests.

To make it possible to use this cheatcode, it is necessary to take care of the following:
- The contract must be visible in the test context
- Storage variables that you want to access must be public
- If testing internal contract functions, the respective trait must be imported
- Storage related traits must be imported, such as `StoragePointerReadAccess` and `StoragePointerWriteAccess`

## Examples

### Accessing and modifying contract state

You can use `interact_with_state` to access and modify a deployed contract's state. After deploying a contract, you can pass its address to `interact_with_state` along with a closure. Inside the closure, you can use `contract_state_for_testing()` to get access to the contract's state and then read from or write to its storage variables.

```rust
#[starknet::contract]
mod Counter {
    #[storage]
    struct Storage {
        count: u64,
    }
}
```

```rust
// 1. Deploy your contract
let contract = declare("Counter").unwrap().contract_class();
let (contract_address, _) = contract.deploy(array![]).unwrap();

// 2. Use `interact_with_state` to access and modify the contract's state
interact_with_state(
    contract_address,
    || {
        // 3. Get access to the contract's state
        let mut state = Counter::contract_state_for_testing();

        // 4. Read from storage
        let current_count = state.count.read();

        // 5. Write to storage
        state.count.write(current_count + 1);
    }
);
```
