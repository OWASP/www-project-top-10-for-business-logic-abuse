---
title: Methodology
layout: null
tab: true
order: 1
tags: business-logic-abuse
---

## Overview

We modelled business-logic vulnerabilities as automata by mapping the Turing-machine primitives to real-world logic flows.
We chose this framing because any business rule can be expressed as a Turing machine, guaranteeing that our taxonomy can
represent every possible logic flaw.

## Data collection and analysis

We used a large language model to validate that the proposed model provides sufficient coverage by classifying all CVEs
from 2023 to 2025, clustered the classification results, and verified the model against a sample of 25k GitHub security
issues. This confirms that the model is both theoretically complete and practically aligned with real-world vulnerabilities.

The analysis was performed in four steps:

### Stage 1: Model definition and initial classification

We started with the basic groups of issues in regards to the Turing-machine primitives: Tape, State, Transition. For each
group, we introduced around 20 root causes which resulted in 63 distinct classes of vulnerabilities.

To validate the coverage, we used an LLM to classify all CVEs from 2023 to 2025 (76k CVE in total).

### Stage 2: Clustering of existing CVE and publicly known exploits

We took the high-confidence classifications from Stage 1 and removed any CVE with a low correlation score. We then applied
several clustering algorithms using the initial root-cause labels as feature vectors, and selected configurations that maximized
cohesion and silhouette score.

Next, we discarded clusters whose silhouette fell below our threshold and excluded individual CVE with low classification
confidence. Similar clusters were then merged to eliminate redundancy.

Finally, we remove clusters that are not related to business logic attacks and are covered with other Top 10s, such as:
- Issues with cryptography.
- Security misconfigurations.
- Injection attacks.
- Improper API and asset inventory management including unsafe API consumption.

Result: 12 tightly-bound, high-confidence clusters of real-world exploits.

### Stage 3: Clustering of existing CVE and publicly known exploits

As the next step, we removed any clusters whose coverage fell below our threshold. We then added three additional clusters
(Race Condition and Concurrency Issues, Logic Bomb, Loops and Halting Issues, Resource Quota Violations) to cover gaps in
the taxonomy.

After merging similar groups and polishing definitions, we arrived at ten cohesive clusters of vulnerabilities.

### Stage 4: Clustering of existing CVE and publicly known exploits

We recognized that CVE records emphasize impact and exploit details rather than root-cause insights. To fill that gap, we
drew a random sample of 25k security-related issues from GitHub.

We mapped issue descriptions and tags back to our ten clusters and measured how well each cluster captured real-world problem
reports. Based on alignment rates, we refined cluster definitions, merged or split groups to match patterns seen in developer
discussions, and validated that our taxonomy holds outside the CVE corpus.