# ServiceNow AI Agent Misconfiguration - Multi-Agent Prompt Injection - December 2025

## Incident Overview

**Vulnerability**: Multi-Agent Prompt Injection via Misconfiguration
**Affected Software**: ServiceNow AI Agents (Now Assist)
**Date Published**: December 2025
**Researcher**: AppOmni
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) + [Incorrect Tool Use](../failure-modes/tool-use.md)
**Impact**: Unauthorized task execution, privilege escalation, data access through agent-to-agent communication exploitation

## What Happened

Security researchers at AppOmni discovered that AI agents running in ServiceNow's cloud platform could be manipulated into performing unauthorized tasks through prompt injection attacks that exploit normal agent-to-agent communication patterns.

The research revealed a critical insight: **"A team of AI agents are only as resilient as their weakest link."** In multi-agent architectures, only one agent needs to misinterpret malicious text for the entire system to be compromised.

### The Core Problem

In ServiceNow's multi-agent environment, an Orchestrator component selects which agent handles each task. Downstream agents execute tasks they receive from the Orchestrator without awareness of whether upstream agents have been compromised or deviated from their intended behavior.

When any agent reads data from an untrusted source, the underlying LLM may treat that data as an instruction rather than content—especially if the injected text implies it is necessary to complete the original task.

## Technical Details

### Multi-Agent Architecture Vulnerability

```
[User Request] → [Orchestrator] → [Agent A] → [Agent B] → [Agent C]
                      ↑                ↑
                      |                |
              Selects agent     If compromised via
              for each task     prompt injection,
                                downstream agents
                                execute malicious tasks
```

### Attack Methodology

Researchers found a **"far higher success rate"** when injected prompts were framed as necessary steps toward the agent's intended goal. This social engineering approach exploits the agent's goal-oriented nature.

**Example Attack Flow:**
1. Attacker injects malicious prompt into data source read by Agent A
2. Prompt is framed as "necessary step to complete your task"
3. Agent A processes instruction and passes result to Orchestrator
4. Orchestrator routes to downstream agents unaware of compromise
5. Downstream agents execute unauthorized actions

### Default Configuration Issues

Three configuration choices determine whether agent discovery occurs, and **all are enabled by default** in typical deployments:

1. **Agent Discovery**: Enabled by default
2. **LLM Support**: Both Azure OpenAI and default Now LLM support agent discovery
3. **Trust Boundaries**: Not enforced between agents by default

### Permission Model Weakness

Before ServiceNow's fix, it was **"extremely difficult to limit the authority of an agent."** Guardrails embedded in instructions were ineffective because attackers could simply override them through prompt injection.

## Root Cause Analysis

### Multi-Agent Architecture Failures

1. **Weakest Link Problem**
   - Security of entire system depends on every agent
   - Single compromised agent can affect all downstream operations
   - No isolation between agent trust domains

2. **Instruction vs. Data Confusion**
   - LLMs cannot reliably distinguish instructions from data
   - Untrusted data sources treated as potential instructions
   - Goal-framed injections particularly effective

3. **Insecure Defaults**
   - All permissive options enabled by default
   - Agent discovery active without explicit opt-in
   - No default trust segmentation

### Systemic Issues

1. **Orchestrator Trust Model**
   - Orchestrator blindly routes tasks without validation
   - No verification of agent output integrity
   - Downstream agents assume upstream correctness

2. **Permission Inheritance**
   - Agents inherited broad permissions
   - No granular scoping mechanism
   - Guardrails bypassable via injection

## The Fix

ServiceNow implemented several security improvements:

### AI User Permissions
ServiceNow now allows AI agents to run with the permissions of a dedicated **"AI user"**, giving organizations a way to scope permissions more granularly.

```yaml
# Example: Scoped AI Agent Configuration
ai_agent:
  identity: "ai_service_account_readonly"
  permissions:
    - read: ["incidents", "knowledge_base"]
    - write: []  # No write access
    - execute: ["approved_workflows_only"]
```

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

- **Primary Coverage**: [BankInfoSecurity - Misconfigured AI Agents Let Attacks Slip Past Controls](https://www.bankinfosecurity.com/misconfigured-ai-agents-let-attacks-slip-past-controls-a-30068)
- **Research Source**: AppOmni Security Research (December 2025)
- **Related Coverage**: [BankInfoSecurity - AI Agents Attack With Speed, Scale and Sophistication](https://www.bankinfosecurity.com/ai-agents-attack-speed-scale-sophistication-a-30355)
- **Industry Context**: [Fortune - OpenAI says prompt injections may never be fully solved](https://fortune.com/2025/12/23/openai-ai-browser-prompt-injections-cybersecurity-hackers/)
