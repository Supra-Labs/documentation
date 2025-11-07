# Deposit and Withdraw Funds

After adding contracts, you need to ensure your subscription has sufficient funds to pay for callback gas fees. You can deposit funds through the web interface or using on-chain functions.

### **Understanding Your Grant**

Remember, you have a grant from Supra that's used before your deposited funds: This is displayed in UI.\


**How Grants Work:**

1. Callback fees are deducted from your grant first
2. Once grant is exhausted, fees come from your deposited funds
3. Grants cannot be withdrawn - they're only for callback fees
4. Your deposited funds remain untouched until grant is fully used



### Deposit Funds

{% tabs %}
{% tab title="Deposit via Web Interface" %}
1. **Navigate to Subscription Management**
   * Go to [supra.com/data/dvrf](https://supra.com/data/dvrf) &#x20;
   * Connect your wallet (subscription owner)
   * Click on "My Subscription"
2. **Add Funds**
   * Click "Deposit Funds" from the menu
   * Enter the amount to deposit
   * Review current balance and minimum balance requirement.&#x20;
   * Confirm the transaction in your wallet
3. **Verify Deposit**
   * Check updated balance in the interface
   * Ensure balance is above minimum threshold
   * Monitor deposit balance alerts.
{% endtab %}

{% tab title="Deposit via onchain contracts" %}
#### Managing Grants

```solidity
// Check remaining grant
let remaining_grant = deposit::get_client_remaining_grant(client_address);

// Check total grant received
let total_grant = deposit::get_client_total_grant(client_address);

```

#### Deposit funds

```solidity
movedeposit::deposit_fund_v2(
    sender: &signer, 
    client_address: address, 
    deposit_amount: u64
)
```

**Parameters:**

* `sender` - The signer depositing the funds (can be any address)
* `client_address` - The whitelisted wallet address receiving the deposit
* `deposit_amount` - Amount to deposit (in the smallest unit of the native token)\


**Key Features:**

* ✅ Anyone can deposit on behalf of a client
* ✅ Useful for team members or automated top-up services.



**Checking Your Balance**

Before depositing, check your current balance and minimum balance requirements:

```solidity
// Check total deposited funds
let total_balance = deposit::check_client_fund(client_address);

// Check minimum balance requirement
let min_balance = deposit::check_min_balance_client_v2(client_address);000000000000000000
```
{% endtab %}
{% endtabs %}

### **Best Practices**

#### **Deposit Strategy**

**Maintain Buffer**: Keep 3-5x your minimum balance for uninterrupted service.

* 5x minimum = Comfortable buffer for high-volume applications
* 3x minimum = Acceptable for moderate usage
* Below 2x minimum = Risky, may block requests soon

**Regular Monitoring**: Check your balance periodically, especially during high-usage periods

**Automated Top-ups**: Consider implementing an automated deposit script that monitors your balance

**Multiple Depositors**: Designate team members or services that can deposit on your behalf for redundancy

####

### Withdraw Funds

You can withdraw excess funds from your subscription when needed via UI or via onchain functions.&#x20;

{% hint style="info" %}
**Important**: You cannot withdraw funds while you have pending random number requests
{% endhint %}

{% tabs %}
{% tab title="Via Subscription Manager UI" %}
1. Navigate to "My Subscription".
2. Click "Withdraw Funds" from the Menu.
3. Enter withdrawal amount.
4. Confirm transaction in your wallet.
{% endtab %}

{% tab title="Via Onchain Functions" %}


```move
deposit::withdraw_fund(
    sender: &signer, 
    withdraw_amount: u64
)
```

**Parameters:**

* `sender` - Your signer (must be the whitelisted wallet owner)
* `withdraw_amount` - Amount to withdraw (in smallest unit)
{% endtab %}
{% endtabs %}

**Important Restrictions:**

* ❌ Only the whitelisted wallet owner can withdraw their own funds.
* ❌ Grants cannot be withdrawn - only deposited funds.
