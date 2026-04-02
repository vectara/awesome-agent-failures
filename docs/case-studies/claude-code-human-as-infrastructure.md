# Claude Code Multi-Agent Coordination Failure

## Incident Overview

**Agent**: Claude Code (CLI tool, Claude Opus model)
**Operator**: Solo developer running concurrent agent threads against a monorepo
**Date**: 2025
**Failure Mode**: [Verification & Termination Failures](../failure-modes/verification-termination.md)
**Impact**: Work lost to silent overwrites between concurrent agent sessions; operator forced to serve as manual coordination layer across all threads
**Source**: First-person operator report by [travisbreaks](https://github.com/travisbreaks)

## What Happened

A solo developer was running four concurrent Claude Code sessions against the same monorepo, each working on a different project. Thread A was refactoring a component library. Thread B was updating CI configuration. Thread C was building a new dashboard. Thread D was writing tests.

Thread B committed changes to the CI workflow file. Fifteen minutes later, Thread A ran `git add -A` and committed its refactor, which included a stale copy of the CI file from before Thread B's changes. Thread B's work was silently overwritten. No agent detected the conflict. No warning was raised. The overwrite was only discovered when CI broke an hour later and the operator manually investigated.

This was not an isolated incident. Over the course of daily multi-agent use, the operator documented the same class of failure recurring across different file types and thread combinations. Each agent session operates in complete isolation. There is no cross-thread awareness, no file locking, no shared scratchpad, and no conflict detection.

### Why the Agent Cannot Detect This

Each Claude Code session maintains its own context window. Session A has no knowledge that Session B exists, let alone that Session B modified a file 15 minutes ago. When Session A stages files with `git add`, it captures the working directory state as-is, with no mechanism to check whether tracked files were modified by an external process since the session began.

The agent's verification behavior is limited to "did the command succeed." `git add` succeeded. `git commit` succeeded. From Session A's perspective, everything worked correctly.

## Root Cause Analysis

### Agent-Level Failures

1. **No Cross-Session Awareness**: Each agent session believes it is the only process operating on the codebase. There is no inter-process communication, shared state, or coordination protocol between sessions.

2. **Blind File Staging**: The agent stages files without checking whether they have been modified externally since the session started. `git add -A` captures everything in the working directory, including files the agent never touched.

3. **No Post-Commit Verification**: After committing, the agent does not diff against the previous commit to verify that only intended changes were included. Silent inclusion of reverted files goes undetected.

### Systemic Failures

1. **No Multi-Agent Coordination Primitives**: The agent platform provides no file locking, no event bus between sessions, no shared operation log. Concurrent use is unsupported at the infrastructure level but not prevented.

2. **Optimistic Completion Reporting**: The agent reports success based on command exit codes alone. "Commit succeeded" does not mean "commit contained only the changes I intended."

## Impact

- Developer work lost to silent overwrites, requiring manual investigation and re-application of changes
- CI pipeline broken by reverted configuration, requiring debugging time to identify the cause
- Operator forced to adopt manual coordination practices: tracking which thread is touching which files, telling agents to pause while another commits, reviewing every commit diff before pushing
- Throughput from four concurrent agents was sublinear (estimated ~2x, not 4x) because coordination overhead grew with each additional thread

## Mitigation

### Immediate

The operator added behavioral rules to the agent's instruction file (loaded every session):

> "Git commits are scoped to this thread only. Don't stage files from prior sessions."
> "Never push without explicit approval."

These rules reduced but did not eliminate the problem. The agent lacks the ability to reliably determine which files "belong" to its thread.

### Structural

1. **Manual thread isolation**: The operator began assigning each agent thread to a specific subdirectory and instructing it not to touch files outside that scope.
2. **Commit review gate**: All commits are reviewed by the operator before pushing. The operator diffs every commit to verify no unintended file changes were included.
3. **Sequential commit windows**: Instead of allowing agents to commit freely, the operator coordinates commit timing, allowing only one agent to stage and commit at a time.

### What Would Actually Fix This

The fundamental fix requires platform-level changes:
- **File locking or lease system** that prevents one session from staging files another session has modified
- **Shared operation log** visible to all concurrent sessions
- **Pre-commit hooks** that detect when staged files were modified externally since the session started
- **Cross-session event bus** allowing agents to signal "I'm about to commit" and receive "wait, I have uncommitted changes in that file"

## Lessons Learned

### For AI Agent Users

1. **Concurrent agent sessions create coordination overhead the agent cannot manage.** The operator becomes the synchronization layer. Budget for this.
2. **Review every commit diff before pushing.** The agent's "commit succeeded" message means the git command worked, not that the commit contains only what you intended.
3. **Instruction files reduce but do not eliminate multi-agent failures.** The agent cannot reliably scope its own operations because it has no visibility into what other sessions are doing.

### For AI Agent Developers

1. **Multi-agent coordination primitives are missing.** File locking, shared state, event buses between sessions. Without these, concurrent use is a source of data loss.
2. **Pre-commit verification should be built in.** The agent should diff staged changes against the session's initial state and flag any files it did not explicitly modify.
3. **Session isolation needs to be enforced, not assumed.** If the platform supports multiple concurrent sessions, it needs infrastructure to prevent them from silently overwriting each other's work.

### For Organizations

1. **Concurrent AI agent use is an unsupported configuration that users adopt anyway.** The productivity incentive is obvious, but the coordination risks are real and unmitigated by current tooling.
2. **The operator is the only coordination layer that exists today.** Planning, tooling, and training should account for the human overhead of multi-agent orchestration.

## References

- **Narrative Write-Up**: [When Agents Fail: What 6 Months of Daily AI Use Actually Looks Like](https://travisbreaks.org/transmissions/057-when-agents-fail/) - covers this incident and related failure patterns in narrative form
