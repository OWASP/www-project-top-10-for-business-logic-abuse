---
title: "BLA1:2025 - Lifecycle & Orphaned Transitions Flaws"
layout: col-sidebar
tab: false
order: 1
tags: business-logic-abuse
---

## Overview

Business workflows often create temporary artifacts, such as sessions, tokens, or sub-flows, to manage multi-step processes,
like account sign-up, configuration wizards, or batch jobs.

If these artifacts remain active after the parent process ends, attackers can reuse or replay them to invoke orphaned transitions,
bypass controls, and corrupt business logic, leading to unauthorized actions, data exposure, or process disruptions.


## Description

When a parent process completes, whether it’s an e-commerce checkout, a batch import job, or a multi-step configuration
wizard, systems must:

* Invalidate or expire all intermediate artifacts (temporary sessions, locks, one-time tokens, or queued tasks).
* Disable or remove any endpoints, UI components, or sub-routines specific to that workflow.
* Release or reset resources so that downstream operations cannot execute against stale context.
* Detect and clean up any partial or inconsistent state left behind (e.g. half-written records, orphaned database rows,
abandoned locks) to prevent data corruption or logic gaps.

Failures in any of these areas leave orphaned transitions callable, allow inconsistent artifacts to persist, and enable
attackers to replay steps, resurrect closed processes, or corrupt system state:

* **Orphaned control flows:** Sub-routines and API paths remain reachable after their parent context ends, letting attackers
trigger hidden operations without proper checks.

* **Use-after-expiration:** Tokens or sessions linger beyond their intended lifespan, so revoked accounts or cancelled tasks
can be inadvertently reactivated.

* **Inconsistent state residues:** Partial data writes, leftover locks, and abandoned jobs create logic gaps that attackers
exploit to corrupt workflows or leak sensitive information.


## Examples

### Scenario #1: Use of a resource after expiration allow gaining improper access

An operation on a resource after expiration or release in Fortinet FortiManager versions 6.4.12 through 7.4.0 allows an
attacker to gain improper access to FortiGate via valid credentials.


### Scenario #2: Information leakage through expired domain resources

ArcSight ESM improperly handled expired domain resources, allowing reference to revoked domains and potential information
leakage.

## Mapped CWE
- CWE-705
- CWE-672

## Mapped CVE
- CVE-2025-2517
- CVE-2024-47060
