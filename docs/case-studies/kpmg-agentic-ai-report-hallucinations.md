# KPMG Withdraws "Agentic AI" Report After GPTZero Finds It Full of AI Hallucinations - June 2026

## Incident Overview

**Firm**: KPMG<br>
**Document**: "Total Experience: Redefining Excellence in the Age of Agentic AI" (45-citation research report, published October 2025)<br>
**Date**: GPTZero's forensic findings reported June 12, 2026; report removed from KPMG's websites<br>
**Investigators**: GPTZero, with reporting from the Financial Times<br>
**Failure Mode**: [Tool Hallucination](../failure-modes/tool-hallucination.md)<br>
**Impact**: Only 5 of 45 citations correctly matched their sources; roughly half of the report's factual claims were false, unsupported, or misattributed; report withdrawn<br>
**Technology**: Generative AI used in drafting (KPMG did not specify the tool)

## What Happened

In October 2025, KPMG published "Total Experience: Redefining Excellence in the Age of Agentic AI," a research report on enterprise adoption of agentic AI systems. GPTZero's forensic review — covered by the Financial Times and later The Register — found the report itself was substantially built on AI-fabricated material: of 45 citations, only 5 correctly pointed to their claimed sources. Roughly half of the report's factual claims were false, unsupported, or misattributed. GPTZero coined the term "vibe citing" for the pattern: AI-generated references that "look convincing until someone actually clicks them."

### The Catalogue of Errors

- **Unsubstantiated named case studies**: The report cited agentic AI deployments at UBS, Swiss Federal Railways, and Transport for London that lacked any traceable substantiation.
- **A self-contradicting headline statistic**: The report claimed 55% of CEOs ranked AI as their top investment priority — directly contradicting KPMG's **own** 2025 CEO Outlook survey, which reported 71% for the same figure. A firm's flagship AI report misquoted the firm's own prior research.
- **A fabricated product capability**: The report claimed Emirates' "Sara" assistant could handle flight-booking tasks. Sara is a robot customer-service assistant with no such booking functionality — a capability invented for the report, not documented anywhere in Emirates' own materials.

### KPMG's Response

KPMG removed the report from its websites while investigating the circumstances of its publication. A spokesperson said the firm takes "accuracy and integrity" seriously and expects adherence to its responsible-AI guidelines.

## Why This Case Matters

### A Third Big-Four/Institutional Hallucinated-Report Case — And the Most Ironic

This repository already documents fabricated-citation research reports from [EY Canada](ey-loyalty-report-hallucinations.md) and a national government's [AI policy draft in South Africa](south-africa-ai-policy-hallucinations.md). This case follows the identical failure pattern — GPTZero forensic review, fabricated or broken citations, named real institutions, report withdrawn — but with a sharper irony: the report itself is *about* agentic AI adoption, marketed as thought leadership on the very technology category that produced its own fabricated content. A report meant to build confidence in agentic AI instead became a demonstration of why that confidence needs external verification.

### Self-Contradiction Within the Firm's Own Data

Unlike EY's report (where fabrications pointed to external sources) or South Africa's policy (invented academic citations), this case includes an error that required no external fact-check at all: the report's headline statistic directly contradicted KPMG's own CEO Outlook survey, published the same month. That kind of internal inconsistency is detectable by a reviewer with no external database access — reading the report against the firm's own concurrent publications would have caught it, and no one did before publication.

## Technical Analysis

### The Pattern Is Now Established, Not Novel

By the time this report surfaced, the "GPTZero finds Big Four/government report is mostly fabricated citations" pattern was already well established in the public record via EY and South Africa. What differs case to case is not the mechanism — generative AI producing plausible but ungrounded citations, published without a verification gate — but which institution's brand authority substituted for that verification. Here it was KPMG's, on a report specifically positioned as expertise about the technology that generated its own flaws.

### Internal Consistency Checks Are Cheaper Than External Verification

Verifying the UBS, Swiss Federal Railways, and Emirates claims required tracing each to a real source. Catching the 55%-vs-71% CEO statistic contradiction required nothing beyond comparing the report against KPMG's own CEO Outlook, published the same month — a check with essentially zero marginal cost that still did not happen.

## Root Cause Analysis

### AI-Specific Failures

1. **No retrieval-grounding for citations or statistics**: Case-study references and headline statistics were generated as plausible text rather than verified against actual sources, including KPMG's own prior research.
2. **Confident, uniform presentation regardless of accuracy**: Fabricated and accurate citations were indistinguishable in formatting and tone, giving reviewers no internal signal about which claims needed scrutiny.

### Process Failures

1. **No fact-checking gate before publication of external-facing research**: A 45-citation report reached publication without a verification pass against its named sources.
2. **No cross-check against the firm's own prior publications**: The self-contradicting CEO statistic shows the report was not checked even against KPMG's own existing body of research, the lowest-cost verification available.
3. **Brand authority substituting for review**: As with EY and South Africa, the publisher's reputation appears to have lowered scrutiny rather than raising it.

## Lessons Learned

### For Firms Publishing AI-Assisted Thought Leadership

1. **Check new reports against your own prior publications first**: This is the cheapest possible verification step and would have caught the report's most embarrassing error before anyone else did.
2. **A report about a technology is not exempt from that technology's failure modes**: If anything, reports about agentic AI adoption warrant *more* scrutiny of their own AI-generated content, not less, given the reputational stakes of getting caught.
3. **Named case studies require sourcing, not just plausibility**: A claim about a specific company's specific deployment (UBS, Swiss Federal Railways, Emirates) needs a traceable source before publication, not just internal consistency with the report's narrative.

### For Readers of AI-Adoption Research

1. **Treat vendor and advisory-firm AI reports as claims to verify, not settled fact**: This is now the third documented case in this repository of a major institution publishing AI-fabricated research; treat specific statistics and named case studies as unverified until traced to a primary source.
2. **Internal contradictions are a fast red flag**: If a new report's statistics don't match the same firm's previous publications on the same topic, that mismatch alone is reason to distrust the rest of the document until verified.

## References

- **The Register**: [KPMG's AI report turns into a demo of AI hallucinations](https://www.theregister.com/ai-and-ml/2026/06/12/kpmgs-ai-report-turns-into-a-demo-of-ai-hallucinations/5255029)
