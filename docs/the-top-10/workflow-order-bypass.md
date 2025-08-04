---
title: "BLA2:2025 - Logic Bomb, Loops and Halting Issues"
layout: col-sidebar
tab: false
order: 2
tags: business-logic-abuse
---

## Overview
APIs that automate business processes can contain hidden triggers, endless loops, unchecked-input loops,
and unbounded recursion when code omits gating, termination checks, input validation or depth limits.

Attackers exploit these lapses in trigger gating, loop exit conditions, parameter validation and recursion controls to
repeatedly fire hidden routines, exhaust CPU and memory or crash services, resulting in financial loss or denial of service.

## Description
Halting problems in automated business logic create vulnerabilities when exit conditions are unreachable or missing due to lack of proper termination guarantees.

Implementation flaws include missing loop bounds checks, inadequate recursion depth tracking, unchecked integer inputs controlling iterations
and hidden conditional logic triggered by specific inputs.

These failures occur in both standalone applications and distributed microservices, where a single non-terminating component can block entire transaction pipelines:
* **Hidden Trigger Vulnerabilities**: Code paths that activate only under specific conditions without authorization checks.
Attackers can probe for undocumented parameters or date-based triggers that execute privileged operations, causing unexpected transactions,
data purges, or state bypasses.

* **Endless Loop Exploitation:** Routines with unreachable exit conditions such as counters that never reach terminal values, 
flags that never change state, or pointers that cycle through the same memory locations indefinitely.
Attackers target these with specially crafted inputs that force the worst-case path.

* **Unchecked Input Manipulation:** pagination handlers or bulk processing functions that accept user-controlled loop bounds without validation.
Attackers submit extreme values (2^31-1) for page sizes, transaction counts, or retry attempts,
forcing systems to attempt processing billions of operations until resources exhaust.

* **Recursive Depth Attacks:** event handlers or parsers that process nested structures without depth limits.
JSON parsers, XML processors, and message handlers become vulnerable when processing self-referential data structures.
Attackers craft inputs with maximum nesting depth, quickly exhausting stack space with just kilobytes of malicious input.
Deeply nested GraphQL queries are a common example.

## Examples

### Scenario 1: XML "Billion Laughs" attack 

XML "Billion Laughs" attack in IBM Sterling File Gateway: IBM Sterling File Gateway expands recursive XML entities without limits. A crafted DTD inflates a small request into billions of entity references, consuming all memory and crashing the gateway. Attack sequence:

1. **An attacker sends a malicious payload**

```shell
POST /filegateway/receiveFile:
<!DOCTYPE lolz [
  <!ENTITY lol "lol">
  <!ENTITY lol1 "&lol;&lol;&lol;">
  …
  <!ENTITY lol9 "&lol8;&lol8;&lol8;">
]>&lol9;

```

2. **File Gateway processes entities recursively until resource exhaustion.**

This results in order ingestion stopping, disrupting supply chains and preventing new transactions from entering the system.

## Mapped CWEs
- CWE-511: Logic/Time Bomb
- CWE-835: Loop with Unreachable Exit Condition ('Infinite Loop')
- CWE-606: Unchecked Input for Loop Condition
- CWE-674: Uncontrolled Recursion

## Sample CVEs
- CVE-2024-11612
- CVE-2022-23437
- CVE-2025-32399