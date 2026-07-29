---
title: "BLA1:2025 - Action Limit Overrun (ALO)"
layout: col-sidebar
tab: false
order: 1
tags: business-logic-abuse
---

## Overview

Overrun Limit of Idempotent Operations happens when an operation that is meant to execute a specific number of times can
actually be performed multiple times in quick succession. Well-known examples are redeeming a coupon, issuing a refund,
or granting a free trial.

These vulnerabilities exploit a gap between the transaction validation and the protected action (Time of Check, Time of 
Use - TOCTOU). Attackers send duplicate or parallel requests within the same validation window so that each request passes
he “unused” check before any state update is recorded. The result is unintended repeated execution of a single-use operation,
leading to financial loss, inventory depletion, or exhaustion of limited offers.


## Root causes

This flaw exists solely because multiple requests against the same resource collide while reading and writing its usage
state without synchronization. When two or more processes:

1. Read the “remaining uses” counter or “unused” flag at the same time (time-of-check).
2. Validate the resource as available based on the same stale state.
3. Then both proceed to apply the action (time-of-use) before either write commits, each sees the original pre-update
state and is allowed to succeed.

Without a lock, transactional guard, or any atomic increment/decrement, the counter may underflow or accept duplicates.
Logging or fingerprinting of processed request payloads is absent, so duplicate payloads aren’t recognized or rejected.


## Examples

### Scenario #1: Invite link replay in anything-llm due to a race condition

The mintplex-labs/anything-llm repository’s invite-acceptance API fails to lock invite tokens atomically. Attackers send
multiple concurrent requests for a single invite link, and each request succeeds in creating a new account (time-of-check
and time-of-use overlap).

This bypasses the intended security mechanism that restricts invite acceptance to a single user, leading to unauthorized
user creation without detection in the invite tab.


### Scenario #2: Race Condition in nopCommerce Gift Cards

A CMS’s login handler first initializes every new session with role=admin then immediately downgrades it based on user data before returning. If you race a second request into the admin‑only dashboard endpoint before the downgrade executes, you retain admin privileges.
To exploit this vulnerability, attackers can use the following sequence of HTTP calls:

**Step 1.** Initiate login (long user‑lookup).

nopCommerce before 4.80.0 lacks any locking when placing orders. As a result, two near-simultaneous calls to
`POST /checkout/OpcConfirmOrder/` both check the gift card balance before either updates it, allowing double redemption
of the same gift card.

An attacker can exploit this to make multiple purchases using a single gift card, effectively obtaining goods for free.


## Mapped CWE
- CWE-367

## Sample CVE
- CVE-2024-2913
