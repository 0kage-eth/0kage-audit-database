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
