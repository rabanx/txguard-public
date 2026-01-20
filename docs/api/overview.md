# TXGuard API Reference

REST API for analyzing Web3 transactions, addresses, and signatures before signing.

**Base URL**
```
https://txguard.io
```

> For self-hosted instances, replace with your domain.

---

## Authentication

All API requests require authentication via API key.

### Getting an API Key

1. Log in to your TXGuard dashboard
2. Navigate to **Settings → API Keys**
3. Click **Create API Key**
4. Copy and securely store your key (starts with `txg_`)

### Using the API Key

Include the key in request headers:

```bash
# Option 1: Authorization header (recommended)
Authorization: Bearer txg_your_api_key_here

# Option 2: X-API-Key header
X-API-Key: txg_your_api_key_here
```

---

## Rate Limits

| Plan | Requests/min | Daily Limit |
|------|--------------|-------------|
| Free | 10 | 100 |
| Pro | 100 | 10,000 |
| Enterprise | 1000 | 100,000 |

Rate limit headers are included in responses:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1704067200
```

---

## Endpoints

### POST /api/analyze

Main analysis endpoint. Automatically detects input type and performs appropriate analysis.

#### Request

```bash
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "0x095ea7b3...",
    "inputType": "auto",
    "chainId": 1
  }'
```

#### Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `input` | string | ✅ | Transaction hash, address, calldata, or signature |
| `inputType` | string | ❌ | `auto`, `txHash`, `address`, `calldata`, `signature` (default: `auto`) |
| `chainId` | number | ❌ | Chain ID (default: `1` for Ethereum) |

#### Supported Chains

| Chain | ID |
|-------|-----|
| Ethereum | 1 |
| Polygon | 137 |
| Arbitrum | 42161 |
| Optimism | 10 |
| Base | 8453 |
| BSC | 56 |
| Avalanche | 43114 |

#### Response

```json
{
  "id": "clx1abc123...",
  "input": "0x095ea7b3...",
  "inputType": "calldata",
  "chainId": 1,
  "riskScore": 75,
  "riskLevel": "high",
  "summary": "Unlimited token approval to unknown address",
  "warnings": [
    {
      "id": "risk-0",
      "title": "Unlimited Approval",
      "message": "This transaction grants unlimited token spending permission",
      "severity": "CRITICAL",
      "category": "APPROVAL"
    }
  ],
  "decoded": {
    "type": "approval",
    "functionName": "approve",
    "protocol": null,
    "params": [
      { "name": "spender", "type": "address", "value": "0x..." },
      { "name": "amount", "type": "uint256", "value": "115792089237316195423570985008687907853269984665640564039457584007913129639935" }
    ]
  },
  "threatIntel": null,
  "recommendations": [
    "Review the spender address before approving",
    "Consider setting a specific amount instead of unlimited"
  ],
  "processingTime": 245,
  "timestamp": "2024-01-15T12:00:00.000Z"
}
```

---

## Input Types & Examples

### 1. Address Analysis

Check if an address is associated with known threats.

```bash
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "0x3c7f8c6d2b4e5a1f9d8c7b6e5a4d3c2b1a0f9e8d",
    "inputType": "address"
  }'
```

**Response (Threat Detected):**

```json
{
  "riskScore": 95,
  "riskLevel": "critical",
  "summary": " DANGER: This address has been flagged as drainer, phishing.",
  "threatIntel": {
    "isThreat": true,
    "riskScore": 95,
    "riskLevel": "CRITICAL",
    "labels": ["drainer", "phishing"],
    "sources": ["ScamSniffer", "ChainAbuse"],
    "reportCount": 47
  },
  "recommendations": [
    "Do NOT send any funds to this address",
    "Do NOT approve any tokens to this address"
  ]
}
```

---

### 2. Calldata Analysis

Decode and analyze raw transaction calldata.

```bash
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "0xa9059cbb000000000000000000000000d8da6bf26964af9d7eed9e03e53415d37aa960450000000000000000000000000000000000000000000000000de0b6b3a7640000",
    "inputType": "calldata",
    "chainId": 1
  }'
```

**Response (ERC20 Transfer):**

```json
{
  "riskScore": 10,
  "riskLevel": "low",
  "summary": "ERC20: transfer",
  "decoded": {
    "type": "transfer",
    "functionName": "transfer",
    "params": [
      { "name": "to", "type": "address", "value": "0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045" },
      { "name": "amount", "type": "uint256", "value": "1000000000000000000" }
    ]
  },
  "recommendations": [
    "Transaction appears safe based on analysis"
  ]
}
```

---

### 3. Transaction Hash Analysis

Analyze an existing transaction by its hash.

```bash
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "0x5c504ed432cb51138bcf09aa5e8a410dd4a1e204ef84bfed1be16dfba1b22060",
    "inputType": "txHash",
    "chainId": 1
  }'
```

---

### 4. Signature Analysis (EIP-712 / Permit)

Analyze signature requests before signing.

```bash
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "{\"types\":{\"Permit\":[{\"name\":\"owner\",\"type\":\"address\"}...]},\"domain\":{\"name\":\"USDC\"},\"message\":{\"spender\":\"0x...\",\"value\":\"115792089237316195423570985008687907853269984665640564039457584007913129639935\"}}",
    "inputType": "signature"
  }'
