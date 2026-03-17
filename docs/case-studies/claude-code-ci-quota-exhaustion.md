# Claude Code CI Quota Exhaustion

## Incident Overview

**Agent**: Claude Code (CLI tool, Claude Opus model)
**Operator**: Solo developer, private monorepo with 6+ packages
**Date**: 2025
**Failure Mode**: [Plan Generation Failures](../failure-modes/plan-generation.md) + [Incorrect Tool Use](../failure-modes/tool-use.md)
**Impact**: Complete CI lockout for the billing period after the agent burned 2,000/2,000 GitHub Actions free-tier minutes in a single session
**Source**: First-person operator report by [travisbreaks](https://github.com/travisbreaks). Documented from production use of Claude Code across a private monorepo (2024-2025). This case study serves as the primary public record.

## What Happened

The operator asked the agent to fix lint and type errors across a monorepo containing 6+ packages. The correct approach would have been to run all checks locally first (`npx biome check`, `tsc --noEmit`), collect all failures, fix them, and commit once. Instead, the agent adopted an incremental strategy:

1. Fix one error
2. Commit the fix
3. Push to GitHub
4. Wait for CI to run
5. Read the CI output to discover the next error
6. Repeat

This loop continued for 8+ commits across 6+ packages. Each push triggered a full CI pipeline: dependency installation, building all packages, running lint across the entire monorepo, and executing tests. The monorepo's CI was not lightweight. Each run consumed significant compute minutes.

By the end of the session, the repository had burned through all 2,000 GitHub Actions free-tier minutes for the billing period. Every project in the organization was blocked from CI for the remainder of the month. Not just the project being fixed: every project, because GitHub Actions minutes are shared across the organization.

### Why the Agent Chose This Strategy

The agent optimized for incremental correctness. "Fix one thing, verify it worked, move on" is a reasonable strategy when verification is free. The agent treated CI as its verification tool, the way a developer might run a local linter after each change. But CI is not a local linter. It is a shared, metered resource with real cost per invocation.

The agent had no model of CI minutes as a finite, billable resource. Nothing in its training, its instruction file, or its conversation context told it that each push costs compute time from a limited pool. It applied a locally-rational strategy (incremental verification) using an inappropriate tool (remote CI instead of local checks).

## Root Cause Analysis

### Agent-Level Failures

1. **No Resource Cost Model**
   - The agent has no concept of CI minutes as a consumable resource
   - Each "push and wait for CI" action appeared costless from the agent's perspective
   - No threshold or budget tracking for external resource consumption

2. **Incremental Strategy Without Cost Analysis**
   - The agent chose a fix-one-push-one strategy without evaluating whether batch execution would be more efficient
   - "Fix, commit, push, wait for CI" was treated as the default workflow rather than an expensive choice
   - No comparison of local verification vs. remote verification costs

3. **CI as a Linting Tool**
   - The agent used the full CI pipeline as its error discovery mechanism
   - Local equivalents (`npx biome check`, `tsc --noEmit`) were available but not used
   - The agent did not distinguish between "tools that verify locally for free" and "tools that consume shared resources remotely"

### Systemic Failures

1. **No Resource Guardrails**
   - GitHub Actions provides no per-session or per-repository minute caps (only organization-wide limits)
   - The CI pipeline had no early-exit optimization (it ran the full suite even for lint-only changes)
   - No alerting mechanism warned the operator or agent that minutes were being consumed rapidly

2. **Shared Resource Pool**
   - GitHub Actions minutes are pooled across the entire organization
   - One agent session burning through the quota affected every project and every contributor
   - The blast radius of the agent's strategy extended far beyond the task it was working on

## Impact

- Total CI lockout for the entire GitHub organization for the remainder of the billing period
- All projects lost automated testing and deployment pipelines
- Manual verification required for all subsequent deploys until the quota reset
- Developer workflow disruption across every project in the organization, not just the one the agent was fixing
- Exposed a systemic risk: any agent session with push access can silently exhaust shared CI resources

## Mitigation

### Immediate Fix

The operator added explicit instructions to the agent's instruction file (CLAUDE.md):

1. **Run all checks locally before pushing.** `npx biome check` for lint, `tsc --noEmit` for type errors, `npm run build` for build verification.
2. **Batch fixes into single commits.** Fix all errors in a package before committing, not one at a time.
3. **Never use CI as a linting tool.** CI is for final verification, not iterative error discovery.

### Structural Changes

1. **Local validation script**: A pre-push validation script (`validate-deploy-config.mjs`) was created that runs the same checks CI would run, catching common issues before they reach the remote pipeline.
2. **Pre-push gate in instruction file**: The agent's instruction file now includes a mandatory pre-push checklist (build, lint, type-check) that must pass before any push.
3. **CI pipeline optimization**: The CI pipeline was updated with path-based triggers so that changes to one package only run checks for that package, reducing per-run cost.

## Lessons Learned

### For AI Agent Users

1. **AI agents have no concept of metered resources.** CI minutes, API rate limits, cloud compute costs: the agent treats all external services as free and unlimited unless explicitly told otherwise.
2. **Add resource constraints to your instruction file.** "Run checks locally before pushing" and "batch commits" are not obvious to an agent that has never paid a bill.
3. **Monitor shared resource pools during agent sessions.** A single agent session can exhaust resources that affect your entire organization.

### For AI Agent Developers

1. **Resource awareness should be a first-class agent capability.** Agents that can trigger external services (CI, cloud APIs, deployment pipelines) need a model of cost per invocation.
2. **Prefer local tools over remote tools when both are available.** An agent choosing between `npx biome check` (free, instant) and "push to CI and wait" (metered, slow) should default to the local option.
3. **Batch optimization should be a planning primitive.** When an agent has multiple items to fix, it should evaluate whether incremental or batch execution is more efficient before starting.

### For Organizations

1. **Set per-repository or per-workflow minute caps** if your CI platform supports them. Organization-wide pools mean one runaway session can lock out everything.
2. **Implement CI cost alerting.** Automated alerts when minute consumption spikes can catch agent-driven resource exhaustion before it hits the limit.
3. **Treat CI access as a privileged operation.** Agents with push access to repositories with CI pipelines have implicit access to your compute budget. Scope this accordingly.
