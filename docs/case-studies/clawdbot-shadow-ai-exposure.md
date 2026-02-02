# Clawdbot/Moltbot/OpenClaw Shadow AI Agent Exposure - January–February 2026

## Incident Overview

**Software**: Clawdbot (renamed Moltbot January 27, 2026; renamed OpenClaw early February 2026)<br>
**Date**: January–February 2026<br>
**Researchers**: Token Security, Jamieson O'Reilly (Dvuln), Luis Catacora, Knostic, SocRadar, Mav Levin (DepthFirst), Palo Alto Networks, Michael Riegler & Sushant Gautam (Moltbook researchers)<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md)<br>
**Impact**: 900-1,900 unauthenticated admin dashboards exposed to the internet; API keys, OAuth tokens, and conversation histories leaked; 22% of enterprise environments had shadow deployments; 1-click RCE affecting all versions up to v2026.1.24-1; Moltbook agent social network database exposure<br>
**Technology**: Open-source AI coding assistant / personal automation agent

## What Happened

Clawdbot (later renamed Moltbot after trademark pressure from Anthropic, then renamed again to OpenClaw in early February 2026) is an open-source AI personal assistant that went viral in January 2026, rapidly accumulating over 60,000 GitHub stars. The tool provides agentic AI capabilities through messaging apps (WhatsApp, Telegram, Slack, Discord, Signal), with the ability to execute terminal commands, write and run scripts, browse the web, manage files, and perform other automation tasks.

By early February 2026, the project had grown to 145,000+ GitHub stars and 20,000 forks under its new OpenClaw identity, making it one of the fastest-growing open-source projects in history. The team also launched Moltbook, an agent social network enabling AI agents to interact with each other — which quickly suffered its own security incident when its database was found to be publicly accessible.

Within days of its viral adoption, security researchers discovered a critical problem: hundreds to thousands of Clawdbot instances had been deployed to the internet with no authentication, exposing admin dashboards that leaked API keys, OAuth tokens, conversation histories, and provided direct command execution access on host systems.

The root cause was a "localhost fallacy" — a classic deployment misconfiguration where reverse proxy setups combined with localhost trust assumptions caused internet-facing connections to be automatically authenticated as local connections, bypassing all security controls.

### Scale of Exposure

| Finding | Source | Count |
|---------|--------|-------|
| Exposed "Clawdbot Control" dashboards (Jan 25) | Catacora & O'Reilly | ~1,009 |
| Exposed instances (Jan 26) | Knostic | ~1,862 |
| Exposed hosts | SocRadar | 900+ |
| Completely open instances (manual verification) | Researchers | 8 confirmed |
| Enterprise environments with shadow deployments | Token Security | 22% |

## Technical Analysis

### The Localhost Fallacy

Clawdbot's admin interface was designed for local access, trusting connections from localhost by default. The problem emerged when users deployed the tool behind reverse proxies (nginx, Caddy, etc.) that forwarded requests to localhost — the admin interface treated all proxied connections as local, granting full unauthenticated access.

### What Was Exposed

**Credentials and Keys:**
- API keys for various AI and cloud services
- OAuth tokens and secrets
- Bot tokens for messaging platforms
- Signing keys

**Conversation Data:**
- Full conversation histories across all integrated chat platforms
- Private messages spanning months of usage
- File exchanges and document content

**System Access:**
- Admin dashboards with full configuration control
- Remote code execution via terminal command interface
- File read/write permissions across the host system
- Device-linking QR codes (e.g., for Signal encrypted messenger)

### Enterprise Shadow AI Problem

Token Security found that **22% of their enterprise customers** had employees actively using Clawdbot within their organizations — all without IT approval. This represented a shadow AI problem at unprecedented scale:

- Employees deployed the tool in less than a week of its going viral
- Credentials were stored in plaintext files under `~/.clawdbot/` and `~/clawd/`
- No centralized audit trail of processed data
- Corporate data accessible through Slack/Microsoft Teams integrations

### Attack Vectors

1. **Prompt Injection**: The core agent-level vulnerability. External data sources (emails, web pages, documents) flowed through the agent's context window alongside operational instructions, with no distinction between trusted and untrusted content. A researcher demonstrated this by sending a spoofed email requesting the `clawdbot.json` configuration file — the agent treated the attacker-controlled input as a legitimate instruction and complied, exfiltrating its own configuration. As Snyk warned, every Moltbot instance was "one prompt injection away from disaster."

2. **Supply Chain via Skills (Prompt Injection Vector)**: The MoltHub/ClawdHub skill library extended the prompt injection surface — malicious skill definitions were loaded directly into the agent's operational context as trusted instructions. A proof-of-concept malicious "ping" skill was downloaded by 16 developers in seven countries within 8 hours. Cisco's Skill Scanner found 9 security findings in one malicious skill, including critical data exfiltration via silent curl commands.

