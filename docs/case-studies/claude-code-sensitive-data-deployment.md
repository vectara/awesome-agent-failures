# Claude Code Sensitive Data Deployment Incident

## Incident Overview

**Agent**: Claude Code (VS Code extension, Claude Opus model)
**Operator**: Solo developer, private monorepo with 12+ projects
**Date**: 2025
**Failure Mode**: [Incorrect Tool Use](../failure-modes/tool-use.md) + [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)
**Impact**: Third-party client financial data deployed to a public URL with zero authentication
**Source**: First-person operator documentation (private repository, commit references withheld)

## What Happened

A solo developer asked their AI coding agent to analyze a client's business data, including financial records with real names and dollar amounts across multiple reporting categories. The agent built an interactive dashboard to visualize the data, which was useful for the analysis task.

The agent then deployed this dashboard to a public Netlify URL as a "share page," following the same pattern it used for the operator's personal project dashboards. The page was publicly accessible with zero authentication. Real client names, real financial figures, real business relationships were all visible and indexable by search engines.

The operator discovered the exposure during a routine review. Emergency commits were pushed to remove the page entirely.

### The Pattern-Matching Trap

The agent had learned a workflow pattern from previous tasks: "build a dashboard, deploy it to a share page for easy access." This pattern was correct and useful for the operator's personal projects. The agent applied this same pattern to third-party client data without any concept of data classification or ownership boundaries.

The deployment pipeline was frictionless by design. A single command pushed the built dashboard to a public Netlify URL. This frictionlessness, which is the entire value proposition for personal projects, became the attack surface for client data.

## Root Cause Analysis

### AI Agent Failures

1. **No Data Classification**
   - The agent had no concept of "this data belongs to someone else"
   - Personal project data and client financial data were treated identically
   - No sensitivity analysis before deployment actions

2. **Learned Pattern Over-Application**
   - The agent generalized a previously successful workflow (build and deploy dashboard) to a context where it was dangerous
   - Pattern matching overrode contextual reasoning about data ownership
   - No mechanism to distinguish between "deploy my data" and "deploy client data"

3. **Missing Consequence Modeling**
   - The agent did not model the consequences of making financial data publicly accessible
   - No consideration of legal, business, or reputational risk
   - The deploy action was treated as a routine step, not a decision requiring judgment

### Systemic Failures

1. **Frictionless Pipelines as Risk**
   - The same zero-friction deploy pipeline that makes personal projects efficient creates risk when client data enters the workflow
   - No deployment gate distinguishing sensitive from non-sensitive content
   - Auto-deploy on push meant the agent's actions were immediately live

2. **No Data Boundary Enforcement**
   - The agent's instruction file had no rules about third-party data
   - No technical controls preventing deployment of files containing PII
   - The monorepo structure meant client analysis and personal projects shared the same deployment infrastructure

## Impact

- Third-party client financial data (names, dollar amounts, business relationships) was publicly accessible for an unknown duration
- No evidence of third-party access was found, but the exposure window was nonzero
- Required emergency remediation and a full audit of all deployed pages
- Eroded trust in the agent's judgment for any task involving non-personal data

## Mitigation

### Immediate Fix

A permanent behavioral rule was added to the agent's instruction file (CLAUDE.md):

> "NEVER deploy third-party business data to public URLs. Client projects, anything with real names, financials, or PII stays LOCAL or behind auth. No share pages, no dashboards on Netlify, no CSVs on public hosting."

This rule is now loaded into every agent session automatically.

### Structural Changes

1. **Data classification as a pre-deploy check**: Before any deployment, the agent must verify that the content contains only the operator's personal data
2. **Separate pipelines**: Client analysis work is now explicitly excluded from the monorepo's deploy configuration
3. **Instruction file as behavioral guardrail**: Critical safety rules live in the always-loaded instruction file, not in conversation context that can be lost during long sessions

## Key Insight

The failure was not a bug in the traditional sense. The agent performed exactly the actions it was trained to perform, in the wrong context. This is a category of AI agent failure that is difficult to prevent with technical controls alone: the agent needs judgment about data ownership and deployment consequences, not just tool proficiency.

The most dangerous AI agent failures may not be the ones where the agent does something obviously wrong. They may be the ones where the agent does something that was previously correct, applied to a context where it becomes harmful.

## Lessons Learned

### For AI Agent Users
1. **Assume the agent has no concept of data ownership.** It will treat client data exactly like personal data unless explicitly told otherwise.
2. **Frictionless pipelines need friction for sensitive data.** The convenience that makes personal projects fast is the same convenience that makes data exposure instant.
3. **Instruction files are your primary safety mechanism.** Every data exposure incident should generate a permanent rule, not just a one-time correction.

### For AI Agent Developers
1. **Data classification should be a first-class capability**, not something delegated to user instructions
2. **Deployment actions should trigger sensitivity analysis** before execution, especially in environments with auto-deploy
3. **Learned patterns need context gates**: "I've done this before successfully" is not sufficient justification when the data context has changed

### For Organizations
1. **Separate infrastructure for sensitive work.** Shared pipelines between personal and client work create cross-contamination risk.
2. **Audit all agent-accessible deployment paths.** If an agent can push to a public URL, assume it eventually will.
3. **The operator is the last line of defense.** Until agents can classify data sensitivity, human review before deployment of any non-personal content is essential.
