---
hidden: true
---

# Deposit and Withdraw Funds

After adding contracts, you need to ensure your subscription has sufficient funds to pay for callback gas fees. You can deposit funds through the web interface or using on-chain functions.\


{% tabs %}
{% tab title="Deposit via Web Interface" %}
1. **Navigate to Subscription Management**
   * Go to [supra.com/data/dvrf](https://supra.com/data/dvrf)
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
For EVM Chains

```solidity
solidity

// Deposit funds to susbcription
interface IDeposit {
    function depositFund() external payable;
}

// Check minimum balance requirement (additional)
uint128 minBalance = deposit.getMinBalanceLimit(maxGasPrice, maxGasLimit);
```

For Supra L1

```solidity
move
// Deposit funds to subscription
public entry fun deposit_funds(sender: &signer, amount: u64) {
    deposit::deposit_fund(sender, amount);
}

```
{% endtab %}
{% endtabs %}



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
EVM Chains

```solidity
// Withdraw funds
deposit.withdrawFund(withdrawAmount);

```

Supra L1

```solidity
// Withdraw funds from subscription
public entry fun withdraw_funds(sender: &signer, amount: u64) {
    deposit::withdraw_fund(sender, amount);
}

```
{% endtab %}
{% endtabs %}





####

####
