---
title: Top10
layout:  null
tab: true
order: 1
tags: example-tag
---

# OWASP Top 10 for Business Logic Abuse – 2025

| Class name                                                  | Summary                                                                                                                                                                                                                  |
|-------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [BLA1:2025 - Action Limit Overrun (ALO)][bla1]              | Overrun Limit of Idempotent Operations arises when unsynchronized concurrent requests exploit a TOCTOU gap to bypass single-use checks and repeatedly execute operations like coupon redemptions or refunds.             |
| [BLA2:2025 - Concurrent workflow order bypass (CWOB)][bla2] | Workflow Order Bypass is a race-condition attack that runs a workflow’s final step before its required prior steps complete, either across distributed commands or within unguarded internal sub-states.                 |
| [BLA3:2025 - Object state manipulations (OSM)][bla3]        | Object state manipulation flaws occur when APIs bind unfiltered user input into internal objects without validating fields or types, allowing attackers to override protected properties, such as roles or permissions.  |
| [BLA4:2025 - Malicious Logic Loop (MLL)][bla4]              | APIs lacking proper gating, loop exit checks, input validation, or recursion limits can be exploited to trigger hidden routines and exhaust resources, leading to service crashes, financial loss, or denial of service. |
| [BLA5:2025 - Artifact Lifetime Exploitation (ALE)][bla5]    | Abuse of one-time or short-lived resources, like tokens, sessions, or temporary files left valid beyond their intended lifecycle, lets attackers replay stale artifacts to access sensitive operations or data.          |
| [BLA6:2025 - Missing Transition Validation (MTV)][bla6]     | Transition validation flaws occur when APIs defer or omit essential checks in multi-step state workflows, letting attackers call later endpoints and enabling unauthorized workflows.                                    |
| [BLA7:2025 - Resource Quota Violation (RQV)][bla7]          | Without proper rate limits, business endpoints triggering heavy operations can be abused to exhaust resources, degrade services, or cause financial harm in AI systems where one user’s token overuse can DoS others.    |
| [BLA8:2025 - Internal State Disclosure (ISD)][bla8]         | When systems show different messages, codes, visuals, delays for valid vs invalid inputs they leak internal states, enabling attackers to lay groundwork for targeted intrusion or fraud.                                |
| [BLA9:2025 - Broken Access Control (BAC)][bla9]             | Flawed or missing role and permission checks in critical workflows let attackers spoof roles, bypass controls, and execute unauthorized actions, causing privilege escalation and data integrity breaches.               |
| [BLA10:2025 - Shadow Function Abuse (SFA)][bla10]           | Shadow functions are unprotected hidden features in production code, like internal APIs or test utilities, which attackers find via code inspection or discovery tools to bypass security and access restricted data.    |

<sup>1</sup> Of the analyzed security issues on Github. Referer to the Methodology section for further information.

[bla1]: docs/the-top-10/action-limit-overrun.html
[bla2]: docs/the-top-10/workflow-order-bypass.html
[bla3]: docs/the-top-10/object-state-manipulations.html
[bla4]: docs/the-top-10/malicious-logic-loop.html
[bla5]: docs/the-top-10/artifact-lifetime-exploitation.html
[bla6]: docs/the-top-10/missing-transition-validation.html
[bla7]: docs/the-top-10/resource-quota-violation.html
[bla8]: docs/the-top-10/internal-state-disclosure.html
[bla9]: docs/the-top-10/broken-access-control.html
[bla10]: docs/the-top-10/shadow-function-abuse.html