# Amazon Q AI Agent Causes Retail Website Outages - March 2026

## Incident Overview

**Company**: Amazon<br>
**Date**: March 2–5, 2026 (four high-severity incidents in one week)<br>
**Failure Mode**: [Incorrect Tool Use](../failure-modes/tool-use.md) + [Tool Hallucination](../failure-modes/tool-hallucination.md)<br>
**Impact**: ~1.6 million errors, 120,000+ lost orders in first incident; 6.3 million lost orders in second; six-hour customer-facing outage<br>
**Technology**: Amazon Q (AI coding assistant)

## What Happened

Amazon's AI coding assistant "Q" contributed to four high-severity incidents in a single week on Amazon's retail website. An engineer following incorrect guidance from Q — which had referenced an outdated internal wiki — triggered a cascade of failures that locked customers out of checkout, pricing, and account information for six hours.

### The Incident Sequence

1. **March 2 — First Outage**: An engineer followed guidance from Amazon Q that was inferred from an outdated internal wiki page. The resulting code change caused approximately 1.6 million errors and an estimated 120,000 lost orders.

2. **March 5 — Second Outage**: A related incident resulted in 6.3 million lost orders — a substantially larger impact.

3. **Additional Incidents**: Two more high-severity incidents occurred during the same week, contributing to a six-hour meltdown that locked shoppers out of checkout, pricing, and account information.

4. **Internal Response**: Amazon convened a "deep dive" internal meeting and imposed a 90-day "code safety reset" affecting 335 critical retail systems.

### Amazon's Public Response

Amazon initially acknowledged the AI connection in internal documents but later downplayed it in public statements, claiming only one incident involved AI tools. Internal documentation, however, attributed the root cause to "an engineer following inaccurate advice that an agent inferred from an outdated internal wiki."

## Technical Analysis

### Tool Hallucination: Outdated Knowledge

Amazon Q's core failure was providing guidance based on an outdated internal wiki page. This is a tool hallucination — the AI tool produced incorrect output (outdated guidance presented as current) that led the engineer to make a bad decision. The wiki page had likely been accurate at some point but was no longer current, and Q had no mechanism to assess the freshness or validity of its knowledge sources.

### Incorrect Tool Use: Misapplied Guidance

The engineer applied Q's guidance to production-critical retail systems. The AI assistant effectively acted as a tool for code guidance, but its output was incorrect for the current system state, making this also an incorrect tool use scenario — the tool was used in a context where its output was invalid.

### Scale Amplification

The incidents demonstrate how AI-assisted development can amplify the blast radius of errors:
- A single bad recommendation cascaded into millions of errors
- The speed of AI-assisted development meant changes were deployed faster than they could be reviewed
- Critical retail infrastructure was modified based on AI guidance without sufficient human verification

## Root Cause Analysis

### AI Agent Failures

1. **Stale Knowledge Base**: Amazon Q referenced an outdated internal wiki without verifying the currency or accuracy of the information.
2. **No Freshness Assessment**: The AI had no mechanism to assess whether its knowledge sources were still valid or had been superseded.
3. **Confident Incorrect Output**: Q presented outdated guidance with the same confidence as current information, providing no signal to the engineer that the advice might be stale.
4. **No Impact Assessment**: The AI did not assess the potential impact of the recommended changes on production systems.

### Process Failures

1. **Insufficient Code Review**: AI-generated or AI-guided changes to critical systems were deployed without adequate human review.
2. **Stale Documentation**: Internal wiki pages were not maintained, creating a source of incorrect information for both humans and AI.
3. **No Staged Deployment**: Changes to critical retail systems were not deployed incrementally with monitoring at each stage.

## Company Response

### Immediate Actions
- Amazon convened a "deep dive" internal meeting to investigate the incidents.
- A 90-day "code safety reset" was imposed on 335 critical retail systems.
- Senior engineers were required to get code signed off before deployment — a "controlled friction" measure adding human oversight back into the workflow.

### Broader Implications
The incidents occurred as Amazon was spending record amounts on AI infrastructure while simultaneously cutting workforce costs — highlighting the tension between aggressive AI adoption and operational stability.

## Lessons Learned

### For Organizations Using AI Coding Assistants
1. **AI guidance is not authoritative**: Code changes recommended by AI assistants must be independently verified, especially for production-critical systems.
2. **Knowledge freshness matters**: AI assistants that reference internal documentation can only be as accurate as that documentation. Stale docs create stale AI advice.
3. **Critical systems need human gates**: Changes to high-impact production systems should always require human review, regardless of whether they were AI-assisted.

### For AI Coding Assistant Vendors
1. **Source freshness indicators**: AI assistants should flag when their guidance is based on potentially outdated sources.
2. **Confidence calibration**: The assistant should express lower confidence when referencing older documentation or when the guidance hasn't been recently validated.
3. **Impact awareness**: AI assistants should assess the potential blast radius of recommended changes and flag high-risk modifications.

### For Engineering Leadership
1. **AI acceleration needs proportional safeguards**: Faster development velocity from AI tools requires proportionally stronger safety checks, not weaker ones.
2. **"Controlled friction" is valuable**: Adding human review gates for critical systems is not inefficiency — it's risk management.
3. **Maintain documentation**: If AI tools reference internal docs, those docs become production-critical infrastructure and must be maintained accordingly.

## References

- **Fortune**: [Amazon retail website crashes from AI agent's inaccurate advice](https://fortune.com/2026/03/12/amazon-retail-site-outages-ai-agent-inaccurate-advice/)
- **CNBC**: [Amazon convenes 'deep dive' internal meeting to address AI-related outages](https://www.cnbc.com/2026/03/10/amazon-plans-deep-dive-internal-meeting-address-ai-related-outages.html)
- **TechRadar**: [Amazon making even senior engineers get code signed off following outages](https://www.techradar.com/pro/amazon-is-making-even-senior-engineers-get-code-signed-off-following-multiple-recent-outages)
