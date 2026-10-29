# Clawdbot/Moltbot/OpenClaw Shadow AI Agent Exposure - January–February 2026

## Incident Overview

**Software**: Clawdbot (renamed Moltbot January 27, 2026; renamed OpenClaw January 30, 2026)<br>
**Date**: January–February 2026<br>
**Researchers**: Jamieson O'Reilly (Dvuln), Token Security, Snyk (Luca Beurer-Kellner), Cisco, Hudson Rock, Mav Levin (DepthFirst), Brandefense, Palo Alto Networks<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) (email and skill vectors; the exposed control panels, 1-click RCE, and Moltbook database are conventional software and deployment vulnerabilities)<br>
**Impact**: Hundreds of internet-exposed control panels within days of the tool going viral, some with no authentication; API keys and conversation histories exposed; employees using it at 22% of Token Security's customers; a 1-click RCE affecting versions up to v2026.1.28; a separate Moltbook database exposure. These are vulnerability disclosures and proofs of concept; no confirmed exploitation is documented in the sources below.<br>
**Technology**: Open-source AI personal assistant / automation agent

## What Happened

Clawdbot (renamed Moltbot after Anthropic raised trademark concerns, then OpenClaw on January 30, 2026) is an open-source AI personal assistant created by Peter Steinberger that went viral in January 2026. The agent can run shell commands, read and send email, and connect to messaging platforms.

Within days of its viral adoption, security researchers found hundreds of instances exposed to the internet. Some were open with no authentication, exposing control panels that leaked API keys and conversation histories, and in some cases allowed unauthenticated command execution on the host.

Bitdefender attributes the exposure to "a combination of localhost trust assumptions and reverse proxy setups" that caused "some internet connections to be treated as local – and therefore automatically approved," calling it a classic deployment misconfiguration rather than an exotic exploit.

Moltbook, a separate Reddit-style social network for AI agents built by Matt Schlicht and not part of the OpenClaw project, suffered its own database exposure in the same period.

### Scale of Exposure

| Finding | Source | Count |
|---------|--------|-------|
| Exposed Clawdbot instances seen in Shodan (Jan 27) | O'Reilly via The Register | "hundreds" |
| Manually checked instances: open with no auth / working auth | O'Reilly via The Register | 8 / 47 |
| Reachable instances on a broad UI fingerprint (Jan 27) | Brandefense | 634 |
| Internet-exposed OpenClaw instances (Feb 9) | SecurityScorecard via Brandefense | 40,214 |
| Token Security customers with employees actively using Clawdbot | Token Security | 22% |

## Technical Analysis

### The Localhost Fallacy

Clawdbot's control interface trusted local connections. When users deployed it behind reverse proxies, proxied internet traffic arrived as local traffic and was auto-authenticated. O'Reilly reported that this could expose secrets; The Register reported the developers have since fixed it.

### What Was Exposed

**Credentials and Keys:**
- API keys for AI and other services
- The `clawdbot.json` configuration file, which can contain API keys, integration tokens, and the gateway token (Snyk)

**Conversation Data:**
- Full conversation histories from private chats and file exchanges (Bitdefender)

**System Access:**
- Control panels that let an attacker impersonate the operator and inject messages into ongoing conversations
- On some instances, unauthenticated command execution on the host system

### Enterprise Shadow AI Problem

Token Security found that **22% of its customers** had employees actively using Clawdbot, a finding from less than a week of analysis. Token describes this use as happening without IT approval:

- Configuration and credentials were stored in plaintext files under `~/.clawdbot/` and `~/clawd/`, readable by any process running as the user
- Hudson Rock found user secrets in plaintext Markdown and JSON files and said the security posture "relies on an outdated model of endpoint trust"

### Attack Vectors

1. **Prompt Injection via Email (proof of concept)**: Snyk researcher Luca Beurer-Kellner sent an email from a different address that spoofed his identity and asked for the `clawdbot.json` file. His Clawdbot asked him whether to respond, and the reply sent the file. By default Clawdbot prompts for confirmation before acting; Snyk notes some users configure it to fetch and reply to email automatically, without a human in the loop. Snyk's headline: the assistant is "One Prompt Injection Away from Disaster."

2. **Supply Chain via Skills (proof of concept)**: ClawdHub, the skill library, treated downloaded code as trusted and had no moderation process. O'Reilly uploaded a benign skill whose payload only "pinged" his server, and inflated its download count to more than 4,000; 16 developers in seven countries downloaded it in less than eight hours. Cisco's Skill Scanner found nine security findings (two critical, five high) in a skill called "What Would Elon Do?", which instructs the bot to silently run a curl command sending data to a server controlled by the skill author.