```

**Response (Dangerous Permit):**

```json
{
  "riskScore": 90,
  "riskLevel": "critical",
  "summary": "⚠️ Permit signature with unlimited approval",
  "decoded": {
    "type": "permit",
    "functionName": "PERMIT Signature",
    "params": [
      { "name": "spender", "type": "address", "value": "0x..." },
      { "name": "value", "type": "uint256", "value": "unlimited" },
      { "name": "deadline", "type": "uint256", "value": "1735689600" }
    ]
  },
  "warnings": [
    {
      "severity": "CRITICAL",
      "title": "⚠️ Unlimited Token Approval",
      "message": "Signature contains: unlimited approval"
    }
  ]
}
```

---

## Risk Levels

| Level | Score | Description |
|-------|-------|-------------|
| `safe` | 0-10 | No risks detected |
| `low` | 11-30 | Minor concerns, generally safe |
| `medium` | 31-60 | Review recommended |
| `high` | 61-80 | Significant risks detected |
| `critical` | 81-100 | Do not proceed |

---

## Warning Categories

| Category | Description |
|----------|-------------|
| `APPROVAL` | Token approval risks (unlimited, suspicious spender) |
| `TRANSFER` | Transfer-related risks (large amounts, unknown recipient) |
| `CONTRACT` | Contract interaction risks (unverified, proxy) |
| `SIGNATURE` | Signature request risks (permit, setApprovalForAll) |
| `ADDRESS` | Address reputation issues |
| `THREAT_INTEL` | Known threat detected |
| `PATTERN` | Suspicious transaction patterns |

---

## Error Responses

```json
{
  "error": "Unauthorized",
  "message": "Valid session or API key required"
}
```

| Status | Error | Description |
|--------|-------|-------------|
| 400 | Invalid input | Malformed request or validation error |
| 401 | Unauthorized | Missing or invalid API key |
| 429 | Rate limited | Too many requests |
| 500 | Internal error | Server error |

---

## SDK Examples

### JavaScript/TypeScript

```typescript
const TXGUARD_API_KEY = 'txg_your_api_key';

async function analyzeTransaction(calldata: string, chainId = 1) {
  const response = await fetch('https://txguard.io/api/analyze', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${TXGUARD_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      input: calldata,
      inputType: 'calldata',
      chainId,
    }),
  });

  if (!response.ok) {
    throw new Error(`API error: ${response.status}`);
  }

  return response.json();
}

// Usage
const result = await analyzeTransaction('0xa9059cbb...', 1);

if (result.riskLevel === 'critical') {
  console.warn(' DANGER:', result.summary);
} else {
  console.log('✅ Risk level:', result.riskLevel);
}
```

### Python

```python
import requests

TXGUARD_API_KEY = 'txg_your_api_key'
API_URL = 'https://txguard.io/api/analyze'

def analyze_transaction(input_data: str, input_type: str = 'auto', chain_id: int = 1):
    response = requests.post(
        API_URL,
        headers={
            'Authorization': f'Bearer {TXGUARD_API_KEY}',
            'Content-Type': 'application/json',
        },
        json={
            'input': input_data,
            'inputType': input_type,
            'chainId': chain_id,
        }
    )
    response.raise_for_status()
    return response.json()

# Usage
result = analyze_transaction('0x095ea7b3...', 'calldata', 1)
print(f"Risk: {result['riskLevel']} ({result['riskScore']}/100)")
```

### cURL

```bash
# Analyze an address
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{"input": "0xdead...", "inputType": "address"}'

# Analyze calldata
curl -X POST https://txguard.io/api/analyze \
  -H "Authorization: Bearer txg_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{"input": "0xa9059cbb...", "inputType": "calldata", "chainId": 1}'
```

## Webhooks
Configure webhooks to receive real-time notifications when monitored addresses are involved in transactions.

> Available on **Pro** and **Enterprise** plans.

### Setup

1. Go to **Dashboard → Webhooks**
2. Click **Create Webhook**
3. Enter your endpoint URL
4. Select events to subscribe to
5. Copy the signing secret for verification

### Events

| Event | Description |
|-------|-------------|
| `transaction.detected` | New transaction involving a monitored address |
| `risk.high` | High-risk transaction detected for monitored address |
| `approval.granted` | Token approval granted by monitored address |

### Payload Example

```json
{
  "event": "risk.high",
  "timestamp": "2024-01-15T12:00:00.000Z",
  "data": {
    "address": "0x...",
    "txHash": "0x...",
    "riskScore": 85,
    "riskLevel": "critical",
    "summary": "Interaction with known drainer contract"
  }
}
```

### Signature Verification

All webhook requests include an `X-TXGuard-Signature` header. Verify using HMAC-SHA256:

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expected = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expected)
  );
}
```

*TXGuard performs transaction analysis only and does not execute, broadcast, or sign transactions.*
