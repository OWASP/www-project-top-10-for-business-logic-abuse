---
title: "BLA3:2025 - Object state manipulations (OSM)"
layout: col-sidebar
tab: false
order: 3
tags: business-logic-abuse
---

## Overview
Object State Manipulations occur when APIs bind user-supplied data directly into internal objects without filtering
allowed fields or validating types. Attackers exploit mass assignment or data-type smuggling to override protected
properties such as roles, flags, or balances.

The result is unauthorized privilege changes, injection of arbitrary state, or theft of business logic. Because these
flaws live in the object‐mapping layer rather than a specific endpoint, they often go unnoticed until abused in a
production API.


## Root causes
Object state manipulation exploits are achieved through two distinct approaches:

* **Mass assignment:**  Occurs when an API endpoint takes the entire client-supplied payload and writes every field
directly into a server-side object without filtering. Attackers include unexpected properties (for example
“role”: “admin”) in their JSON bodies, and the framework blindly applies them, overriding protected attributes.

* **Data type smuggling:** Happens when an API accepts values in formats that bypass type checks—such as sending "true"
(string) instead of true (boolean), or "0" (string) instead of 0 (number). By exploiting loose parsing rules, attackers
slip malicious values past simplistic validation and alter object state.


Together, these flaws let attackers inject or override internal properties, leading to unauthorized privilege changes,
account takeover, and other severe business-logic violations.


## Examples

### Scenario 1: Wallet top-up through data type smuggling
A wallet‑top‑up API parses "amount" via a loose float parser. An attacker sends `"amount":"1e4"` (string), granting
10,000 units instead of 1:
1. Normal top‑up:
```shell
POST /api/wallet/topup
{ "userId": 55, "amount": 1 }
```

2. Attack request:
```shell
POST /api/wallet/topup
{ "userId": 55, "amount": "1e4" }
```

### Scenario 2: Privilege escalation through Mass assignment in Langflow
Langflow releases before v1.0.13 contain a Mass Assignment flaw that lets a remote, authenticated user with minimal
privileges elevate themselves to super admin by sending a specially crafted HTTP request to the users API endpoint.

To exploit, this vulnerability an attacker executes the following:

```shell
PATCH /api/v1/users/1234 HTTP/1.1
Authorization: Bearer [TOKEN]
{
    "is_superuser":true
}
```

## Mapped CWEs
- CWE-1287: Improper Validation of Specified Type of Input
- CWE-704: Incorrect Type Conversion or Cast
- CWE-843: Access of Resource Using Incompatible Type
- CWE-681: Incorrect Conversion between Numeric Types
- CWE-192: Integer Coercion Error


## Sample CVEs
- CVE-2024-13275
- CVE-2022-25845
- CVE-2017-9805
