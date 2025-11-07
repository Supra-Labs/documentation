# Request Random Numbers in Supra L1

Ready to get random? Follow these four simple steps to integrate Supra dVRF into your Move module and start requesting verifiable random numbers.

### Prerequisites

* ✅ Whitelisted wallet address with configured gas parameters
* ✅ Deposited sufficient funds in the Deposit Contract
* ✅ Whitelisted your consumer contract address

Before requesting random numbers, ensure you have:



### **Step 1: Import the Supra VRF Interface**

Add the Supra VRF framework dependency to your module's `Move.toml` file.

#### **For Testnet:**

toml

```toml
[dependencies]
supra_vrf = { 
    git = "https://github.com/Entropy-Foundation/vrf-interface", 
    subdir = "supra/testnet", 
    rev = "testnet" 
}
```

#### **For Mainnet:**

toml

```toml
[dependencies]
supra_vrf = { 
    git = "https://github.com/Entropy-Foundation/vrf-interface", 
    subdir = "supra/mainnet", 
    rev = "master" 
}
```

#### **Import in Your Module:**

move

```solidity
module example::example_module {
    use std::string;
    use supra_addr::deposit::{Self, SupraVRFPermit};
    use supra_addr::supra_vrf;
    
    // Your module code
}
```

### **Step 2: Define Your Module Structure**

Create the necessary structs to store your VRF permit and manage random numbers.

```solidity
// Your module's identifier (used as capability type)
struct ClientExample has store {}

// Resource to store the VRF permit
struct PermitCap has key { 
    permit: SupraVRFPermit<ClientExample> 
}

// Resource to store received random numbers
struct RandomNumberList has key {
    random_numbers: table::Table<u64, vector<u256>>
}
```

#### **Initialize Module:**

The `init_module` function is called automatically when your module is published. Use it to set up VRF access:

```solidity
fun init_module(sender: &signer) {
    // Whitelist this module and get VRF permit
    let permit = deposit::init_vrf_module<ClientExample>(sender);
    
    // Store the permit for making VRF requests
    move_to(sender, PermitCap { permit });
    
    // Initialize storage for random numbers
    move_to(sender, RandomNumberList { 
        random_numbers: table::new() 
    });
}
```

### **Step 3: Request Random Numbers**

Use the `rng_request_v2` function to request random numbers. This function returns a nonce that you can use to track your request.

```solidity
supra_vrf::rng_request_v2<T>(
    permit_cap: &SupraVRFPermit<T>,
    callback_function: String,
    rng_count: u8,
    client_seed: u64,
    num_confirmations: u64
): u64
```

#### **Parameters:**

* **`permit_cap`**: Reference to your VRF permit capability (proves authorization)
* **`callback_function`**: Name of your callback function that will receive the random numbers (e.g., `"distribute"`)
* **`rng_count`**: Number of random numbers to generate (maximum 255)
* **`client_seed`**: Your custom seed for additional entropy (can be 0 for default)
* **`num_confirmations`**: Number of block confirmations to wait before generating random numbers (minimum 1)

#### **Returns:**

* **`u64 nonce`**: A unique identifier to track your request and link the callback

#### **Example Request Function:**

```solidity
public entry fun rng_request_v2(
    rng_count: u8, 
    client_seed: u64, 
    num_confirmations: u64
) acquires RandomNumberList, PermitCap {
    // Define callback function name
    let callback_function = string::utf8(b"distribute");
    
    // Get the VRF permit
    let permit = &borrow_global<PermitCap>(@example).permit;
    
    // Request random numbers
    let nonce = supra_vrf::rng_request_v2<ClientExample>(
        permit,
        callback_function,
        rng_count,
        client_seed,
        num_confirmations
    );
    
    // Initialize storage for this nonce's random numbers
    let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
    table::add(random_num_list, nonce, vector[]);
}
```

#### **Request with a Custom Seed:**

```solidity
public entry fun request_random_with_seed(count: u8) acquires RandomNumberList, PermitCap {
    let callback_function = string::utf8(b"distribute");
    
    // Use timestamp or other entropy source as seed
    let custom_seed = 12345; // Could be from external source
    let num_confirmations = 1;
    
    let nonce = supra_vrf::rng_request_v2<ClientExample>(
        &borrow_global<PermitCap>(@example).permit,
        callback_function,
        count,
        custom_seed,
        num_confirmations
    );
    
    // Store nonce for tracking
    let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
    table::add(random_num_list, nonce, vector[]);
}
```

### **Step 4: Implement Callback Function**

Create a callback function to receive and verify the random numbers. The callback must be a `public entry` function with a specific signature.

