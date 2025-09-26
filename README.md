# BitStable: Multi-Collateral Stablecoin Protocol

## Overview

**BitStable** is a decentralized stablecoin protocol built on the **Stacks blockchain**.
It enables users to mint **USDx**, a SIP-010 compliant fungible stablecoin, by depositing collateral in **STX** and **xBTC**.

The protocol ensures solvency and system stability through **risk parameters**, **minimum collateralization ratios**, and an **automated liquidation engine**.

BitStable extends Bitcoin’s utility within the Stacks ecosystem, offering a Bitcoin-secured, censorship-resistant, and trust-minimized stablecoin.

---

## System Features

* **Multi-Collateral Vaults**
  Users can open vaults and deposit **STX** and/or **xBTC** as collateral.

* **USDx Stablecoin (SIP-010)**
  Mint and burn USDx against overcollateralized positions.

* **Collateral Management**
  Deposit, withdraw, and manage collateral balances across multiple vaults.

* **Risk Parameters**

  * **Minimum Collateral Ratio (200%)** for safe vault creation.
  * **Liquidation Ratio (150%)** triggers forced liquidation for unsafe vaults.
  * **Liquidation Penalty (10%)** incentivizes liquidators to restore solvency.
  * **Stability Fee (2% annual)** ensures protocol sustainability.

* **Liquidation Engine**
  Authorized liquidators can liquidate undercollateralized vaults, repaying vault debt in USDx in exchange for discounted collateral.

* **Oracle Integration**
  Price feeds for STX and xBTC are provided by authorized oracle operators, with freshness checks to prevent stale prices.

* **Emergency Admin Controls**
  Controlled by contract owner for protocol safety (e.g., shutdown in case of oracle/system failure).

---

## Contract Architecture

### Core Components

* **Vaults**
  Each vault stores collateral balances (`stx-collateral`, `xbtc-collateral`), debt, owner, and activity status.

* **Stablecoin (USDx)**
  Implemented as a fungible token under SIP-010. Supports transfers, balance queries, minting, and burning.

* **Oracle Module**
  Maintains latest price feeds with `price`, `timestamp`, and `confidence`. Ensures only authorized operators can update.

* **Liquidation Module**

  * Calculates vault health factor.
  * Allows authorized liquidators to burn USDx and claim collateral at a discount if vault health falls below liquidation ratio.

* **Protocol Stats**
  Tracks system-wide totals: total vaults, debt, collateral, and supply.

---

## Data Flow (High-Level)

1. **Collateral Deposit**

   * User opens a vault and deposits STX and/or xBTC.
   * Vault collateral and system totals are updated.

2. **USDx Minting**

   * User mints USDx based on collateral ratio.
   * Minimum ratio check ensures new debt remains safe.

3. **Repayment / Burning**

   * User repays USDx to reduce debt.
   * Vault debt and total supply decrease.

4. **Collateral Withdrawal**

   * User withdraws collateral only if vault remains above minimum collateralization ratio.

5. **Liquidation**

   * Oracle updates asset prices.
   * If a vault falls below liquidation ratio, authorized liquidators can repay its debt and receive collateral at a penalty discount.

---

## Key Constants

* **Minimum Collateral Ratio:** `200%`
* **Liquidation Ratio:** `150%`
* **Liquidation Penalty:** `10%`
* **Stability Fee:** `2% annual`
* **Max Oracle Price Age:** `3600 seconds`

---

## Contract Functions (Highlights)

### Public

* `create-vault (stx-amount, xbtc-amount)` → Opens new vault.
* `add-collateral (vault-id, stx-amount, xbtc-amount)` → Deposits more collateral.
* `mint-usdx (vault-id, amount)` → Mints USDx against collateral.
* `burn-usdx (vault-id, amount)` → Repays vault debt.
* `withdraw-collateral (vault-id, stx-amount)` → Withdraws collateral if safe.
* `liquidate-vault (vault-id)` → Executes liquidation.

### Oracle

* `update-price (asset, price, confidence)` → Updates price feed.

### Read-Only

* `get-vault (vault-id)` → Returns vault details.
* `get-protocol-stats` → Returns global metrics.
* `calculate-health-factor (vault-id)` → Computes vault health.
* `is-vault-safe (vault-id)` → Returns liquidation safety status.

---

## Security Considerations

* **Oracle Reliability**: Relies on timely and accurate updates.
* **Liquidator Permissions**: Only authorized liquidators can trigger liquidation.
* **Collateral Safety**: Withdrawal and minting checks prevent undercollateralized vaults.
* **Emergency Controls**: Owner can shut down protocol if oracle fails.

---

## Future Extensions

* Multi-asset collateral expansion.
* Governance-driven parameter updates (DAO).
* Automated liquidator markets instead of fixed authorization.
* Interest-bearing collateral integration.

---

## License

MIT License. See [LICENSE](./LICENSE) for details.
