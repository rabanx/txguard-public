# Architecture Overview

This document provides a high-level overview of the TXGuard system architecture and
explains how transactions are analyzed before signing.

The architecture is intentionally designed to be **analysis-only** and **non-custodial**.
TXGuard does not execute, broadcast, or sign transactions.

---

## High-Level Flow

![TXGuard End-to-End Flow](/diagrams/diagram.png)

TXGuard operates as a security analysis layer that sits **between the user and the signing decision**.

The system processes transaction-related inputs, analyzes them using multiple independent signals,
and returns a clear risk-oriented decision to the user.

---

## Core Design Principles

TXGuard is built around the following principles:

- **Pre-signing analysis** — all checks happen before a transaction is signed
- **Read-only interaction** — no private keys, no transaction execution
- **Multi-source verification** — risk decisions are never based on a single signal
- **Explainability** — results are designed to be understandable by non-experts
- **Security-first** — conservative defaults and explicit warnings

---

## Input Layer

TXGuard accepts multiple types of transaction-related input:

- Raw calldata
- Transaction hash
- Blockchain address

These inputs can be provided individually or combined, depending on the integration
(wallet, dApp backend, monitoring service).

The system automatically detects the input type and normalizes it for further analysis.

---

## Analysis Layer

The analysis layer consists of three primary components that operate independently
and feed into the final risk decision.

### Transaction Decoder

The transaction decoder translates low-level blockchain data into
human-readable operations, such as:

- Function names and parameters
- Token approvals and transfers
- Contract interactions

TXGuard currently supports:
- EVM-compatible chains
- Solana transactions

Decoded data is used both for risk analysis and for user-facing explanations.

---

### Threat Intelligence

TXGuard performs threat intelligence checks against multiple independent sources,
including:

- Known malicious contracts
- Phishing and drainer addresses
- High-risk interaction patterns

Threat intelligence is treated as a **signal**, not a verdict.
It contributes to the overall risk assessment rather than acting as a binary allow/deny mechanism.

---

### Risk Analysis Engine

The risk analysis engine correlates all available signals, including:

- Decoded transaction intent
- Threat intelligence findings
- Contextual metadata (e.g. interaction type, value exposure)

The engine produces a normalized risk assessment that is designed to be:
- Deterministic
- Explainable
- Resistant to single-source false positives

---

## Analysis Result

The output of the analysis is a structured result that includes:

- A high-level risk classification
- Supporting signals and findings
- Contextual explanations suitable for end users or developers

TXGuard intentionally avoids opaque scoring systems in favor of
clear, decision-oriented outcomes.

---

## User-Facing Decision States

TXGuard presents one of three clear outcomes:

- **SAFE** — Proceed with confidence
- **WARNING** — Review carefully
- **DANGER** — Do not sign

These states are designed to support fast, informed decisions without requiring
deep technical knowledge of blockchain internals.

---

## Scope and Limitations

TXGuard provides **security analysis only**.

It does **not**:
- Execute or broadcast transactions
- Sign transactions
- Custody assets or private keys
- Act as a wallet or financial service provider

TXGuard is intended to complement existing wallets, dApps, and security workflows,
not replace them.

---

## Extensibility

The architecture is designed to support:

- Additional blockchains
- New threat intelligence sources
- Expanded risk scenarios and detection patterns
- Advanced analysis layers for enterprise use cases

New capabilities can be introduced without changing the core interaction model.

---

## Summary

TXGuard’s architecture prioritizes clarity, safety, and user empowerment.

By analyzing transactions **before signing** and presenting results in a
clear and explainable way, TXGuard reduces the risk of malicious interactions
while preserving full user control.
