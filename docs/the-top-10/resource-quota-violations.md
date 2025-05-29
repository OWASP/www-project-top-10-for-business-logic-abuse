# Resource Quota Violations


## Overview

Business applications often expose endpoints that consume computational resources, trigger external services, or perform
expensive operations without adequate throttling controls.

When systems fail to implement proper rate limiting, usage quotas, or resource consumption monitoring, attackers can overwhelm services,
exhaust system resources, or abuse business functionality to cause financial damage, service degradation, or operational disruption.

This is especially true for software systems that rely on AI and LLMs: overconsumption of tokens by one user can cause DoS
for all other customers.


## Description

Resource abuse vulnerabilities arise when applications lack proper controls over how frequently or intensively users can
consume system resources. These flaws manifest in several critical areas:

* **Missing Rate Limiting**: Endpoints that accept unlimited requests per time period, allowing attackers to flood services with API calls, form submissions, or resource-intensive operations that degrade performance for legitimate users.
* **Inadequate Resource Quotas**: Business processes that lack consumption limits on expensive operations such as file generation, email sending, SMS delivery, or third-party API calls, enabling attackers to rack up costs or exhaust service limits.
* **Unbounded Resource Consumption**: Operations that scale with user input without upper bounds, such as bulk processing requests, large file uploads, or complex queries that can consume excessive CPU, memory, or storage resources.
* **Business Logic Rate Abuse:** Exploiting legitimate functionality at excessive rates to gain unfair advantage, such as rapid-fire voting, automated content scraping, or mass account creation that violates intended usage patterns.
* **Data Scraping & Brute-Force:** Absence of per-user or per-API-key limits enables attackers to scrape entire product catalogs or launch automated credential-guessing campaigns.


## Examples

### Scenario #1: Absent rate limit on an AI-enabled endpoint

An AI-powered recommendation endpoint in an e-commerce platform generates personalized product suggestions by invoking an LLM.
However, it imposes no per-user quotas on the number of tokens consumed or the frequency of calls, allowing a single user to exhaust compute capacity and drive up API costs.

**Legitimate recommendation request:**

```
GET /recommendations \
  -H "Authorization: Bearer eyJhbGciOi..." \
  -H "Accept: application/json"
```

The server:
- Authenticates the JWT.
- Looks up userId and their purchase/browsing history in its own database.
- Calls the AI-enabled recommendation engine to retrieve recommended products.
- Returns a list of products.


**Attack – unbounded request flood:**
```
POST /api/newsletter/subscribe (×10,000 requests)
Body: { "email": "victim@example.com", "list": "weekly" }
```

Because the endpoint lacks any rate limiting or per-user quota:
- Each call spins up an expensive AI inference.
- The attacker drives up compute and API-provider costs.
- Legitimate users experience slow or failed recommendation responses.
- The service risks DoS and large overage bills from its AI vendor.


### Scenario #2: 

A PDF generation service exposes GraphQL endpoints to its customers with a flat limit of 100 calls per minute per user.
Every operation counts as one call. Because the generatePDF mutation uses much more CPU and memory but still counts as a single call
an attacker can exhaust server resources while staying inside the rate limit.

**Legitimate user session:**
1. Get all documents available to a user:
```shell
POST /graphql
Content-Type: application/json

{
  "query": "
    query ListDocuments {
      documents {
        id
        title
      }
    }
  "
}
```

2. Generate a single document and quit:
```shell
POST /graphql
Content-Type: application/json

{
  "query": "
    mutation GeneratePDF($input: PDFInput!) {
      generatePDF(input: $input) {
        jobId
        url
      }
    }
  ",
  "variables": {
    "input": {
      "template": "invoice",
      "data": { /* order details */ }
    }
  }
}
```

**Attack: Mutation flood within rate limit quota**:
```shell
for i in $(seq 1 100); do
  curl -s -X POST https://api.acme.com/graphql \
    -H "Authorization: Bearer <token>" \
    -H "Content-Type: application/json" \
    -d '{
      "query": "
        mutation {
          generatePDF(input: {
            template: \"full_catalog\",
            data: { /* 200-page report */ }
          }) {
            jobId
          }
        }
      "
    }' &
done
wait
```

Note that the attacker stays within the rate limit quota by performing just 100 calls.
However, high CPU consumption causes by excessive PDF generation may cause DoS or slower responses for other users.  

## Mapped CWEs
- CWE-770: Allocation of Resources Without Limits or Throttling
- CWE-400: Uncontrolled Resource Consumption
- CWE-799: Improper Control of Interaction Frequency

## Sample CVE
- CVE-2023-37934
- CVE-2025-26524