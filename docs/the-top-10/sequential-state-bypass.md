---
title: "BLA4:2025 - Sequential State Bypass"
layout: col-sidebar
tab: false
order: 4
tags: business-logic-abuse
---

## Overview

This class of vulnerabilities occurs when systems fail to enforce proper state machine logic, allowing applications to jump
between states without following mandatory sequential progression.

Unlike validation-focused flaws, these weaknesses specifically target the integrity of state ordering and flow control,
permitting invalid state combinations that violate the intended business state model.


## Description

These vulnerabilities arise from inadequate enforcement of state machine constraints and sequential dependencies. The core
patterns include:

* **Direct state jumps:** Systems allow transitions that skip mandatory intermediate states (e.g., pending to shipped, bypassing
payment).

* **Invalid state combinations:** Applications permit logically impossible or contradictory states to coexist.

* **Sequence bypass:** Critical ordering requirements are ignored, allowing out-of-order state progression.

## Examples

### Scenario #1: Completing checkout without triggering a real payment transaction

An insufficient origin check of IPN callback in the WooCommerce plugins such as GloBee and CardGate lets attackers forge
“payment completed” notifications, updating an order’s status without ever triggering a real transaction.

By crafting fake IPN calls, fraudsters bypass payment checks,  leading to unpaid orders being fulfilled and undermining
trust in order integrity.

## Mapped CWEs
- CWE-372

## Sample CVEs
- CVE-2024-2382
- CVE-2023-3525
