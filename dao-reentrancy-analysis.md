# DAO Reentrancy Vulnerability Analysis

## 1. Background
The DAO was an early decentralized autonomous organization built on Ethereum.
In 2016, a vulnerability in its withdrawal logic allowed repeated fund extraction,
leading to a large-scale loss of funds.

This document analyzes the vulnerability from a structural and logical perspective,
without reproducing the exploit.

---

## 2. Vulnerability Category
- Type: Reentrancy
- Core issue: External call before internal state update

---

## 3. High-Level Contract Logic (Simplified)
The vulnerable withdrawal logic followed this sequence:

1. User requests withdrawal
2. Contract sends Ether to user
3. Contract updates user's balance

This ordering introduced unintended control flow re-entry.

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
