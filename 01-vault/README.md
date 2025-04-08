# Vault - Tier 1
You are looking through an old version of the OpenZeppelin implementation of ERC-4626 and notice a vulnerability that requires frontrunning an innocent user. You have been granted a large amount of ETH (say e.g. 1k ETH, but you are free to choose the amount :) ) and want to set up a whitehat bot to execute this exploit and return the funds to the user.

Question:
- a) Describe the vulnerability and the payoffs for an attacker.
- b) Produce code that can check if this vulnerability has occurred in the past and determine how much value was lost, if any.
- c) Write code for the bot that can carry out the exploit (don’t worry about returning user funds).

Solution: 

**a) Vulnerability Summary (Older ERC-4626 Vaults)**

- **Naive Formula**: The vault uses `(depositAmount * totalSupply) / totalAssets` to mint new shares, but doesn’t account for *direct* token transfers that bypass `deposit()`.
- **Front-Run Attack**: An attacker sends a large amount of tokens directly to the vault before a victim’s deposit. This inflates `totalAssets` but leaves `totalSupply` unchanged.
- **Zero Shares for Victim**: Because the ratio is skewed, `(victimDeposit * totalSupply) / (inflated totalAssets)` truncates to zero.
- **Attacker Redeems All**: The attacker still holds nearly 100% of the shares and can withdraw all vault assets—including the victim’s deposit.

**Payoff**: The attacker effectively steals the victim’s deposit, gaining full control over the vault’s assets for minimal cost.

Sources:
1. "https://docs.openzeppelin.com/contracts/5.x/erc4626"
2. "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/audits/2022-10-ERC4626.pdf"