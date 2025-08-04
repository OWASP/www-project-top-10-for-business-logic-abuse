---
title: "BLA5:2025 - Data Oracle Exposure"
layout: col-sidebar
tab: false
order: 5
tags: business-logic-abuse
---

## Overview

When systems display different messages, codes, visuals, or delays for valid versus invalid inputs, whether it is in a login
dialog, registration form, password-reset prompt, or data query they leak protected business states.

Attackers exploit these side channels to enumerate accounts, verify resource existence, and map workflow logic, undermining
confidentiality and laying groundwork for targeted intrusion or fraud.

## Description

These side-channels arise because systems tie UI feedback directly to internal checks instead of normalizing all outputs.
As a result, attackers gain an oracle that confirms hidden states.

Common forms include:

- **Distinct error messages**: Feedback such as “username not found” versus “incorrect password” pinpoints which validation
failed, guiding enumeration.

* **Message detail level**: Stack traces or validation notes shown in dialog boxes expose implementation details and code
paths.

* **UI element presence**: Optional fields or links appearing only for valid cases (e.g., “resend activation” shown for
existing users) betray state.

* **Status indicators**: Different icons, colors, or access controls in a dashboard for valid versus invalid resources reveal
existence and permissions.

* **Timing variations**: Delays from hashing or database lookups for valid inputs contrast with immediate failures for invalid
ones, enabling side-channel attacks.

By failing to standardize all user-facing outputs, these systems break the abstraction between business-process checks and
presentation, allowing attackers to reverse-engineer workflows and data.


## Examples

### Scenario #1: Reveal account existence through verbose authentication message

In Joomla before version 3.9.23, entering an unknown username on the administrator login page returns “Username not found,”
whereas a wrong password on a valid account shows “Password incorrect”.

1. Authentication request by a non-existing user:
```shell
POST /administrator/index.php
{
    "username": "ghost",
    "password": "x"
}
```

Response:
- Status: 200 OK.
- UI: “Username not found”.

2. Authentication request by an existing user with wrong password:
```shell
POST /administrator/index.php
{
    "username": "admin",
    "password": "x"
}
```

Response:
- Status: 200 OK.
- UI: “Password incorrect”.

That enables attackers to scan common names and isolate valid admin accounts for brute-force.

### Scenario #2: Request timing differences reveal account existence

Versions of the Fides open-source server prior to 2.44.0 only hashed passwords for existing users, adding ~0.40 s versus
~0.05 s for unknown ones.

1. Authentication request by an existing user with incorrect password:
```shell
POST /api/v1/login
{
    "username": "Alice"
    "password": "xxx"
}
```

Response:
- Latency: 0.45 s.
- Status: 401.

2. Authentication request by a non-existing user:
```shell
POST /api/v1/login
{
    "username": "Bob",
    "password": "yyy"
}
```

Response:
- Latency: 0.05 s.
- Status: 401.

Attackers measure this timing gap to build a list of valid users before attempting password attacks.

## Mapped CWEs
- CWE-1230
- CWE-200
- CWE-203

## Related CVEs
- CVE-2020-35614
- CVE-2024-45052
