# DAO Reentrancy Vulnerability Analysis

## 1. Background
The DAO was an early decentralized investment fund built on Ethereum.
Users deposited ETH into a smart contract and were later able to withdraw their funds.

In 2016, a vulnerability in the withdrawal logic allowed an attacker
to repeatedly drain ETH from the contract, leading to one of the most
significant security incidents in Ethereum's history.

---

## 2. Vulnerability Category
- Vulnerability type: Reentrancy
- Core issue: External call executed before internal state update

---

## 3. High-Level Contract Logic (Simplified)

The vulnerable withdrawal process followed this logical sequence:

1. A user requests to withdraw funds
2. The contract sends ETH to the user
3. The contract updates the user's balance

The problem is that step 2 triggers external code execution.
Before step 3 is completed, the external contract can call back into
the withdrawal function and repeat the process.

---

## 4. Root Cause Analysis
The fundamental problem was not the external call itself,
but the assumption that control flow would return only once.

By calling an external address before updating internal state,
the contract allowed recursive calls that repeatedly passed balance checks.

---

## 5. Why This Was Easy to Miss
Several factors contributed to the vulnerability:
- Developers assumed sequential execution
- External calls were treated as "safe transfers"
- No mental model for adversarial re-entry existed at the time

---

## 6. Defensive Insights
Key defensive lessons include:
- Always update internal state before external calls
- Follow the Checks-Effects-Interactions pattern
- Treat every external call as untrusted

---

## 7. Limitations
This analysis focuses on logical structure rather than bytecode-level behavior.
It does not cover modern mitigations such as reentrancy guards.

---

## 8. Closing Notes
This document is part of Experiment #2026-01 and serves as a minimal,
AI-assisted vulnerability pattern analysis.
