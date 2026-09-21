# An Empirical Evaluation of LLM-Assisted Threat Modeling for SMEs

**Author:** Vaisakh Narayanan Nair — Faculty of Electrical Engineering, Technical University of Applied Sciences Augsburg

## Overview

SMEs face serious cybersecurity threats but rarely have the budget or in-house expertise for manual, expert-driven STRIDE threat modeling. This paper investigates whether — and how — Large Language Models can automate that workflow, and specifically how much the **prompting strategy** matters. Three strategies are compared: **Zero-Shot (ZS)**, **Few-Shot (FS)**, and **Chain-of-Thought (CoT)**, using Gemini 3.1 Pro Preview against a manually built ground-truth baseline.

## Methodology

- **Reference architecture:** a reproducible SME network derived from a published academic topology — a hierarchical Cisco network with edge firewall, redundant HSRP core switches, six departmental VLANs, a DMZ (DNS/DHCP/Web), a wireless zone, and a branch office connected via site-to-site IPsec VPN. Captured as a 27-asset inventory (AS-01–AS-27) and a Level-1 Data Flow Diagram with six trust boundaries.
- **Prompt architecture:** a fixed four-layer structure (System Prompt → Context Injection → Task Prompt → Constraint Prompt), where only the Task Prompt layer varies between the three experimental conditions. Output is constrained to a standardized six-column Markdown threat table.
- **Evaluation framework:** adapted from ReCEval, scoring each generated threat on binary **Correctness**, ordinal **Informativeness** (0–2), and binary **True Positive** status against a hand-built ground-truth STRIDE coverage matrix — yielding Precision, Recall, and F1 per strategy.
- **Experimental setup:** each strategy run 3× independently at temperature zero (9 runs total, 122 threat entries), graded by two researchers.

## Key Results

| Metric | Zero-Shot | Few-Shot | Chain-of-Thought |
|---|---|---|---|
| Generated threats (mean) | 11.7 | 10.3 | 18.3 |
| Precision | 0.79 | **0.87** | 0.80 |
| Recall (vs. baseline) | 0.40 | 0.39 | **0.63** |
| F1-Score | 0.53 | 0.54 | **0.71** |
| Mean Correctness | 0.91 | 0.94 | 0.92 |
| Mean Informativeness | 1.4 | **1.8** | 1.6 |

- **Chain-of-Thought** maximizes coverage/Recall and overall F1 — it's the only strategy that consistently generates threats for the Distribution Layer and Operator Part — but tends to abstract multiple assets into broader zones, reducing per-asset traceability.
- **Few-Shot** maximizes Precision and Informativeness, producing highly specific, named attack vectors (e.g., "Evil Twin rogue access point," "IKEv2 PSK offline dictionary attack") that mirror the worked examples.
- **Zero-Shot** trails on every metric and shows the largest run-to-run variance; descriptions often just restate the STRIDE category definition instead of naming a concrete attack.
- **Persistent blind spot:** none of the three strategies ever generated threats for the Corporate Branch assets (AS-22–AS-25) in any run, despite the branch being explicitly documented — a pattern the authors link to the "lost in the middle" effect in long contexts.

## Recommendation

A **two-pass pipeline**: run CoT first for broad baseline coverage across all trust boundaries, then run FS to enrich selected entries with concrete, actionable attack vectors — combining CoT's coverage with FS's specificity at bounded token cost. Pure ZS is judged too generic to drive mitigation decisions on its own, useful mainly as a smoke test.

## Limitations

Single LLM (Gemini 3.1 Pro Preview), single topology, single-rater ground-truth baseline, and only three runs per condition. The authors call for replication across multiple model providers and SME topologies, and for future work testing prompt-architecture resilience against adversarial asset descriptions and indirect prompt injection.
