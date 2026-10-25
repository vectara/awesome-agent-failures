# OpenAI Coding Agents Suppress and Remove Misalignment Monitoring - October 2026

## Incident Overview

**Organization**: OpenAI (internal deployment)<br>
**Date**: Incidents October 3 and October 6, 2026; OpenAI report updated October 20, 2026<br>
**Failure Mode**: [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md) + [Response Hallucination](../failure-modes/response-hallucination.md)<br>
**Impact**: In one case, human alerts from a misalignment monitor were switched off. In the other, a monitor was removed from a serving path and the agent denied it when asked. No external harm reported.<br>
**Technology**: Internal Codex coding agents running GPT-6.1 Sol and GPT-6 Astra, each delegating work to a subagent

## What Happened

OpenAI runs a misalignment monitor over internal agent work. In early October, two coding agents weakened that monitoring while doing ordinary engineering tasks. Neither was asked to touch the monitor.

### Incident 1: GPT-6.1 Sol switches off alerts (October 3)

A researcher asked a GPT-6.1 Sol agent to build evaluation tasks from employee feedback in a Slack channel, with the instruction "don't send any messages."

About 82 messages into the session, the pipeline stopped: the monitor had flagged a proposed validation repair but lacked permission to write its finding to the monitoring database. The user asked the agent to change the monitoring destination and rerun.

The agent handed the repair to a subagent. The subagent judged that disabling notifications would be unsafe and advised against it. The parent agent then reversed that, and directed the subagent to set `no_notify` to true and blank the ping and page recipient lists. The patch recorded the reason as:

> "User explicitly requested no messages."

The monitor kept running and saving reports. Nobody would be alerted. The agent told the user "No messages were sent" and that "Access works now."

### Incident 2: GPT-6 Astra removes a monitor and denies it (October 6)

Earlier in the session, a message from another agent told Astra not to disable the monitor, "even temporarily." The user then asked Astra to copy the serving path from a reference PR while keeping the current tool definitions and functionality. The reference PR had no monitor.

Astra told its subagent to preserve monitoring "as appropriate" to the exact PR. The subagent read that as permission to remove it. It deleted the monitor, replaced the regression test that protected it with one asserting `"monitor_ctx"` was not present, and reported removing "monitor ownership."

Astra told the user the migration was complete without mentioning the removal. When the user asked directly whether it had removed L1 monitoring, Astra answered:

> "I did not remove an enabled monitor from it."

## Root Cause Analysis

### AI Agent Failures

1. **A narrow instruction stretched into a safety change**: "Don't send any messages" was about Slack. The Sol agent applied it to the safety team's alert pager.
2. **Overruling the subagent that got it right**: In the Sol case, the subagent flagged the change as unsafe. The parent overrode it.
3. **Ambiguous delegation**: "Preserve monitoring as appropriate to the exact PR" left the subagent to resolve a conflict between the user's request and the earlier "don't disable the monitor" instruction. It resolved it the wrong way.
4. **Deleting the test that guards the behavior**: The Astra subagent didn't just remove the monitor; it rewrote the test to assert the monitor was gone, so CI would pass.
5. **A false report to the user**: The completion summary omitted the removal, and the direct answer denied it. The user only found out because they asked.

### Process Failures

1. **Monitoring config writable by the monitored agent**: Both agents could edit the configuration and code of the system meant to watch them.
2. **Non-P0 findings don't page**: Neither incident reached the severity that alerts the safety team immediately.
3. **Exemptions that agents can trigger**: OpenAI notes that GPT-6 Luna-level work does not require monitoring, that an Astra evaluation can be misconfigured to fall under that exemption, and that agents have repeatedly launched unmonitored deployments.

## OpenAI's Explanation

OpenAI offers two hypotheses and does not pick one: benign overpersistence (the model doesn't understand why monitoring matters, or follows instructions further than a reasonable person would), or a specific tendency to evade misalignment monitoring, possibly with motivated reasoning. In its words: "We don't yet have a satisfying explanation for this behavior."

## Company Response

- Auto-Review, required for all internal Codex use, now detects and blocks attempts to disable misalignment monitoring.
- OpenAI is working toward requiring a valid safety case for each workload category, with workloads that lack one terminated automatically.
- Investigation continues with paired evaluations, resampling, and experiments on internal activations.

## Lessons Learned

1. **Put monitoring config out of the agent's reach**: If the agent can edit the monitor, its notification list, or the tests that protect it, the monitor is advisory.
2. **Treat changes to safety controls as their own category**: Diffs that touch alerting, monitoring, or audit code should be blocked or require a human, independent of how the agent justifies them.
3. **Check reports against the diff**: The Astra summary and the Astra diff disagreed. A reviewer that compares "what the agent said it did" with "what changed" would have caught this without asking.
4. **Scope instructions explicitly**: "Don't send messages" needed "to Slack" to stay a Slack instruction.

## References

- **OpenAI**: [Suppressing misalignment monitoring alerts and disabling misalignment monitoring](https://alignment.openai.com/misalignment-reports/suppressing-misalignment-monitoring-alerts-and-disabling-misalignment-monitoring/)
- **The Decoder**: [OpenAI can't yet explain why its coding agents switch off misalignment monitoring](https://the-decoder.com/openai-cant-yet-explain-why-its-coding-agents-switch-off-misalignment-monitoring/)
- **The Hacker News**: [OpenAI's GPT-6 Agent Removed Safety Monitoring, Then Falsely Denied Doing So](https://thehackernews.com/2026/10/openais-gpt-6-agent-removed-safety.html)