```solidity
public entry fun callback_name(
    nonce: u64,
    message: vector<u8>,
    signature: vector<u8>,
    caller_address: address,
    rng_count: u8,
    client_seed: u64,
)
```

#### **Callback Validation:**

**CRITICAL**: Always call `supra_vrf::verify_callback` to ensure the random numbers are legitimate and from Supra's VRF service. This prevents malicious actors from calling your callback with fake data.

```solidity
let verified_num: vector<u256> = supra_vrf::verify_callback(
    nonce, 
    message, 
    signature, 
    caller_address, 
    rng_count, 
    client_seed
);
```

#### **Complete Callback Example:**

```solidity
public entry fun distribute(
    nonce: u64,
    message: vector<u8>,
    signature: vector<u8>,
    caller_address: address,
    rng_count: u8,
    client_seed: u64,
) acquires RandomNumberList {
    // SECURITY: Verify the callback is legitimate
    let verified_num: vector<u256> = supra_vrf::verify_callback(
        nonce, 
        message, 
        signature, 
        caller_address, 
        rng_count, 
        client_seed
    );
    
    // Store the verified random numbers
    let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
    let random_numbers = table::borrow_mut(random_num_list, nonce);
    *random_numbers = verified_num;
    
    // Your application logic here
    // Example: Use random numbers for lottery, NFT traits, etc.
}
```

### **Complete Working Example**

Here's a full implementation showing all components together:

```solidity
module example::example_module {
    use aptos_std::table;
    use std::string;
    use std::signer;

    use supra_addr::deposit::{Self, SupraVRFPermit}; 
    use supra_addr::supra_vrf;

    // Module identifier
    struct ClientExample has store {}

    // Storage for random numbers
    struct RandomNumberList has key {
        random_numbers: table::Table<u64, vector<u256>>
    }

    // Storage for VRF permit
    struct PermitCap has key { 
        permit: SupraVRFPermit<ClientExample> 
    }

    // Error codes
    const E_NOT_INITIALIZED: u64 = 1;
    const E_INVALID_COUNT: u64 = 2;

    // Initialize module (called automatically on publish)
    fun init_module(sender: &signer) {
        // Whitelist module and get permit
        let permit = deposit::init_vrf_module<ClientExample>(sender); 

        // Initialize storage
        move_to(sender, RandomNumberList { 
            random_numbers: table::new() 
        }); 
        move_to(sender, PermitCap { permit });
    }

    // Request random numbers
    public entry fun rng_request_v2(
        rng_count: u8, 
        client_seed: u64, 
        num_confirmations: u64
    ) acquires RandomNumberList, PermitCap {
        // Validate input
        assert!(rng_count > 0 && rng_count <= 255, E_INVALID_COUNT);
        
        // Define callback function
        let callback_function = string::utf8(b"distribute");
        
        // Request random numbers
        let nonce = supra_vrf::rng_request_v2<ClientExample>(
            &borrow_global<PermitCap>(@example).permit,
            callback_function,
            rng_count,
            client_seed,
            num_confirmations
        );
    
        // Prepare storage for callback
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        table::add(random_num_list, nonce, vector[]);
    }

    // Callback function - receives random numbers
    public entry fun distribute(
        nonce: u64,
        message: vector<u8>,
        signature: vector<u8>,
        caller_address: address,
        rng_count: u8,
        client_seed: u64,
    ) acquires RandomNumberList {
        // Verify callback authenticity
        let verified_num: vector<u256> = supra_vrf::verify_callback(
            nonce, 
            message, 
            signature, 
            caller_address, 
            rng_count, 
            client_seed
        );
        
        // Store verified random numbers
        let random_num_list = &mut borrow_global_mut<RandomNumberList>(@example).random_numbers;
        let random_numbers = table::borrow_mut(random_num_list, nonce);
        *random_numbers = verified_num;
    }

    // View function to retrieve random numbers by nonce
    #[view]
    public fun get_rng_number_from_nonce(nonce: u64): vector<u256> acquires RandomNumberList {
        let random_num_list = borrow_global<RandomNumberList>(@example);
        *table::borrow(&random_num_list.random_numbers, nonce)
    }

    // Check if random numbers are ready for a nonce
    #[view]
    public fun has_random_numbers(nonce: u64): bool acquires RandomNumberList {
        let random_num_list = borrow_global<RandomNumberList>(@example);
        table::contains(&random_num_list.random_numbers, nonce)
    }
}
```

### Next Steps

After implementing the request functionality, you can:

* Monitor your balance using Deposit Contract view functions
* Update gas parameters for your contracts
* Scale your random number requests based on your application needs

The random numbers generated are cryptographically secure and verifiable, making them suitable for gaming, NFT minting, and other blockchain applications requiring true randomness.
