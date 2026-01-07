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
This vulnerability highlights several defensive principles
that can be applied beyond this specific case.

First, internal state must always be updated before making
any external calls. This prevents attackers from exploiting
inconsistent or outdated contract state.

Second, every external interaction should be treated as
potentially malicious, regardless of whether the recipient
appears trusted.

Finally, developers should adopt defensive design patterns
such as Checks-Effects-Interactions to explicitly separate
validation, state mutation, and external communication.

---

## 7. Limitations
This analysis focuses on the high-level logical flow of the DAO
withdrawal mechanism rather than low-level EVM or bytecode behavior.

It does not examine gas-related constraints, compiler-specific
optimizations, or subtle edge cases that may arise in real-world
deployments.

Additionally, this document does not cover modern mitigation
techniques such as reentrancy guards or language-level protections,
as the goal is to analyze the original vulnerability in its
historical and conceptual context.

---

## 8. Closing Notes

This document represents a minimal, structured vulnerability analysis
completed as part of Experiment #2026-01.

Rather than aiming for completeness, the focus of this experiment
was to practice consistent execution, clear reasoning, and
scope-controlled output.

By limiting the analysis to logical flow, developer assumptions,
and defensive principles, this format demonstrates how meaningful
security insight can be produced without deep tooling or exhaustive
technical detail.

This approach is intentionally repeatable.
The same structure can be applied to other vulnerability patterns
to build reliable security intuition and long-term analytical discipline.

