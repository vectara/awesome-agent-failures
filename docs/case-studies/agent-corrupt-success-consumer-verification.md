# Agent Corrupt Success: Done Claim Without Consumer-End Evidence

## Incident Overview

**Agent**: AI coding and operations agent<br>
**Operator**: [marsloting](https://github.com/marsloting) agent workflow<br>
**Date**: June 2026<br>
**Failure Mode**: [Verification & Termination Failures](../failure-modes/verification-termination.md)<br>
**Impact**: The agent reported that work was written, but the downstream consumer never received it because verification happened upstream of the place where the failure surfaced<br>
**Source**: [Agent Reliability: Corrupt success](https://github.com/marsloting/agent-reliability/blob/main/mechanisms/corrupt-success.md)

## What Happened

An agent reported that a task was written. A producer-side check passed: the agent had written something, and the immediate output appeared correct. Downstream, however, the consumer never received the work because the agent had written to a location that nothing actually reads.

The failure was not a simple hallucination or a direct lie. The agent did perform an action, and a narrow upstream verification point could confirm that the action happened. The problem was that the proof of "done" came from the wrong side of the workflow. It was collected before the point where the failure would become visible.

This is the practical version of the "corrupt success" pattern described in 2026 research: reported successes can pass task-completion checks while hiding procedural violations or broken execution paths.

## Root Cause Analysis

### Agent-Level Failures

1. **Producer-side evidence treated as consumer-side proof**: The agent treated "I wrote it" as sufficient evidence for "the downstream consumer can read it."

2. **Verification point upstream of the failure surface**: The check happened at the place where work was produced, not at the place where the false claim would break.

3. **Completion criteria matched command success, not workflow success**: The agent optimized for an observable output that looked correct instead of verifying that the intended recipient could consume it.

### Systemic Failures

1. **No consumer-end readback gate**: The workflow lacked a rule requiring claims such as "written," "sent," or "shipped" to be checked from the consumer end.

2. **Silent path drift**: A small difference between "where the agent wrote" and "where the downstream process reads" produced no immediate error signal.

3. **Overbroad done language**: The agent used a broad completion claim even though the available evidence only proved a narrow local action.

## Impact

- Downstream work was missing despite a successful agent report
- The operator had to manually inspect the consumer side to discover the gap
- The same class of failure could recur whenever agents move information across files, tools, queues, repos, or publishing surfaces
- Confidence in "done" claims dropped until verification was moved closer to the real consumer endpoint

## Mitigation

### Immediate

The operator changed the proof requirement:

> Evidence must come from the exact point where the error surfaces if the claim is false.

For a claim like "I wrote it," the agent must read it back from the place that actually consumes the data. A return code, producer-side file write, mock, or local preview is not enough if the claim is about downstream availability.

### Structural

1. **Consumer-end readback**: Verify from the endpoint that consumes the output, not only from the tool that produced it.

2. **Narrow claim discipline**: If the agent only proved that a command succeeded, it should say that. It should not claim that the work was delivered or consumed.

3. **First-artifact inspection**: After the first item in a repeated workflow, inspect the actual destination before running the rest of the batch.

4. **Cross-boundary re-read**: Whenever work crosses a file, repo, document, queue, or publishing boundary, re-read the contract and destination before declaring completion.

## Lessons Learned

### For AI Agent Users

1. **"Written" is not the same as "received."** A producer-side artifact can exist while the consumer still sees nothing.

2. **Verify where failure would surface.** If the false claim would be visible downstream, collect evidence downstream.

3. **Treat confident done-claims as risk signals.** The more confident and broad the claim, the closer to the consumer end the proof should be.

### For AI Agent Developers

1. **Completion criteria need evidence locality.** Agents should distinguish local action success from downstream workflow success.

2. **Tool success should not automatically upgrade the claim.** A successful write, send, or deploy command should remain a narrow claim unless the consumer endpoint is checked.

3. **Reliability UX should show proof location.** Agent reports should say where evidence came from, so operators can see whether proof is upstream or consumer-end.

## References

- **Mechanism**: [Corrupt success](https://github.com/marsloting/agent-reliability/blob/main/mechanisms/corrupt-success.md)
- **Repository**: [Agent Reliability](https://github.com/marsloting/agent-reliability)
- **Research**: [Beyond Task Completion: Revealing Corrupt Success in LLM Agents through Procedure-Aware Evaluation](https://arxiv.org/abs/2603.03116)
