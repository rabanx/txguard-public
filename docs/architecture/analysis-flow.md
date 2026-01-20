# Analysis Flow

This document explains how TXGuard processes transaction-related inputs
and transforms them into structured security analysis results.

---

## Analysis Pipeline

![TXGuard Analysis Flow](/diagrams/diagram1.png)

---

## Input Handling

TXGuard accepts multiple optional inputs:

- Raw calldata
- Transaction hash
- Blockchain address
- Target contract (optional)
- Transaction value (optional)
- Sender address (optional)

The system automatically detects the input type and normalizes all inputs
into a unified internal representation.

---

## Decoding Phase

During decoding, TXGuard translates low-level blockchain data into
human-readable operations, including:

- Function calls and parameters
- Token approvals and transfers
- Contract interaction intent

Decoded data forms the foundation for all further analysis.

---

## Threat Intelligence Checks

TXGuard performs threat intelligence lookups against multiple sources to identify:

- Known malicious addresses
- Phishing and drainer contracts
- Previously reported high-risk interactions

Threat intelligence is treated as a signal rather than a hard verdict.

---

## Risk Analysis

The risk analysis phase correlates all available signals, including:

- Decoded transaction intent
- Threat intelligence findings
- Contextual metadata

The result is an explainable risk assessment designed to minimize
false positives and maximize clarity.

---

## Enhanced Analysis Output

The final output includes:

- Decoded function summary
- Warnings and security playbooks
- Contextual risk scenarios
- A multi-axis risk score

This structure allows both end users and developers to understand
*why* a transaction is considered risky.

---

## Design Goals

- Deterministic analysis
- Explainable results
- No reliance on opaque scoring alone
- Analysis-only, non-custodial design
