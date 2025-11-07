# 🏦 SatoshiVault Protocol

### Unlock Bitcoin's dormant value through decentralized, collateralized lending

Borrow against your BTC without selling—maintain sovereignty, upside potential, and full control.

---

## 📘 Overview

**SatoshiVault** is a **decentralized lending protocol** built on the **Stacks blockchain**, enabling Bitcoin holders to access liquidity without relinquishing ownership.
By locking BTC as collateral and borrowing stablecoins in a trust-minimized, overcollateralized manner, users can participate in on-chain finance while preserving Bitcoin’s long-term upside.

The protocol is fully governed by smart contracts, enforcing collateralization ratios, liquidation thresholds, and interest mechanisms with transparent logic and no custodial intermediaries.

---

## ⚙️ System Overview

At a high level, SatoshiVault orchestrates three key components:

1. **Collateral Management**

   * Users deposit BTC (via Stacks’ Bitcoin bridge) as collateral.
   * The protocol tracks collateralization ratios based on oracle-supplied BTC/USD prices.

2. **Loan Issuance & Repayment**

   * Borrowers take loans in stablecoins, collateralized by their BTC deposits.
   * Loans accrue interest per block until fully repaid.
   * On repayment, collateral is unlocked automatically.

3. **Risk & Liquidation**

   * Smart contracts continuously verify collateral ratios.
   * If the ratio falls below the liquidation threshold, the position is liquidated to protect solvency.

---

## 🧱 Contract Architecture

| Component                                     | Description                                                                                  |
| --------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **`platform-initialized`**                    | Tracks whether the protocol is active. Prevents re-initialization.                           |
| **`minimum-collateral-ratio`**                | Minimum ratio (e.g., 150%) required to open a loan.                                          |
| **`liquidation-threshold`**                   | Collateral ratio (e.g., 120%) at which liquidation occurs.                                   |
| **`platform-fee-rate`**                       | Fee applied on loans and repayments (future integration-ready).                              |
| **`total-btc-locked` / `total-loans-issued`** | Global state tracking aggregate collateral and loans.                                        |
| **`loans` map**                               | Core registry storing per-loan metadata (borrower, collateral, loan amount, interest, etc.). |
| **`user-loans` map**                          | Index for quickly fetching a user's active loan IDs.                                         |
| **`collateral-prices` map**                   | Oracle-fed asset pricing used for collateral valuation.                                      |

---

## 🔄 Data Flow Summary

```text
[User Action] → [Smart Contract Validation] → [State Update] → [Loan Lifecycle Event]

1. Deposit BTC → increases total-btc-locked
2. Request Loan → validates collateral ratio → records loan in `loans` and `user-loans`
3. Repay Loan → computes accrued interest → releases collateral → updates user index
4. Price Feed Update → adjusts BTC price → triggers liquidation check if below threshold
```

---

## 🧩 Core Functions

### Initialization

* `initialize-platform`: Enables protocol after deployment. Restricted to contract owner.
* `update-collateral-ratio` / `update-liquidation-threshold`: Administrative tuning parameters.
* `update-price-feed`: Owner updates oracle-driven BTC price.

### Loan Lifecycle

* `deposit-collateral(amount)`: Registers BTC collateral in protocol accounting.
* `request-loan(collateral, loan-amount)`: Opens a new loan if collateral ratio ≥ required minimum.
* `repay-loan(loan-id, amount)`: Fully repays principal + interest; marks loan as repaid.

### Liquidation Logic

* `check-liquidation(loan-id)`: Internal function that checks if a loan’s collateral ratio falls below the threshold.
* `liquidate-position(loan-id)`: Forcefully closes an undercollateralized loan.

### Read-Only Accessors

* `get-loan-details(loan-id)`
* `get-user-loans(user)`
* `get-platform-stats()`
* `get-valid-assets()`

---

## 🧮 Key Protocol Parameters

| Parameter                    | Description                            | Default                 |
| ---------------------------- | -------------------------------------- | ----------------------- |
| **Minimum Collateral Ratio** | Required collateralization per loan    | `150%`                  |
| **Liquidation Threshold**    | Ratio triggering liquidation           | `120%`                  |
| **Interest Rate (per loan)** | Default interest rate per block period | `5%` (static prototype) |
| **Fee Rate**                 | Reserved for future fee mechanics      | `1%`                    |
| **Collateral Assets**        | Supported collateral types             | `["BTC", "STX"]`        |

---

## 🧠 Design Considerations

* **Decentralized Sovereignty:** BTC remains user-owned; smart contracts enforce rules without custodians.
* **Transparent Risk Control:** Collateral ratios and liquidation rules are verifiable on-chain.
* **Extendable Oracle Layer:** Supports modular oracles for BTC and STX price updates.
* **Composable Architecture:** Designed for integration with stablecoin minters, bridges, and DeFi primitives on Stacks.

---

## 🔐 Security & Integrity

* **Access Control:**
  Only the contract owner (deployer) can perform administrative updates.

* **Error Management:**
  Each failure scenario is mapped to a distinct `ERR-*` code, aiding deterministic debugging.

* **Invariant Enforcement:**

  * Loans cannot be created below minimum collateral ratio.
  * Invalid or stale price feeds are rejected.
  * Users cannot repay or liquidate loans they do not own.

---

## 🚀 Future Enhancements

* Integration with **SIP-010** token standards for collateralized stablecoin issuance.
* **Decentralized Oracle aggregation** for BTC/USD feeds.
* **Dynamic interest rates** and fee sharing mechanisms for protocol DAO governance.
* Support for **multi-asset collateral** beyond BTC and STX.

---

## 📄 License

This project is released under the **MIT License**.
See `LICENSE` file for details.

---

## 🧭 Summary

SatoshiVault demonstrates a **trustless, transparent, and composable lending primitive** built on Stacks, unlocking new capital efficiency for Bitcoin holders.
Its modular Clarity architecture makes it extensible for DeFi integrations, decentralized governance, and future Bitcoin-native yield systems.
