# Issues

Key issues in DEX based on AMM:

| #   | Issue                                                     |
| --- | --------------------------------------------------------- |
| 1   | No slippage parameter while trading one token for another |

---

## No slippage parameter while trading one token for another

Slippage defines `minimumOutput` while you are swapping one token for another. If the trade where you exchange tokenA for tokenB does not yield a minimum amount of tokenB, then you basically reject the transaction. Slippage is needed to avoid entering trades where realized price is much worse than what you expected. Bots on blockchain will execute sandwich attacks

| #                           | Protocol  | Issue                                              |
| --------------------------- | --------- | -------------------------------------------------- |
| [0028](../database/0028.md) | BadgerDAO | Missing slippage/min-return check in veCVXStrategy |
