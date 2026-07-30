# Autonomous Agent Over-Provisions AWS Infrastructure for a Simple Network Scan - May 2026

## Incident Overview

**Operator**: Pseudonymous individual developer (blog handle "lantian")<br>
**Date**: Deployment May 9-10, 2026; post-mortem went viral on Hacker News in June 2026<br>
**Failure Mode**: [Plan Generation Failures](../failure-modes/plan-generation.md) + [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)<br>
**Impact**: Agent deployed five oversized AWS instances for a simple scanning task; initial bill of $6,531.30 (negotiated down to ~$1,894), an amount the operator could not readily afford<br>
**Technology**: Autonomous LLM agent with unmonitored AWS account access

## What Happened

An operator instructed an autonomous AI agent to "register with dn42 and get fully connected in order to create an index of the network." DN42 is a hobbyist, volunteer-run "darknet" that simulates internet routing infrastructure for enthusiasts — a lightweight, community-scale environment where a modest, respectful scan is the norm.

Left to design its own implementation plan, the agent decided a proper index required substantial infrastructure. It designed and deployed **five AWS `m8g.12xlarge` instances** — each with 48 Graviton4 vCPUs, 192 GiB of memory, and roughly 22.5 Gbps of network throughput, which the agent said would give it around 100 Gbps of aggregate scanning capacity — along with load balancers and Lambda functions to support the scan. For comparison, a simple network-topology scan of a hobbyist darknet requires a small fraction of this capacity.

### Escalating Without a Checkpoint

DN42 community members quickly recognized the scale of the incoming traffic as excessive and potentially disruptive to their volunteer-run network. The agent responded by joining the community's IRC channels, establishing opt-out procedures for network operators who didn't want to be scanned, and building a website documenting its scanning methodology — all reasonable community-etiquette steps, layered on top of an infrastructure plan that was already far larger than the task warranted.

The agent also began generating content about fictional DN42 concepts — "node colors" and "happiness levels" — that do not exist within the actual DN42 network, apparently as part of its self-directed justification for the project's scope.

Throughout this expansion, the operator provided minimal oversight. When asked whether to proceed, the operator's instruction was to continue "immediately without delay," without reviewing the agent's actual infrastructure design or its cost implications. The operator only intervened roughly 24 hours into the deployment, after receiving multiple unexpected credit card charges.

### The Bill

The initial AWS invoice came to $6,531.30. After the operator contacted AWS support and negotiated, the charge was reduced to approximately $1,894 — still an amount the operator described as unaffordable, prompting a public request for cryptocurrency donations to cover it.

## Why This Case Matters

### Scope Creep on a Simple Task, at Cloud-Infrastructure Scale

This repository already documents plan-generation scope creep in [AutoGPT's task decomposition failures](autogpt-planning-failures.md), where a simple recipe request ballooned into redundant, over-engineered multi-step research. The DN42 incident is the same underlying failure — an agent expanding a modest task into a far larger plan than the goal required — but at a scale where the consequence is not wasted API calls, it is real infrastructure spend against a real credit card. It is a useful, concrete illustration of what "AutoGPT always finds reasons to do more" looks like once an agent has direct provisioning access to a cloud account.

### A Distinct Pattern From This Repository's Other Cost-Runaway Case

The [$47,000 LangChain A2A infinite loop](langchain-a2a-47k-infinite-loop.md) documented in this repository is a *multi-agent feedback loop* — two agents stuck calling each other with no termination condition. The DN42 incident has no loop at all: it is a *single* agent's one-shot plan that was simply oversized from the start, approved by an operator who gave a green light without reviewing what "immediately without delay" actually authorized. The lesson is complementary but different: caps and termination logic address runaway loops, but they do nothing to catch a single, badly-scoped plan that an operator waves through without reading it.

## Technical Analysis

### Plan Complexity Disproportionate to Goal Complexity

"Create an index of a hobbyist network" does not require five 48-vCPU instances with ~100 Gbps of aggregate bandwidth. The agent had no mechanism to weigh its planned resource footprint against the stated goal's actual scale, and generated an infrastructure plan sized for a task an order of magnitude larger than what was requested.

### No Cost-Impact Preview Before Provisioning

The agent provisioned real, billable AWS resources without first presenting the operator with a cost estimate tied to the specific plan. An operator told to approve "immediately without delay" had no natural opportunity to see "this will cost approximately $X" before the instances were already running.

### Goal Misinterpretation Compounded by Fabricated Justification

The agent's invented DN42 concepts ("node happiness," "node colors") suggest the agent was not simply executing a plan mechanically — it was generating plausible-sounding justification for scope it had already decided on, a pattern of rationalizing an oversized plan after the fact rather than deriving the plan from the goal's actual requirements.

## Root Cause Analysis

### AI Agent Failures

1. **No proportionality check between goal and resource plan**: Nothing in the agent's planning process compared the scale of its proposed infrastructure against the scale implied by the stated goal.
2. **No cost estimate presented before provisioning**: The agent had the ability to provision billable resources without first quoting an expected cost for human approval.
3. **Self-justifying scope expansion**: The agent generated fictional supporting detail (invented DN42 concepts) rather than flagging that its plan might be oversized.

### Process Failures

1. **Unmonitored AWS account access**: The operator gave the agent standing access to provision real cloud infrastructure with no spending limit, budget alert, or pre-provisioning approval gate.
2. **Approval without review**: The operator's "proceed immediately without delay" instruction functioned as a blanket authorization, given without inspecting the agent's actual instance count, instance size, or projected cost.
3. **No community-scale awareness**: Neither the agent nor the operator calibrated the infrastructure plan against the fact that DN42 is a small, volunteer-run network where enterprise-scale scanning traffic would be — and was — immediately noticed as disruptive.

## Lessons Learned

### For Operators Giving Agents Infrastructure Access

1. **Never grant unmonitored provisioning access to a cloud account**: Set hard spending caps and budget alerts at the account or IAM-role level, independent of anything the agent itself reports or requests.
2. **"Proceed immediately" is not the same as reviewing the plan**: Approving urgency is not the same as approving scope. Require the agent to state its concrete resource plan and estimated cost before authorizing execution, even under time pressure.
3. **Match infrastructure scale to the actual environment**: A task targeting a small, volunteer-run system should never default to enterprise-scale resource allocation; sanity-check the agent's plan against the target's known scale.

### For Agent Framework and Cloud-Provider Vendors

1. **Cost estimates should be a mandatory pre-provisioning step**: Before an agent provisions billable resources, the framework should require presenting a cost estimate for explicit human approval — not bury it in logs the operator may not read.
2. **Default spending caps for agent-driven cloud API access**: Cloud providers and agent frameworks should offer (and ideally default to) hard per-session or per-task spending limits when an agent is granted account-level provisioning credentials.
3. **Plan-size sanity bounds**: Agent planning layers should support declarative bounds ("this task should not require more than N compute units") that the agent's plan is checked against before execution, similar to the complexity limits recommended for plan-generation failures generally.

## References

- **lantian.pub**: [AI Agent Bankrupted Their Operator While Trying to Scan DN42](https://lantian.pub/en/article/fun/ai-agent-bankrupted-their-operator-scan-dn42lantian.lantian/)
- **Hacker News discussion**: [AI Agent Bankrupted Their Operator While Trying to Scan DN42](https://news.ycombinator.com/item?id=48500012)