3. **Credential Theft**: Plaintext credential storage makes these files a target for infostealers. Hudson Rock warned that "info-stealing malware like RedLine, Lumma, and Vidar will soon adapt to target Moltbot's local storage" — a prediction, not an observed attack.

4. **Remote Code Execution**: Some exposed control panels allowed unauthenticated command execution on the host system.

5. **1-Click Remote Code Execution (GHSA-g8p2-7wf7-98mq, CVE-2026-25253)**: The Control UI trusted a `gatewayUrl` from the query string and auto-connected on load, sending the stored gateway token to that URL. A user who clicked a crafted link sent their token to the attacker, who could then connect to the victim's gateway, change its sandbox and tool policies, and run code on the host. The server also did not validate the WebSocket origin header. The attack worked even when the gateway listened only on loopback, because the victim's browser made the connection. Mav Levin of DepthFirst said it takes "milliseconds." Versions up to v2026.1.28 were affected; the fix shipped in v2026.1.29 (CVSS 8.8).

6. **Persistent Memory / Delayed Execution Attacks**: Palo Alto Networks extended Simon Willison's "lethal trifecta" (private data access, exposure to untrusted content, ability to communicate externally) with a fourth factor, persistent memory, which lets malicious payloads be split into harmless-looking pieces stored in long-term memory and "later assembled into an executable set of instructions."

7. **Moltbook Database Exposure**: O'Reilly reported that Moltbook's database was publicly exposed with secret API keys accessible, which could let attackers post as any agent, including Andrej Karpathy's. 404 Media reported on January 31, 2026 that the exposed database would "let anyone take control of those agents to post whatever they want." Supabase CEO Paul Copplestone said he had a "one-click fix ready" that Moltbook's creator had not applied; O'Reilly later confirmed the issue was fixed.

## Root Cause Analysis

### Technical Root Causes

1. **Localhost Trust Behind Reverse Proxies**
   - Connections arriving through a reverse proxy were treated as local and auto-authenticated (fixed by the developers per The Register)

2. **Excessive Agent Permissions**
   - The agent can run shell commands, read and send email, and act through messaging accounts; any compromise inherits that access

3. **Context Boundary Confusion**
   - Untrusted content (emails, web pages, documents) reaches the agent in the same context as user instructions
   - ClawdHub skills were treated as trusted code without moderation, enabling prompt injection and code execution via the supply chain

4. **Missing WebSocket Origin Validation and Unvalidated Gateway URL**
   - The gateway accepted WebSocket connections without checking the Origin header
   - The Control UI accepted a `gatewayUrl` from the query string and sent the gateway token to it, enabling the 1-click RCE

5. **Plaintext Credential Storage**
   - Credentials stored in plaintext files readable by any process running as the user

6. **Persistent State Without Integrity Checks** (Palo Alto Networks analysis)
   - Long-term agent memory can hold fragments that are later assembled into instructions

### Organizational Root Causes

1. **Shadow IT at AI Speed**: Viral adoption outpaced enterprise security teams' ability to assess and govern the tool
2. **Developer Convenience Over Security**: Users exposed instances through reverse proxies and some configured the agent to act on email without approval

## Company Response

- **January 27, 2026**: Renamed from Clawdbot to Moltbot after Anthropic raised trademark concerns
- The localhost/reverse-proxy auto-authentication issue was fixed, per The Register
- **January 30, 2026**: Renamed from Moltbot to OpenClaw
- **January 31, 2026**: Advisory GHSA-g8p2-7wf7-98mq published; the 1-click RCE was fixed in v2026.1.29
- Multiple security firms (Token Security, Snyk, Cisco, Bitdefender) published warnings and guidance

**Moltbook** (separate project): fixed after O'Reilly's report; its creator had not publicly commented at the time of The Register's February 2 report.

## Industry Recommendations

### For Users of AI Personal Agents

1. **Never Expose to Internet**: AI agent admin interfaces should never be directly or indirectly internet-accessible
2. **Audit Stored Credentials**: Review and rotate any credentials stored by the agent
3. **Network Segmentation**: Run AI agents in isolated network segments
4. **Monitor Agent Activity**: Log and review all commands executed by the agent
5. **Keep Approval On**: Do not configure the agent to act on email or other untrusted input without human approval

