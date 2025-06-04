---
title: "BLA8:2025 - Replays of Idempotency Operations"
layout: col-sidebar
tab: false
order: 8
tags: business-logic-abuse
---

## Overview

This category covers business processes that must execute only once but can be replayed when APIs lack proper safeguards.
By omitting unique identifiers, history checks, or token invalidation, simple retries or replays trigger duplicate refunds,
repeated system resets, or multiple password changes. The result is financial errors, operational disruption, and weakened
security controls.

## Description

These weaknesses undermine business processes by failing to enforce one-time workflow constraints:
* **Unrestricted repeatable actions:** Critical operations such as account creation, refunds, or invoice creation proceed on every identical request because no unique key or usage record is required.
* **Replayable one-time tokens:** Workflows relying on transient flags or consumed tokens, such as consumption loyalty credits, password resets, and similar, do not record usage or invalidate tokens, so the same request can re-trigger password resets, email confirmations, and similar actions.

## Examples of attacks

### Scenario #1: Token replay attack

The ORY Hydra OAuth2 server (pre-1.4.0) failed to blacklist JWT IDs (JTIs), allowing replay of a client’s JWT assertion during client-authentication:

**Legitimate call:**
``` shell
POST /oauth2/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'client_id=c001d00d-5ecc-beef-ca4e-b00b1e54a111' \
--data-urlencode 'scope=application openid' \
--data-urlencode 'client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer' \
--data-urlencode 'client_assertion=eyJhb [...] jTw'

Response: {"access_token":"zeG0NoqOtlACl8q5J6A-TIsNegQRRUzqLZaYrQtoBZQ.VR6iUcJQYp3u_j7pwvL7YtPqGhtyQe5OhnBE2KCp5pM","expires_in":3599,"scope":"application openid","token_type":"bearer"}
```

**Attack call with the same assertion that should be only used once enables JTI replay attack:**
```shell
POST /oauth2/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'client_id=c001d00d-5ecc-beef-ca4e-b00b1e54a111' \
--data-urlencode 'scope=application openid' \
--data-urlencode 'client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer' \
--data-urlencode 'client_assertion=eyJhb [...] jTw'
Response: {"access_token":"wOYtgCLxLXlELORrwZlmeiqqMQ4kRzV-STU2_Sollas.mwlQGCZWXN7G2IoegUe1P0Vw5iGoKrkOzOaplhMSjm4","expires_in":3599,"scope":"application openid","token_type":"bearer"}
```


### Scenario #2: Partial Refund in WooCommerce

A partial-refund endpoint in WooCommerce lacked replay safeguards, so network glitches or malicious replays triggered multiple refunds.

**Legit return:**

```
POST /wp-json/wc/v3/orders/12345/refunds
Body: { "amount": 10.00, "lineItems": [..]}
```

**Replayed due to client retry or attacker:**
```
POST /wp-json/wc/v3/orders/12345/refunds
Body: { "amount": 10.00, "lineItems": [..] }
```

## Mapped CWEs
- CWE-837
- CWE-799

## Sample CVEs
- CVE-2025-1968
- CVE-2025-3479