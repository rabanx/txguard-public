# TXGuard — Limitations & Scope

TXGuard is a **transaction analysis and risk assessment platform**.  
It is designed to help users and developers better understand what they are about to sign — **before** interacting with blockchain transactions.

This document outlines **what TXGuard does and does not do**, to ensure correct expectations and responsible usage.

---

## What TXGuard Does

TXGuard provides **pre-signature analysis** by combining:

- Transaction decoding (EVM & Solana)
- Threat intelligence from multiple independent sources
- Heuristic and pattern-based risk detection
- Multi-factor risk scoring
- Human-readable explanations and recommendations

TXGuard is intended to **assist decision-making**, not replace it.

---

## What TXGuard Does NOT Do

TXGuard explicitly **does not**:

- ❌ Execute transactions
- ❌ Broadcast transactions to any blockchain
- ❌ Sign transactions or messages
- ❌ Custody user funds or private keys
- ❌ Act as a wallet or wallet provider
- ❌ Provide financial, investment, or legal advice
- ❌ Guarantee safety or profitability of any transaction

All final decisions remain the responsibility of the user.

---

## No Absolute Guarantees

Blockchain security is adversarial by nature.

While TXGuard aims to detect **known risks, suspicious patterns, and common attack vectors**, it **cannot guarantee** detection of:

- Zero-day exploits
- Previously unseen attack techniques
- Newly deployed malicious contracts with no reputation data
- Social engineering attacks outside transaction context
- Off-chain deception (fake UIs, phishing websites, impersonation)

TXGuard reduces risk — it does not eliminate it.

---

## Threat Intelligence Limitations

Threat intelligence data is aggregated from multiple sources, which may include:

- Public reports
- Community-driven databases
- Internal analysis pipelines

Limitations include:

- Delays between threat discovery and reporting
- False positives or false negatives
- Incomplete coverage across all chains and ecosystems

Threat intelligence should be treated as **one signal among many**, not a single source of truth.

---

## Decoding & Interpretation Limitations

Transaction decoding is based on:

- Known ABIs
- Heuristic detection
- Common protocol patterns

As a result:

- Obfuscated or custom calldata may not be fully decoded
- Proxy contracts may hide final execution logic
- Dynamic or upgradeable contracts may behave differently over time
- Off-chain effects (e.g. oracle manipulation) may not be visible at signing time

TXGuard presents the **best interpretation available at the time of analysis**.

---

## Risk Scores Are Contextual

Risk scores and levels (`SAFE`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`) are **context-dependent** and should be interpreted accordingly:

- A `HIGH` risk does not always imply malicious intent
- A `LOW` risk does not guarantee harmless execution
- The same action may carry different risk depending on user context, value, and intent

Users should always consider:
- Transaction value
- Destination contract or address
- Their own trust assumptions

---

## API & Automation Considerations

When using the TXGuard API:

- Results are provided **as-is**
- Automated decisions based solely on risk scores are discouraged
- Final authorization should always include human review for high-risk actions

TXGuard is designed to **augment security workflows**, not replace them.

---

## Regulatory & Compliance Scope

TXGuard is a **technical security analysis tool**.

It is **not**:
- A financial service provider
- A regulated advisory service
- A compliance or KYC solution

Users remain responsible for compliance with applicable laws and regulations in their jurisdiction.

---

## Responsible Usage

TXGuard should be used as part of a **defense-in-depth strategy**, which may include:

- Hardware wallets
- Transaction simulation
- Manual contract review
- Reputable wallet software
- Security education and awareness

---

## Final Note

TXGuard exists to close the gap between **what users are asked to sign** and **what they can realistically understand**.

It empowers users with visibility —  
but **understanding and responsibility remain human decisions**.

---

*TXGuard performs transaction analysis only and does not execute, broadcast, or sign transactions.*
