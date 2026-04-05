# Claude Code Context Compaction Drops Operational State

## Incident Overview

**Agent**: Claude Code (CLI tool, Claude Opus model)<br>
**Operator**: Solo developer, private monorepo with 12+ projects<br>
**Date**: September 2025 through March 2026 (recurring)<br>
**Failure Mode**: [Verification & Termination Failures](../failure-modes/verification-termination.md)<br>
**Impact**: Agent loses safety constraints and operational state mid-task after context window compression, risking repeated failures including data exposure<br>
**Source**: First-person operator report by [travisbreaks](https://github.com/travisbreaks). Documented from production use of Claude Code across a private monorepo. This case study serves as the primary public record. See also the [OpenClaw email deletion case study](openclaw-email-deletion.md), which documents context compaction causing a single catastrophic event; this case study documents the same root cause manifesting as gradual, recurring state loss across long sessions.

## What Happened

Claude Code's VS Code extension performs "context compaction" when the conversation context window fills up. The extension summarizes the conversation history to free up token space, allowing the session to continue rather than forcing the user to start a new thread. However, this summarization consistently loses granular operational state: which files were read vs. modified, which edits are partially complete, what the current working state of a multi-step task is, and (critically) what safety constraints were established earlier in the conversation.

After compaction, the agent resumes with confidence based on the compressed summary. It "remembers" it was working on a task but loses the specifics. It may re-read files it already processed, skip steps it believes it completed, or make assumptions about state that are wrong.

### Where This Gets Dangerous

In one incident, the agent's post-compaction behavior mirrored a previous data exposure pattern. Earlier in the conversation, the operator had established context that the data being analyzed belonged to a third-party client and must not be deployed publicly. After compaction, that constraint was lost from the summary. The compacted context retained "working on a data dashboard" but dropped "this is client data, do not deploy." The operator caught the drift before any exposure occurred, but only because they recognized the behavioral pattern from a prior incident.

### The Core Problem

Context compaction is lossy by design. That is the tradeoff for continuing a long session instead of starting fresh. But the summarizer does not know which details are load-bearing for the current task. A file path, a half-completed edit, a "do not deploy" flag: these look like minor details to a generic summarizer, but they are critical operational state. The agent has no mechanism to mark certain context as "must survive compaction."

### Relationship to the OpenClaw Email Deletion Case Study

The [OpenClaw email deletion incident](openclaw-email-deletion.md) documented context compaction as the root cause of a single, dramatic failure: an agent mass-deleted emails after compaction silently dropped the "suggest, don't execute" constraint. That case study captures the acute risk, where one compaction event causes one catastrophic action.

This case study documents the chronic version of the same root cause. Rather than a single high-impact event, context compaction here caused repeated, low-grade state loss across dozens of long sessions over several months. The operator experienced gradual erosion of task context, safety constraints, and operational state, each instance individually recoverable but collectively representing a persistent reliability problem. The mitigation strategy (persistent memory files, thread-scoped state, instruction-file constraints) was developed specifically because the failure pattern was recurring, not one-off.

The two case studies are complementary: OpenClaw shows what happens when compaction hits a safety-critical constraint once; this case study shows what happens when compaction degrades operational reliability session after session.

## Root Cause Analysis

### Agent-Level Failures

1. **No State Prioritization During Compaction**
   - The summarizer treats all conversation context with equal weight
   - Safety constraints established mid-conversation are not flagged as high-priority
   - Operational details (file paths, partial edits, PIDs) are compressed away as "noise"

2. **Confident Resumption on Incomplete State**
   - After compaction, the agent does not signal uncertainty about its compressed context
   - It resumes with the same confidence level as before compaction
   - The operator has no way to know what was lost without re-establishing context manually

3. **No Pre-Compaction Checkpoint**
   - The agent does not automatically save current state before compaction occurs
   - There is no prompt to the operator that compaction is about to happen
   - Critical state exists only in the context window, with no persistent backup

### Systemic Failures

1. **Context Window as the Only State Store**
   - The agent's entire understanding of the current task lives in the context window
   - There is no external state store that persists across compaction events
   - Safety constraints established through conversation are as fragile as any other context

2. **No Compaction-Aware Architecture**
   - The compaction process is a platform-level operation invisible to the agent
   - The agent cannot register "protected" context that must survive summarization
   - No hooks exist for the agent to checkpoint state before compaction triggers

## Impact

- Repeated instances of post-compaction confusion requiring the operator to re-establish task context
- Wasted time as the agent re-reads files, re-discovers state, or repeats completed steps
- Risk of safety constraint loss: "do not deploy this data" or "this is a client project" can be summarized away
- Operator trust erosion, since the agent's post-compaction behavior is unpredictable
- Near-miss on a data exposure incident when deployment constraints were lost from context

## Mitigation

### Immediate Fix

The operator added behavioral rules to the agent's instruction file (CLAUDE.md), which is loaded at the start of every session and survives compaction because it is re-read from disk:

1. The agent must warn the operator before context fills, rather than letting compaction happen mid-task
2. The agent must checkpoint progress to persistent memory files every ~45 minutes during long tasks
3. Before compaction, the agent must write current state, PIDs, pending work, and exact resume commands to a memory file
4. Critical safety rules (data classification, deploy restrictions) live in the always-loaded instruction file, not in conversation context

### Structural Changes

1. **Persistent memory system**: A file-based memory architecture (30+ markdown files) that stores state outside the context window. The agent loads relevant files at session start and writes checkpoints during long tasks.
2. **Thread-scoped state files**: Each agent session writes its operational state to a dedicated file, so that context can be recovered after compaction or session restart.
3. **Safety constraints in instruction files**: Rules like "never deploy client data" are now in the always-loaded instruction file rather than established through conversation. This ensures they survive any compaction event.

## Lessons Learned

### For AI Agent Users

1. **Any constraint established through conversation is fragile.** If a safety rule only exists because you told the agent during the session, it will not survive context compaction. Put critical rules in the instruction file.
2. **Long sessions are high-risk sessions.** The longer a session runs, the more likely compaction will occur and the more state there is to lose. Consider breaking long tasks into shorter sessions with explicit handoff documents.
3. **Build external state stores.** Persistent files, checklists, and memory documents are more reliable than the agent's context window for tracking multi-step work.

### For AI Agent Developers

1. **Compaction needs state awareness.** The summarizer should distinguish between "background context" and "active operational state" and preserve the latter with higher fidelity.
2. **Pre-compaction hooks are essential.** The agent (or operator) needs a signal before compaction occurs, with the ability to checkpoint state to persistent storage.
3. **Safety constraints need special handling.** Rules about data sensitivity, deployment restrictions, and access controls should be tagged as "must survive compaction" and carried forward verbatim.

### For Organizations

1. **Context window limits create a silent risk boundary.** Teams using AI agents for long tasks should understand that context compaction can silently drop safety constraints, not just convenience context.
2. **Instruction files are the most reliable safety mechanism.** They are loaded from disk, not from conversation memory. Every critical constraint should live there, not in chat history.
3. **Audit post-compaction behavior.** If your AI agent workflows involve long sessions, test what happens after compaction. Verify that safety constraints, data classification rules, and operational state survive the transition.