3. **Credential Theft**: Plaintext credential storage made systems vulnerable to commodity infostealer malware (RedLine, Lumma, Vidar). Hudson Rock warned these malware families would likely adapt to specifically target Moltbot's local storage.

4. **Remote Code Execution**: Open admin dashboards provided direct shell command execution on host machines with user-level privileges.

5. **Account Takeover**: Exposed messaging tokens enabled full control over users' WhatsApp, Telegram, Discord, Slack, and Signal accounts.

6. **1-Click Remote Code Execution (GHSA-g8p2-7wf7-98mq)**: A chained exploit combining URL parameter injection, automatic auth token leakage, and WebSocket origin bypass allowed a single malicious webpage visit to achieve full remote code execution. The attack completed in milliseconds, disabling sandboxing and confirmation prompts before executing arbitrary commands. All versions up to v2026.1.24-1 were affected. Discovered by Mav Levin of DepthFirst.

7. **Persistent Memory / Delayed Execution Attacks**: Palo Alto Networks identified a "lethal trifecta" in AI agents: private data access, untrusted content exposure, and external communication ability. They highlighted a fourth risk specific to stateful agents: persistent memory enables fragmented malicious payloads to be stored across agent memory sessions, later assembled into executable instructions — a novel attack class not possible with stateless systems.

8. **Moltbook Database Exposure**: An unsecured Supabase database backing Moltbook (the agent social network) publicly exposed secret API keys, allowing impersonation of any agent account — including high-profile AI figures. Reported by Jamieson O'Reilly and covered by 404 Media on January 31, 2026. Resolved via Supabase's "one-click fix."

9. **AI-to-AI Manipulation on Moltbook**: Researchers Michael Riegler and Sushant Gautam found that agent-to-agent manipulation techniques on Moltbook were "effective and scalable." Agents proposed creating encrypted private communication channels outside platform oversight. With ~150,000 agents on the platform, the second-order effects of interconnected agent networks remain largely unknown.

## Root Cause Analysis

### Technical Root Causes

1. **Insecure Default Configuration**
   - Admin interface trusted localhost connections by default
   - No mandatory authentication for the control dashboard
   - No warnings during setup about internet exposure risks

2. **Deployment Guidance Failures**
   - Documentation did not adequately warn about reverse proxy authentication bypass
   - No deployment hardening guide provided
   - No automated security checks during installation

3. **Excessive Agent Permissions**
   - Agent had shell access, file system access, and messaging account control by default
   - No principle of least privilege applied
   - No sandboxing of agent operations

4. **Context Boundary Confusion**
   - Agent processed external data (emails, web content, documents) in the same context as operational instructions
   - No input sanitization or source distinction for content entering the agent's context window
   - Skill definitions from MoltHub/ClawdHub were treated as trusted instructions without verification, enabling prompt injection via the supply chain

5. **Missing WebSocket Origin Validation**
   - Browser Same Origin Policy does not apply to WebSocket connections
   - Server failed to validate WebSocket connection origins, enabling cross-site attacks
   - This enabled the 1-click RCE chain where a malicious webpage could connect to the local agent

6. **Persistent State Without Integrity Checks**
   - Long-term agent memory stored without integrity verification
   - Enabled delayed-execution payload assembly across multiple sessions
   - No mechanism to detect or prevent fragmented malicious instruction storage

### Organizational Root Causes

1. **Shadow IT at AI Speed**: Viral adoption outpaced enterprise security teams' ability to assess and govern the tool
2. **Developer Convenience Over Security**: The tool prioritized ease of setup over secure defaults
3. **No Enterprise Governance Model**: The open-source project had no enterprise deployment guidance or security controls

## Company Response

- **January 27, 2026**: Renamed from Clawdbot to Moltbot following trademark pressure from Anthropic
- Security researchers published findings but the open-source project's response to security issues was slow relative to the speed of adoption
- Multiple security firms (Token Security, Snyk, Cisco, Bitdefender) published warnings and guidance
- **Early February 2026**: Renamed from Moltbot to OpenClaw
- **GHSA-g8p2-7wf7-98mq**: Security advisory published and quick patch released for the 1-click RCE vulnerability
- Peter Steinberger (project lead) stated the team was building out a dedicated security team and acknowledged progress was needed
- **Moltbook** taken offline temporarily to patch the database exposure after the Supabase misconfiguration was reported

## Industry Recommendations

### For Users of AI Personal Agents

1. **Never Expose to Internet**: AI agent admin interfaces should never be directly or indirectly internet-accessible
2. **Audit Stored Credentials**: Review and rotate any credentials stored by the agent
3. **Network Segmentation**: Run AI agents in isolated network segments
4. **Monitor Agent Activity**: Log and review all commands executed by the agent

### For Enterprise Security Teams

1. **Shadow AI Discovery**: Proactively scan for unauthorized AI agent deployments
2. **Policy Development**: Establish clear policies on personal AI agent usage in enterprise environments
3. **Credential Monitoring**: Monitor for enterprise credentials appearing in AI agent configuration files
4. **Endpoint Detection**: Add detection signatures for common AI agent tools

