# Race Condition and Concurrency Issues


## Overview

Failures with concurrent state changes synchronization in multi-step business processes stem from actions taken on outdated or unverified state. These problems originate from missing synchronization, time-of-check/time-of-use race conditions, and enforcement gaps in concurrent or event-driven workflow systems.


## Description

These vulnerabilities compromise business logic by decoupling critical checks from corresponding actions:



* **Check-and-Act Race Conditions:** The system reads a resource’s state and later acts on it without atomic validation, allowing the state to change between the check and the use. This covers both TOCTOU flaws and loose optimistic-concurrency checks that accept stale version tokens.
* **Unsynchronized Shared-Resource Access:** Multiple threads or processes modify the same data without locks or mutexes, violating exclusivity and causing double actions, data corruption, or inconsistent updates
* **Event Synchronization Failures:** In event-driven workflows, services publish events before transactions commit or handle messages out of order, so consumers act on uncommitted or outdated context.


## Examples of attacks


### Scenario #1: Race Condition in Drupal password reset flow

Drupal core has a race-condition vulnerability in its UserController that manages password reset flow and allows reuse
of an already consumed token by sending two reset requests in quick succession using last-byte synchronization technique:

1. **First legit call:**
```
GET /user/reset/1/1616792769/jHjyg8sV9VZaPSwFOBrzdkrhVkPQluUQH-5SlUYIvGI/login
```

2. **Subsequent call that creates a race condition:**
```
GET /user/reset/1/1616792769/jHjyg8sV9VZaPSwFOBrzdkrhVkPQluUQH-5SlUYIvGI/login
```

Attackers can hijack accounts even after the legitimate user resets their password once, compromising account security.

## Mapped CWEs
- CWE-362
- CWE-367
- CWE-662

## Sample CVEs
- CVE-2023-24042
- CVE-2022-4037
- CVE-2021-36532
- CVE-2020-1667