---
title: The Top 10
layout:  null
tab: true
order: 1
tags: business-logic-abuse
---

# OWASP Top 10 for Business Logic Abuse – 2025

| Class name                                                 | Coverage<sup>1</sup> | Summary                                                                                                                                                               |
|------------------------------------------------------------|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [BLA1:2025 - Lifecycle & Orphaned Transitions Flaws][bla1] | 13.1%                | Orphaned sessions, tokens, or subflows persist after parent completion, leaving stale state reachable and letting attackers replay actions or corrupt business logic. |
| [BLA2:2025 - Logic Bomb, Loops and Halting Issues][bla2]   | 5.8%                 | Hidden triggers, unbounded loops, or unchecked recursion lacking exit conditions enable endless processing, resource exhaustion, or unintended privileged execution.  |
| [BLA3:2025 - Data type smuggling][bla3]                    | 4.7%                 | Loose type checks, unsafe casting, or deserialization admit mismatched data types, subverting logic, executing code, or corrupting data.                              |
| [BLA4:2025 - Sequential State Bypass][bla4]                | 4.5%                 | Weak state-machine enforcement lets attackers skip mandatory steps or combine invalid states, executing unauthorized actions outside intended progression.            |
| [BLA5:2025 - Data Oracle Exposure][bla5]                   | 4%                   | Distinct messages, UI cues, or timing variations reveal internal state, enabling enumeration, workflow mapping, and targeted attacks.                                 |
| [BLA6:2025 - Missing Roles and Permission Checks][bla6]    | 2.8%                 | Applications omit or misapply role and permission checks, allowing unauthorized actors to perform privileged operations and escalate access.                          |
| [BLA7:2025 - Transition Validation Flaw][bla7]             | 2.2%                 | Attackers bypass mandatory transactional conditions, advancing workflows without required validations and triggering incomplete or invalid operations.                |
| [BLA8:2025 - Replays of Idempotency Operations][bla8]      | 1.5%                 | One-time operations lack uniqueness or consumption tracking, permitting request replays that repeat effects and violate business invariants.                          |
| [BLA9:2025 - Race Condition and Concurrency Issues][bla9]  | 1.1%                 | Concurrent actions on shared resources without synchronization lead to timing races, enabling inconsistent updates or unauthorized outcomes.                          |
| [BLA10:2025 - Resource Quota Violations][bla10]            | ~1%                  | Absent rate limits or usage caps let attackers overconsume resource-intensive endpoints, exhausting compute, inflating costs, and degrading service availability.     |

<sup>1</sup> Of the analyzed security issues on Github. Referer to [the Methodology][methodology] section for further information. 

[bla1]: lifecycle-orphaned-transitions-flaws.md
[bla2]: logic-bomb-loops-halting-issues.md
[bla3]: data-type-smuggling.md
[bla4]: sequential-state-bypass.md
[bla5]: data-oracle-exposure.md
[bla6]: missing-roles-and-permission-checks.md
[bla7]: transition-validation-flaw.md
[bla8]: replays-of-idempotency-operations.md
[bla9]: race-condition-and-concurrency-issues.md
[bla10]: resource-quota-violations.md
[methodology]: ../methodology/methodology.md