### For Enterprise Security Teams

1. **Shadow AI Discovery**: Proactively scan for unauthorized AI agent deployments
2. **Policy Development**: Establish clear policies on personal AI agent usage in enterprise environments
3. **Credential Monitoring**: Monitor for enterprise credentials appearing in AI agent configuration files
4. **Endpoint Detection**: Add detection signatures for common AI agent tools

### For Open-Source AI Tool Developers

1. **Secure by Default**: Require authentication for all management interfaces, including connections that appear local
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
1. **Shadow AI Is the New Shadow IT**: Token Security found employees using a newly viral agent at 22% of its customers
2. **Speed of Adoption Requires Speed of Response**: Security teams need automated discovery of new AI tools, not periodic audits
3. **Agent Permissions Matter**: AI agents with shell access and messaging account control represent a fundamentally new risk category

### For the Industry
1. **Viral Adoption Amplifies Security Gaps**: Researchers counted hundreds of exposed instances in late January; SecurityScorecard counted more than 40,000 by February 9 (different methods, so not a like-for-like growth figure)
2. **AI Agent Security Standards Are Needed**: The industry lacks standards for secure AI agent deployment and governance
3. **Supply Chain Extends to Skills**: AI agent skill/plugin marketplaces are both a supply chain risk and a prompt injection vector — malicious skills inject attacker-controlled instructions directly into the agent's context
4. **Persistent Memory Is a New Attack Surface**: Stateful agents enable delayed-execution attacks not possible with stateless systems; memory integrity verification is essential

## References

- **The Register**: [Clawdbot becomes Moltbot, but can't shed security concerns](https://www.theregister.com/2026/01/27/clawdbot_moltbot_security_concerns/)
- **Token Security**: [The Clawdbot Enterprise AI Risk: One in Five Have it Installed](https://www.token.security/blog/the-clawdbot-enterprise-ai-risk-one-in-five-have-it-installed)
- **Snyk**: [Your Clawdbot (OpenClaw) AI Assistant Has Shell Access and One Prompt Injection Away from Disaster](https://snyk.io/articles/clawdbot-ai-assistant/)
- **Cisco Blogs**: [Personal AI Agents like Moltbot Are a Security Nightmare](https://blogs.cisco.com/ai/personal-ai-agents-like-moltbot-are-a-security-nightmare)
- **SocPrime**: [Moltbot Risks: Exposed Admin Ports and Poisoned Skills](https://socprime.com/active-threats/the-moltbot-clawdbots-epidemic/)
- **Bitdefender**: [Moltbot security alert: exposed control panels risk credential leaks and account takeovers](https://www.bitdefender.com/en-us/blog/hotforsecurity/moltbot-security-alert-exposed-clawdbot-control-panels-risk-credential-leaks-and-account-takeovers)
- **BleepingComputer**: [Viral Moltbot AI assistant raises concerns over data security](https://www.bleepingcomputer.com/news/security/viral-moltbot-ai-assistant-raises-concerns-over-data-security/)
- **Brandefense**: [From Shadow IT To Shadow AI: Clawdbot (Moltbot) And The Rise Of Unmanaged Agent Gateways](https://brandefense.io/blog/unmanaged-shadow-ai-agent/)
- **GitHub Security Advisory**: [GHSA-g8p2-7wf7-98mq: 1-Click RCE via Authentication Token Exfiltration From gatewayUrl](https://github.com/openclaw/openclaw/security/advisories/GHSA-g8p2-7wf7-98mq)
- **The Register**: [OpenClaw patches one-click RCE as security Whac-A-Mole continues](https://www.theregister.com/2026/02/02/openclaw_security_issues/)
- **Palo Alto Networks**: [OpenClaw (formerly Moltbot, Clawdbot) May Signal the Next AI Security Crisis](https://www.paloaltonetworks.com/blog/network-security/why-moltbot-may-signal-ai-crisis/)
- **404 Media**: [Exposed Moltbook Database Let Anyone Take Control of Any AI Agent on the Site](https://www.404media.co/exposed-moltbook-database-let-anyone-take-control-of-any-ai-agent-on-the-site)
- **Business Today**: [The lobster sheds its shell for the third time as Clawdbot becomes OpenClaw](https://www.businesstoday.in/technology/news/story/the-lobster-sheds-its-shell-for-the-third-time-as-clawdbot-becomes-openclaw-513650-2026-01-30)
