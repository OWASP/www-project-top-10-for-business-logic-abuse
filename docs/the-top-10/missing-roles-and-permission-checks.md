---
title: "BLA6:2025 - Missing Roles and Permission Checks"
layout: col-sidebar
tab: false
order: 6
tags: business-logic-abuse
---

## Overview

Business workflows relying on gated role validations assume that only entitled actors execute sensitive transitions
(e.g., branch deletion, transaction approval).

When implementations omit roles or permissions checks or apply them incorrectly,
attackers manipulate role identifiers or permission assignments to bypass controls and perform unauthorized operations,
leading to privilege escalation and data integrity breaches.


## Description

When a system exposes endpoints without verifying that the requester has the necessary privileges, unauthorized actors
can trigger sensitive transitions meant only for higher-tier roles.

Common failures include endpoints that omit role checks entirely, authorization logic trusting client-supplied parameters,
overly broad default permissions, and identifier tampering (BOLA) to bypass intended restrictions. These lapses allow
attackers to sidestep intended controls, modify or delete protected resources, and disrupt critical business workflows.

In practice, these authorization gaps surface through several common failures:
* **Missing Role Checks:** Endpoints accepting any authenticated token for sensitive actions due to absent server-side guards.

* **Flawed Logic Trusting Client Data:** Authorization based on headers or query parameters (e.g., X-User-Role) that
clients can forge to gain elevated rights.

* **Overly broad ACLs:** Default or misconfigured permissions granting lower-tier roles access to administrative functions
or data through hidden or undocumented endpoints.

* **Identifier tampering:** Manipulating object IDs in paths or payloads to access or modify others’ resources (e.g., changing
`/orders/200` to `/orders/201`). This is essentially the BOLA vulnerability.

## Examples

### Scenario #1: Gitlab branch deletion

A Gitlab branch deletion endpoint lacked any role or permission check, so any valid token will succeed regardless of its scope:
1. For a legitimate call by a maintainer, the system verifies the token is valid and then deletes the branch:
```shell
DELETE /api/v4/projects/:projectId/repository/branches/:branchId

Authorization: Bearer MAINTAINER_TOKEN
```

2. However, the same call can be performed by a user without delete_branch permission. Since the system doesn’t verify the
role of the caller, that call will succeed as well: 
```shell
DELETE /api/v4/projects/:projectId/repository/branches/:branchId

Authorization: Bearer MAINTAINER_TOKEN
```


### Scenario #2: Allowing users to edit their permissions

A vulnerability in the component /households/permissions of hay-kot mealie v2.2.0 allows group managers to edit their own
permissions. Attack sequence:
1. Obtain a bearer token via the Mealie token endpoint.
2. Add more permission to a user:
```shell
PUT /api/households/permissions
{
    "userId": "fb97d64d-7a4e-4287-9708-f56b3417869b",
    "canInvite": true,
    "canManageHousehold": true,
    "canManage": true,
    "canOrganize": true,
}
```

This way users can escalate their privileges.

## Mapped CWEs
- CWE-863
- CWE-862
- CWE-732
- CWE-284
- CWE-639

## Sample CVEs
- CVE-2023-3290
- CVE-2023-3286
- CVE-2024-55070
- CVE-2021-39931