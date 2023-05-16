# Issues

Key issues in lending and borrowing platforms can be classfied into

| #   | Issue                                                       |
| --- | ----------------------------------------------------------- |
| 1   | Borrowers' collateral is liquidated before default          |
| 2   | Borrowing closed without repayment                          |
| 3   | Repayments paused while liquidation is active               |
| 4   | Collateral pause stops existing repayment and liquidations  |
| 5   | Liquidator takes collateral without proportionate repayment |
| 6   | Infinite loan roll-over                                     |
| 7   | Repayments sent to 0 address                                |
| 8   | Borrowers permanently unable to repay loan                  |
| 9   | Borrower repayment only partially credited                  |

---

## 1. Borrowers collateral is liquidated before default

Liquidation happens when - value of collateral posted by borrower drops in value - borrower fails to make a repayment as scheduled

When either of these conditions is not met, borrower should not be liquidated. Any liquidation leads to a loss to borrower because the liquidation fees is deducted from collateral value. So all issues relating to early liquidation of a borrower when the above 2 conditions are NOT violated are critical flaws in a lending protocol

Following cases fall in this category

| #                           | Protocol      | Issue                                                                                                        |
| --------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------ |
| [0001](../database/0001.md) | Teller V2     | Ignores payment cycle duration & only considers payment default duration                                     |
| [0002](../database/0002.md) | Papr          | maxLTV = Liquidation LTV - borrower taking loan = max LTV can be immediately liquidatable                    |
| [0003](../database/0003.md) | AbraNFT       | Lender can change oracle that prices collateral when loan becomes outstanding                                |
| [0004](../database/0004.md) | Blueberry     | Understates the underlying value by ignoring interest earned on underlying                                   |
| [0005](../database/0005.md) | Union Finance | A repeat borrower can be instantly liquidated because the `lastRepay` is not updated from previous borrowing |

---

## 2. Borrowers create loan terms such that borrower cannot be liquidated

This is another peculiar case with decentralized lending platforms where borrowers are allowed to create their own borrowing terms and lenders provide a matching offer that is consistent with borrower terms. Such cases can create another problem - defining terms such that borrowers cannot be liquidated.

| #                           | Protocol    | Issue                                                                       |
| --------------------------- | ----------- | --------------------------------------------------------------------------- |
| [0006](../database/0006.md) | Teller V2   | Overwrite existing collateral commitment to make collateral amount 0        |
| [0007](../database/0007.md) | Debt DAO    | Line of Credit array can be corrupted by a borrower to prevent liquidation  |
| [0008](../database/0008.md) | AbraNFT     | Borrowers can construct loans with malicious oracles that block liquidation |
| [0009](../database/0009.md) | Wild Credit | Liquidation can be avoided by depositing a UniV3 position with 0 liquidity  |
| [0010](../database/0010.md) | Taurus      | Liquidation gets reverted in an edge case when price drops 99%              |
| [0011](../database/0011.md) | Isomorph    | DOS attack avoids liquidation                                               |

---

## 3. Debt closed without repayment

This class of errors relate to scenarios where a borrower can manipulate the state to move loan to `repaid` status without an actual repayment to back it. This would cause a permanent loss to lenders who can no longer claim a receivable from borrowers. Borrower can use the `repaid` status to claim back his collateral.

| #                           | Protocol | Issue                                                                               |
| --------------------------- | -------- | ----------------------------------------------------------------------------------- |
| [0012](../database/0012.md) | Debt DAO | Non existent credit `id` can be used to close all loans against that credit line    |
| [0013](../database/0013.md) | Timeswap | Wrong placement of a condition allows borrower to withdraw all collateral           |
| [0014](../database/0014.md) | Taurus   | Missing inputs validation allows keeper to escalate permissions & payback all loans |
| [0015](../database/0015.md) | Astaria  | Anyone can delete liens (lien created on new borrowing)                             |

---

### 4. Repayments paused while liquidations enabled

This class of errors relates to a state of lending protocol where repayments are paused while liquidations are active. This is unfair to genuine borrowers who might want to repay/add more collateral to improve their health factor. It is ok if liquidations are also paused during this time but denying access to borrowers to repay while allowing liquidators to liquidate can cause losses to borrowers. Note that all liquidation penalties are borne by borrowers -> so any DOS that effects borrower repayments introduce vulnerabilities in protocol.

| #                           | Protocol  | Issue                                                                          |
| --------------------------- | --------- | ------------------------------------------------------------------------------ |
| [0016](../database/0016.md) | Blueberry | Liquidation is possible even when repayment is paused                          |
| [0017](../database/0017.md) | Lyra      | Users unable to close positions when price is stale or circuit breaker tripped |

---

### 5. Governance can disallow tokens that might impact existing repayments and liquidations

- In lending/borrowing protocols, adding and removing tokens listed for borrowing/collateral are usually decided by governance. Due to price manipulation risks, governance whitelists only specific tokens that can be considered for lending and colleral purposes.

- In some circumstances, when governance decides to de-whitelist a specific token, it might create issues for existing borrowings. Possibility of DOS for repayments and liquidations exists if such de-whitelisting is not done carefully. This class of errors happen due to inconsistent checks that might fail while executing crucial repayment/liquidation operations

| #                           | Protocol  | Issue                                                               |
| --------------------------- | --------- | ------------------------------------------------------------------- |
| [0018](../database/0018.md) | Blueberry | De-whitelisting of repay tokens causes a DOS for liquidation        |
| [0019](../database/0019.md) | Isomorph  | Pausing collateral prevents repay/liquidation                       |
| [0020](../database/0020.md) | Isomorph  | Liquidation allowed while repayments are disabled in some scenarios |

---

### 6. Pausing repayments/liquidations can cause instant liquidation when unpaused

- When a lending protocol has a pausing functionality that pauses both repayments and liquidations, there is an interesting vulnerability that can potentially creep in. During the paused period, price fluctuations can cause borrowing to be under-collateralized & when pausing ends, liquidation (which usually happens via bots) kicks in & a borrower is forcibly liquidated without having a chance to repay/add collateral

- This is unfair to borrowers. While borrowers are mostly individual wallets, liquidation is usually managed via automated bots. Only way to prevent this is to outrun the bots which is impractical for wallet owners. A fix for this issue is usually when unpausing provides a grace period to borrowers in which liquidation cannot be executed

- While a large grace period can create insolvency risks, a reasonable grace period (say 24 hrs) can provide an opportunity for genuine borrowers to take mitigation steps

| #                           | Protocol  | Issue                                                  |
| --------------------------- | --------- | ------------------------------------------------------ |
| [0021](../database/0021.md) | Blueberry | Re-ctivating repayments can cause instant liquidations |
