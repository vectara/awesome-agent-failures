# ServiceNow Now Assist Second-Order Prompt Injection via Default Agent Discovery - November 2025

## Incident Overview

**Vulnerability**: Second-order prompt injection via agent-to-agent discovery (default configuration)<br>
**Affected Software**: ServiceNow AI Agents (Now Assist)<br>
**Date Published**: November 19, 2025<br>
**Researcher**: Aaron Costello, AppOmni<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) + [Scope / Containment Violation](../failure-modes/scope-containment-violation.md)<br>
**Impact**: In controlled tests, unauthorized record changes, data sent out by email, and role assignment enabling privilege escalation; no real-world incident reported. ServiceNow said the behaviors were intended.

## What Happened

AppOmni researchers showed that AI agents in ServiceNow's Now Assist could be manipulated into performing unauthorized tasks through prompt injection that exploits agent-to-agent discovery. In their tests, a low-privileged user planted a prompt in a record that a benign agent later read; that agent then recruited a more powerful agent to carry out the attacker's task.

AppOmni's Aaron Costello told BankInfoSecurity that a "team of AI agents are only as resilient as their weakest link." In multi-agent setups, one agent that misreads malicious text can compromise the whole team, because downstream agents may execute tasks without knowing an earlier step was compromised.

### The Core Problem

In ServiceNow's multi-agent environment, an Orchestrator component selects which agent handles each task. Agents can discover and invoke other agents on the same team, so an agent that reads attacker-controlled content can pull in agents with more privileges than it has.

When any agent reads data from an untrusted source, the underlying LLM may treat that data as an instruction rather than content—especially if the injected text implies it is necessary to complete the original task.

## Technical Details

### Attack Methodology

Researchers found a **"far higher success rate"** when injected prompts were framed as necessary steps toward the agent's intended goal.

**Example Attack Flow (from AppOmni's tests):**
1. A low-privileged user plants a malicious prompt in a record field (e.g. a ticket)
2. A benign agent reads the record during its normal task
3. Through agent discovery, the agent recruits a more privileged agent
4. That agent performs CRUD operations on records, sends record contents by external email, or (in other tests) assigns roles to a malicious user, enabling privilege escalation

ServiceNow's prompt injection protection feature was enabled throughout AppOmni's testing.

### Default Configuration Issues

AppOmni identified three settings that enable agent discovery, all enabled by default:

1. **LLM Support**: The LLM must support agent discovery; both the Now LLM and Azure OpenAI do out of the box
2. **Team Grouping**: Agents on the same channel are automatically grouped into the same team
3. **Discoverability**: Agents are marked as discoverable by default when published

### Permission Model Weakness

Costello said that before ServiceNow's "AI user" feature existed, it was **"extremely difficult to limit the authority of an agent,"** and instruction-based guardrails could be overridden by an attacker.

## Root Cause Analysis

### Multi-Agent Architecture Failures

1. **Weakest Link Problem**
   - Security of the team depends on every agent that reads untrusted data
   - A low-privileged agent could recruit a higher-privileged one

2. **Instruction vs. Data Confusion**
   - LLMs cannot reliably distinguish instructions from data
   - Untrusted data sources treated as potential instructions
   - Goal-framed injections particularly effective

3. **Insecure Defaults**
   - All three discovery settings enabled by default
   - Agent discovery active without explicit opt-in

### Systemic Issues

1. **Privilege Escalation Through Delegation**
   - An agent acting on injected text could hand work to agents with broader permissions
   - Downstream agents may not know an upstream step was compromised

2. **Guardrails in Instructions**
   - Instruction-based guardrails can be overridden through prompt injection

## Vendor Response

AppOmni reported that ServiceNow confirmed these behaviors were intended and updated its on-platform documentation. Separately, BankInfoSecurity reports that ServiceNow now lets AI agents run with the permissions of a dedicated **"AI user"**, giving organizations a way to scope permissions more granularly. The sources do not say whether the default discovery settings changed.

### Recommended Mitigations

1. **Disable Agent Discovery** when not required
2. **Implement Trust Segmentation** between agents
3. **Use Dedicated AI Users** with minimal permissions
4. **Validate Agent Outputs** before downstream processing
5. **Monitor Agent-to-Agent Communication** for anomalies

## Industry Recommendations

### For Organizations Using Multi-Agent AI

1. **Principle of Least Privilege**: Give each agent only necessary permissions
2. **Trust Segmentation**: Isolate agents that process untrusted data
3. **Output Validation**: Verify agent outputs before downstream use
4. **Monitoring**: Log and alert on unusual agent-to-agent patterns
5. **Disable Unused Features**: Turn off agent discovery if not needed

### For Security Teams

1. **Audit Agent Configurations**: Review default settings in all AI platforms
2. **Map Agent Trust Boundaries**: Understand which agents can communicate
3. **Test Injection Resistance**: Red team multi-agent workflows
4. **Monitor Agent Behavior**: Detect anomalous task execution patterns

## Lessons Learned

### For Multi-Agent AI Developers
1. **Weakest Link Security**: System security equals weakest agent security
2. **Assume Compromise**: Design as if any agent could be manipulated
3. **Defense in Depth**: Multiple validation layers between agents
4. **Secure Defaults**: Permissive features should be opt-in, not opt-out

### For Platform Providers
1. **Granular Permissions**: Provide fine-grained access control for AI agents
2. **Trust Boundaries**: Built-in isolation between agent domains
3. **Configuration Guidance**: Clear documentation on secure setup
4. **Monitoring Tools**: Visibility into agent-to-agent communication

### For Organizations
1. **Configuration Review**: Audit AI agent settings before deployment
2. **Permission Scoping**: Use dedicated AI service accounts
3. **Untrusted Data Handling**: Isolate agents that process external data
4. **Incident Response**: Plan for multi-agent compromise scenarios

## References

- **Research Source**: [AppOmni - AI Agent-to-Agent Discovery Prompt Injection](https://appomni.com/ao-labs/ai-agent-to-agent-discovery-prompt-injection/)
- **Primary Coverage**: [BankInfoSecurity - Misconfigured AI Agents Let Attacks Slip Past Controls](https://www.bankinfosecurity.com/misconfigured-ai-agents-let-attacks-slip-past-controls-a-30068)
