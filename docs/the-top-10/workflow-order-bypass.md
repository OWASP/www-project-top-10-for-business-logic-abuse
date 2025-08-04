---
title: "BLA2:2025 - Concurrent workflow order bypass (CWOB)"
layout: col-sidebar
tab: false
order: 2
tags: business-logic-abuse
---

## Overview
Workflow Order Bypass occurs when an attacker leverages a race condition to execute a final workflow step before its
required prior steps have fully applied. This can happen in two distinct ways: either across separate commands or events
in a distributed process that lacks a central orchestrator, or within the hidden internal stages of a single request
where multiple sub-states are processed without an atomic guard.


In both cases attackers skip mandatory checks such as multi-factor verification or email confirmation, resulting in
unauthorized access, privilege escalation, or completion of sensitive operations without ever satisfying the intended
business logic.


## Root causes

1. **Race conditions due to missing workflow orchestration.** When business workflows span multiple services, commands or
   events can be handled out of order if no central orchestrator or saga coordinator enforces sequence. An attacker issues
   the command for step N before step N–1 finalizes. The system treats each command independently and does not block or reorder
   them. Because no atomic guard binds the steps into one transaction or ordered saga, the final action can complete even
   though its prerequisite has not been applied.
2. **Race conditions in hidden sub-states transitions.** Even within a single HTTP request, server-side frameworks often
   execute several internal stages or sub-states in sequence. Between those stages there exists a millisecond-scale window.
   If the application does not bundle all sub-state transitions into an atomic operation or use in-process synchronization,
   an overlapping request can observe or act upon an intermediate sub-state. High-precision race techniques can inject that
   request during the brief window, causing the protected action to execute out of order.

## Examples

### Scenario #1: Airline Seat Upgrade Bypass

An airline API splits seat‑upgrade availability check and payment capture into two separate calls. If the payment call
races in before the availability flag is recorded, upgrades go through even when no seats remain.

Attackers can use the following sequence of steps to exploit this vulnerability:

**Step 1.** Check upgrade availability (sets upgrade_allowed=true).
```shell
POST /api/flights/AA100/upgrades/check HTTP/1.1
{ "passenger_id": "PAX123" }
```

**Step 2.** ~8 ms later, capture payment before availability flag persists.
```shell
POST /api/flights/AA100/upgrades/pay HTTP/1.1
{ "passenger_id": "PAX123", "card_token": "tok_xyz" }
```

Because payment trusts the transient upgrade_allowed flag (which is still in its initial “allowed” state) the upgrade
succeeds even if seats sold out in the meantime.

### Scenario #2: Admin‑Flag Initialization Bypass

A CMS’s login handler first initializes every new session with role=admin then immediately downgrades it based on user data before returning. If you race a second request into the admin‑only dashboard endpoint before the downgrade executes, you retain admin privileges.
To exploit this vulnerability, attackers can use the following sequence of HTTP calls:

**Step 1.** Initiate login (long user‑lookup).

```shell
POST /login HTTP/1.1
{ "user": "jdoe", "pass": "secret" }
```

**Step 2.** ~5 ms later, before downgrade runs, fetch admin page.

```shell
GET /admin/dashboard HTTP/1.1
Cookie: session=eyJhbGciOi…
```

The dashboard grants access because the user still appears as admin in that fleeting sub‑state.

## Mapped CWE
- CWE‑841
- CWE-367
- CWE-368

## Sample CVE
- CVE-2025-31161