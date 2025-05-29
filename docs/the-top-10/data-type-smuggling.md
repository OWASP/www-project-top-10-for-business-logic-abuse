# Data type smuggling


## Overview
This class of vulnerabilities occurs when systems fail to enforce strict data‐type checks: unchecked casting, unsafe deserialization
and loose conversions let attackers slip malformed inputs through, enabling authentication bypass, service disruption, data
corruption or arbitrary code execution that undermines business rules and controls.

## Description
At their core, these vulnerabilities stem from processing inputs without verifying they match the expected data type and
from unsafe serialization. For example, attackers can send arrays, strings or floats instead of the expected integers or
booleans, allowing them to bypass essential business checks. Some common form include:

* **Unsafe serialization:** Systems that deserialize untrusted or loosely formatted data without strict schema validation
or integrity checks can accept crafted payloads that execute arbitrary code, corrupt application state, or bypass business-rule
enforcement
* **Mismatched Input Types:** Passing an array parameter to a scalar field or vice versa causes validation routines to misinterpret
values, letting attackers skip controls
* **Incorrect Type Conversions:** Manual or implicit casts convert data to a different type without range or semantic checks,
truncating or reinterpreting values used for payments, quotas, or access flags
* **Resource Access Type Confusion:** Accessing objects or memory buffers as the wrong type leads to logical or memory-safety
errors, enabling unauthorized data exposure or code execution
* **Numeric Truncation and Sign Flips:** Converting larger integer types to smaller ones without checking bounds causes
underflows or sign inversions, corrupting quantity fields or thresholds
* **Implicit Coercion Side Effects:** Languages that auto-promote or coerce values (e.g., PHP loose comparison, C integer
promotions) can evaluate malicious inputs as valid, undermining business rules

Combined, these weaknesses enable attackers to subvert business logic by exploiting simple type mismatches in code, ranging
from bypassing authentication and manipulating transaction amounts to triggering service outages.

## Examples

### Scenario 1: Denial-of-Service through an header type conversion

The Drupal Security Kit module (≤ 2.0.2) improperly handles header configuration parameters: if a header value is passed
as an array instead of a string, the code misinterprets the type and crashes the HTTP response generation, resulting in
a Denial-of-Service.

**By sending a JSON-encoded array in place of a string header, an attacker can trigger the DoS:**
```shell
GET /api/v1/ \
     -H "X-Content-Security-Policy: []"
```

Here, the Security Kit expects "X-Content-Security-Policy" as a string but receives an array (`[]`), causing a type confusion
and forcing Drupal to crash the request handler.


## Mapped CWEs
- CWE-1287: Improper Validation of Specified Type of Input
- CWE-704: Incorrect Type Conversion or Cast
- CWE-843: Access of Resource Using Incompatible Type
- CWE-681: Incorrect Conversion between Numeric Types
- CWE-192: Integer Coercion Error


## Sample CVEs
- CVE-2024-13275
- CVE-2022-25845
- CVE-2017-9805