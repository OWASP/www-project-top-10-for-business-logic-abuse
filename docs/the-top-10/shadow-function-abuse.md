---
title: "BLA10:2025 - Shadow Function Abuse (SFA)"
layout: col-sidebar
tab: false
order: 10
tags: business-logic-abuse
---

## Overview

Shadow functions are hidden or forgotten features such as internal API endpoints, administrative operations, or test
utilities that remain active in production without proper security controls.

Attackers locate these functions through code inspection or automated discovery tools. They then exploit them to bypass
safeguards or access data and functionality unavailable through public interfaces.

## Root causes

Hidden features stem from three distinct root causes:

- **Shadow functionality** is created by unauthorized teams or tools and remains unknown to security, escaping
governance and review.
 
- **Deprecated functionality** should have been removed but stay accessible in production long after their intended
retirement.

- **Obscured capabilities** are not exposed through standard interfaces yet can be discovered by attackers via code
inspection, automated scanning or reverse engineering.

All of these features operate outside normal access controls, creating unexpected attack vectors that can be exploited
to bypass security measures and access unauthorized data.


## Examples

### Scenario #1: Hidden admin parameter allows accessing admin panel

Authentication bypass vulnerability in Amazing Little Poll affecting versions 1.3 and 1.4. This vulnerability could
allow an unauthenticated user to access the admin panel without providing any credentials by simply accessing the
`lp_admin.php?adminstep=` parameter.


### Scenario #2: WebSocket auth Bypass through a hidden endpoint

FortiOS’s WebSocket management interface included an undocumented alternate path that bypassed token checks. Attackers
connect through this hidden channel to issue privileged commands.

A malicious client establishes a WebSocket handshake at `/ws/` rather than `/api/ws/`, triggering an authentication
bypass and granting full CLI command access.

## Mapped CWEs
- CWE-288
- CWE‑912
- CWE‑1242
- CWE‑425

## Sample CVE
- CVE-2025-4427
- CVE-2024-55591
