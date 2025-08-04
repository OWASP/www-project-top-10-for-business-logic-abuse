---
title: "BLA6:2025 - Missing Transition Validation (MTV)"
layout: col-sidebar
tab: false
order: 6
tags: business-logic-abuse
---

## Overview

Transition Validation Flaws occur when an API defers or omits essential checks during multi-step state changes.
Attackers bypass mandatory validations, for example second-factor checks or approval flags, by calling a later endpoint
directly or by racing the validation step. As a result, security controls that rely on sequential validations are
bypassed, allowing unauthorized workflows.


## Root causes

APIs that break a process into multiple calls must re-validate prerequisite conditions in each step or lock them into a
single atomic operation. If the final action trusts that prerequisites were met earlier, without re-checking on
invocation, attackers can call the action endpoint before validations complete or skip them entirely. This gap between
state transitions and validation enforcement leads to logic bypass.

Alternatively, an attacker can manipulate transition indicators or tokens sent as input by overwriting or forging them
to simulate successful earlier steps. By crafting request parameters (for example, step flags, sequence numbers or
hidden form fields) to appear as though all validations passed, the final endpoint runs without any checks. This
input-based manipulation exploits the missing transition validation to bypass the entire workflow and perform
unauthorized actions.

## Examples

### Scenario #1: Next.js Middleware Bypass

A critical vulnerability in Next.js allowed attacker to skip all middleware processing by exploiting the
`x-middleware-subrequest` header. Originally intended to mark internal framework calls and prevent infinite recursion,
this header can be manipulated by external requests due to a design oversight.

By sending a request with a specially crafted `x-middleware-subrequest` value, an attacker causes the application to skip
all middleware processing, including access restrictions, session validation, and any other controls implemented there.

### Scenario #2: Users can check out unpublished products in microweber

microweber ≤2024.04.1 lets attackers purchase items that an admin has unpublished or deleted by skipping the
publication-check step in the checkout flow. As a result, attackers can acquire unavailable products, disrupting
inventory controls and business workflows:

1. Admin unpublishes a product:

```shell
POST /api/shop/items/publish
{
    "itemId":55,
    "action":"unpublish"
} 
```

2. However, an attacker can still add check out such item using APIs:

```shell
POST /api/shop/checkout
{
  "itemId": 55
} 
```

## Mapped CWEs
- CWE-288
- CWE-841
- CWE-691

## Sample CVEs
- CVE-2025-4427/4428
- CVE-2025-29927
