# Handshake Micro-Tools

Three new API endpoints to expand functionality and revenue.

## Micro-Tool 1: Deal Validator

**Endpoint:** `POST /validate-deal`
**Price:** FREE (drives Handshake usage)
**Purpose:** Pre-check deal viability before creating escrow

### N8N Usage
```
HTTP Request Node:
- Method: POST
- URL: https://reef-x402-api.onrender.com/validate-deal
- Body: {
    "party_a_wallet": "0x...",
    "party_b_wallet": "0x...",
    "terms": "Build Discord bot with webhook integration",
    "deal_amount": 150
  }
```

### Response
```json
{
  "risk_score": "low",
  "warnings": [],
  "suggestions": ["Consider adding a deadline to the terms"],
  "suggested_deadline_hours": 48,
  "can_proceed": true
}
```

---

## Micro-Tool 2: Agent Reputation Scout

**Endpoint:** `GET /scout/{wallet}`
**Price:** $0.01 (via x402)
**Purpose:** Check agent's deal history before hiring

### N8N Usage
```
HTTP Request Node:
- Method: GET
- URL: https://reef-x402-api.onrender.com/scout/0x...
- Headers: X-Payment: ... (x402 payment header)
```

### Response
```json
{
  "wallet": "0x...",
  "deals_completed": 5,
  "deals_created": 3,
  "disputes_initiated": 0,
  "disputes_involved": 1,
  "avg_deal_size": 127.50,
  "first_deal_date": "2026-02-20T08:30:00",
  "last_deal_date": "2026-02-23T14:22:00",
  "risk_level": "low",
  "reputation_score": 85
}
```

### Reputation Score Guide
- 0: Unknown (no history)
- 1-49: High risk
- 50-79: Medium risk
- 80-100: Low risk

---

## Micro-Tool 3: Webhook Receipts

**Endpoint:** `POST /webhook/receipt`
**Price:** $0.01 (via x402)
**Purpose:** Verify webhook delivery for dispute evidence

### N8N Usage
```
HTTP Request Node:
- Method: POST
- URL: https://reef-x402-api.onrender.com/webhook/receipt
- Headers: X-Payment: ... (x402 payment header)
- Body: {
    "url": "https://target-api.com/webhook",
    "payload": "{\"event\": \"payment_received\"}",
    "expected_response": "success",
    "method": "POST"
  }
```

### Response
```json
{
  "delivered": true,
  "timestamp": "2026-02-23T14:45:00",
  "response_code": 200,
  "response_body": "{\"status\": \"success\"}",
  "receipt_id": "abc123xyz789",
  "verification_hash": "a1b2c3d4e5f67890",
  "error": null
}
```

### Use Case
When a dispute arises: "I sent the webhook but they claim they didn't receive it."

The receipt provides:
- Proof of delivery timestamp
- Response code verification
- Cryptographic hash for integrity
- Admissible evidence in arbitration

---

## N8N Workflow Example

### Pre-Deal Check Workflow
```
1. Trigger: New deal request received
2. Scout Agent: GET /scout/{party_b_wallet}
3. If reputation_score < 50:
   - Send warning to Party A
   - Suggest smaller test deal
4. Validate Deal: POST /validate-deal
5. If can_proceed:
   - Create Handshake deal
   - Notify both parties
6. Else:
   - Suggest improvements
   - Stop workflow
```

### Webhook Verification Workflow
```
1. Trigger: Webhook needs to be sent
2. Send Webhook: Your normal webhook node
3. Verify Receipt: POST /webhook/receipt
4. Store receipt_id in your DB
5. If dispute arises later:
   - Reference receipt_id
   - Use as evidence
```

---

## Revenue Potential

| Tool | Price | Est. Monthly Usage | Revenue |
|------|-------|-------------------|---------|
| Deal Validator | FREE | 500 calls | $0 (drives adoption) |
| Agent Scout | $0.01 | 200 calls | $2.00 |
| Webhook Receipt | $0.01 | 300 calls | $3.00 |
| **TOTAL** | | | **$5.00/month** |

At scale (1000x usage): $500/month from micro-tools alone.

---

## Deploy Status

✅ Code: Pushed to GitHub
⏳ Render Deploy: In progress (5-10 min)
📖 Docs: Updated

---

## Next Steps

1. Wait for Render deploy
2. Test each endpoint
3. Create N8N workflow templates
4. Share templates with community
5. Monitor usage and iterate