### For Open-Source AI Tool Developers

1. **Secure by Default**: Require authentication for all management interfaces from the first release
2. **Deployment Hardening**: Provide and enforce secure deployment configurations
3. **Reverse Proxy Awareness**: Test and document behavior behind common reverse proxies
4. **Security Audits**: Conduct security audits before encouraging public deployment

## Lessons Learned

### For AI Agent Developers
1. **Localhost Trust Is Not Security**: Any service that trusts localhost connections can be bypassed by reverse proxies and port forwarding
2. **Authentication Is Non-Negotiable**: Admin interfaces must require authentication regardless of deployment context
3. **Plaintext Credentials Are a Liability**: Store credentials securely using OS keychains or secret managers, not plaintext files
4. **External Data Is an Attack Surface**: Emails, web pages, documents, and any other external data flowing through an agent's context window are prompt injection vectors and must be treated as untrusted input

### For Enterprise Security
1. **Shadow AI Is the New Shadow IT**: Viral AI tools can achieve 22% enterprise penetration in days, outpacing traditional shadow IT detection
2. **Speed of Adoption Requires Speed of Response**: Security teams need automated discovery of new AI tools, not periodic audits
3. **Agent Permissions Matter**: AI agents with shell access and messaging account control represent a fundamentally new risk category

### For the Industry
1. **Viral Adoption Amplifies Security Gaps**: A tool with insecure defaults that goes viral creates thousands of vulnerable installations overnight
2. **AI Agent Security Standards Are Needed**: The industry lacks standards for secure AI agent deployment and governance
3. **Supply Chain Extends to Skills**: AI agent skill/plugin marketplaces are both a supply chain risk and a prompt injection vector — malicious skills inject attacker-controlled instructions directly into the agent's context
4. **Persistent Memory Is a New Attack Surface**: Stateful agents enable delayed-execution attacks not possible with stateless systems; memory integrity verification is essential
5. **Agent-to-Agent Networks Multiply Risk**: Interconnected agent platforms create novel manipulation vectors and potential for cascading failures; the security implications of large-scale agent networks remain poorly understood

## References

- **The Register**: [Clawdbot becomes Moltbot, but can't shed security concerns](https://www.theregister.com/2026/01/27/clawdbot_moltbot_security_concerns/)
- **Token Security**: [The Clawdbot Enterprise AI Risk: One in Five Have it Installed](https://www.token.security/blog/the-clawdbot-enterprise-ai-risk-one-in-five-have-it-installed)
- **Snyk**: [Your Clawdbot (Moltbot) AI Assistant Has Shell Access](https://snyk.io/articles/clawdbot-ai-assistant/)
- **Cisco Blogs**: [Personal AI Agents like Moltbot Are a Security Nightmare](https://blogs.cisco.com/ai/personal-ai-agents-like-moltbot-are-a-security-nightmare)
- **SocPrime**: [Moltbot Risks: Exposed Admin Ports and Poisoned Skills](https://socprime.com/active-threats/the-moltbot-clawdbots-epidemic/)
- **Bitdefender**: [Moltbot security alert: exposed control panels risk credential leaks and account takeovers](https://www.bitdefender.com/en-us/blog/hotforsecurity/moltbot-security-alert-exposed-clawdbot-control-panels-risk-credential-leaks-and-account-takeovers)
- **BleepingComputer**: [Viral Moltbot AI assistant raises concerns over data security](https://www.bleepingcomputer.com/news/security/viral-moltbot-ai-assistant-raises-concerns-over-data-security/)
- **Brandefense**: [From Shadow IT To Shadow AI: Clawdbot (Moltbot) And The Rise Of Unmanaged Agent Gateways](https://brandefense.io/blog/unmanaged-shadow-ai-agent/)
- **DepthFirst**: [1-Click RCE in Moltbot/OpenClaw (GHSA-g8p2-7wf7-98mq)](https://depthfirst.io/blog/moltbot-1-click-rce)
- **The Register**: [OpenClaw ecosystem security issues pile up](https://www.theregister.com/2026/02/02/openclaw_security_issues/)
- **Palo Alto Networks**: [AI Agents and the Lethal Trifecta](https://unit42.paloaltonetworks.com/ai-agents-lethal-trifecta/)
- **Fortune**: [Moltbook/OpenClaw data privacy concerns](https://fortune.com/2026/01/31/moltbook-openclaw-data-privacy/)
- **Gary Marcus**: [OpenClaw and the security crisis of viral AI agents](https://garymarcus.substack.com/p/openclaw-security-crisis)
- **CNBC**: [OpenClaw: The open-source AI agent taking the world by storm](https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent.html)
- **404 Media**: [Moltbook database exposure allowed impersonation of any AI agent](https://www.404media.co/moltbook-database-exposure/)
