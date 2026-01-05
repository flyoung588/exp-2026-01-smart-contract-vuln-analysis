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
The root cause of this vulnerability was a flawed assumption
about control flow during external interactions.

The contract assumed that once ETH was sent to a user,
execution would resume normally and only once.
This assumption ignored the possibility that the recipient
could execute arbitrary logic before the internal balance
was updated.

As a result, the contract repeatedly relied on outdated state
while approving multiple withdrawals.

---

## 5. Why This Was Easy to Miss
This vulnerability was easy to overlook due to several factors.

Developers intuitively expected withdrawal logic to execute
in a simple, linear sequence.
External transfers were often treated as safe, final actions,
rather than points where control could be lost.

At the time, there was limited awareness that a single function
call could be re-entered before its previous execution completed.

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
