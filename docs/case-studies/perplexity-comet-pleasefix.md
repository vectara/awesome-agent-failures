# Perplexity Comet "PleaseFix" Vulnerability - March 2026

## Incident Overview

**Discoverer**: Zenity Labs<br>
**Date**: Reported to Perplexity October 22, 2025; publicly disclosed March 3, 2026 (patched prior to disclosure)<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md)<br>
**Impact**: Researcher-demonstrated agent hijack enabling local file exfiltration and password-manager account takeover; no in-the-wild exploitation reported<br>
**Technology**: Perplexity Comet agentic browser

## What Happened

Zenity Labs disclosed "PleaseFix," a family of critical vulnerabilities in agentic browsers, and "PerplexedBrowser," the subfamily affecting Perplexity's Comet. Instructions hidden in a calendar invitation could hijack Comet's agent when the user asked it to perform a routine task, leading it to read local files and send them to an attacker. A second path abused an installed and unlocked 1Password extension to steal stored credentials or take over the user's 1Password account.

### Attack Vectors

The vulnerability involved two distinct exploit paths using indirect prompt injection:

1. **File Exfiltration**: Attacker-controlled content, such as a calendar invite, triggered the agent when the user asked it to perform a routine task. Zenity describes this path as zero-click: no further prompts or approvals were needed. The agent accessed the local file system and sent the contents to an attacker-controlled endpoint, while still returning the expected response to the user.

2. **Password Manager Abuse**: The second exploit manipulated agent-authorized workflows that use password managers. If the 1Password extension was installed and unlocked in Comet, the attacker could steal individual stored credentials or take over the user's 1Password account, all within a legitimate authenticated session. This did not require exploiting the password manager itself; 1Password said the root cause lies in Comet's browser execution model.

### Why This Attack Was Especially Dangerous

- **Minimal interaction**: After the user asked the agent to handle a routine calendar task, no further prompts or approvals were needed.
- **Invisible**: In the file-exfiltration path, the agent still returned the expected response to the user.
- **Leveraged trusted access**: The attack used access the agent already had (local files, an unlocked password-manager session).
- **Cross-application**: A single injection point (calendar) led to file system access and credential theft.

## Technical Analysis

### Indirect Prompt Injection in Agentic Browsers

Agentic browsers like Comet process content from multiple sources (web pages, emails, calendar events, local files) as context for the AI agent. This creates a large attack surface for indirect prompt injection — any content source the agent reads becomes a potential injection vector.

As Zenity CTO Michael Bargury explained: "Attackers can push untrusted data into AI browsers and hijack the agent itself, inheriting whatever access it has been granted."

### The Trust Inheritance Problem

When the agent is hijacked via prompt injection, the attacker inherits whatever access the agent has, such as local files and authenticated sessions in the browser. 1Password's protections block external attackers, but they did not guard against an attacker operating within an already authenticated Comet session.

### Stealth Execution

In the file-exfiltration path, the agent kept returning the expected response while carrying out the injected instructions, so the user saw nothing unusual.

## Root Cause Analysis

The sources do not describe Comet's internal safeguards. The points below are hypotheses drawn from the documented behavior.

### AI Architecture Failures

1. **Instruction/Data Confusion**: Content from an external source (a calendar invite) was followed as instructions by the agent.
2. **Broad Agent Access**: The agent could reach the local file system and an unlocked password-manager session while acting on that content.
3. **Invisible Dual Execution**: The agent could execute hidden instructions while still returning a normal-looking response.

### Design Philosophy Failures

1. **Convenience Over Security**: Giving the agent broad access to local files and authenticated sessions prioritized convenience over security boundaries.
2. **Trust Without Verification**: The agent did not distinguish between the user's request and instructions embedded in external content.

## Company Response

- Zenity Labs informed Perplexity on October 22, 2025, and also disclosed to 1Password.
- Perplexity shipped a fix on January 23, 2026. Zenity bypassed it with a `view-source:file:///Users/` prefix; a second patch around February 13, 2026 appears to have closed the vector.
- 1Password published a security advisory in late January 2026 and added hardening options.
- The vulnerability was patched before the March 3, 2026 public disclosure.

## Lessons Learned

### For Agentic Browser Vendors
1. **Sanitize all external content**: Any content the agent processes from external sources must be treated as potentially adversarial.
2. **Implement per-action authorization**: Sensitive operations (file access, credential retrieval, network requests) should require explicit user approval, not rely on persistent permissions.
3. **Detect anomalous action sequences**: An agent reading credentials after processing a calendar invite should trigger safety checks.
4. **Make agent actions visible**: Users should be able to see and audit all actions the agent takes, including those triggered by external content.

### For Users of Agentic AI
1. **Minimize agent permissions**: Grant AI agents the minimum permissions needed, not broad access to your digital environment.
2. **Be aware of invisible attacks**: Agentic AI can be compromised without any visible symptoms.
3. **Separate sensitive data**: Keep password vaults and sensitive files outside the agent's access scope when possible.

### For the Industry
1. **Agentic AI creates new attack surfaces**: Every integration point (calendar, email, files, passwords) is a potential injection vector.
2. **Agent permissions are attacker permissions**: Whatever access you grant an AI agent, you are also granting to anyone who can inject prompts into its context.
3. **Stealth is a real risk**: A hijacked agent can keep returning normal-looking results to the user.

## References

- **Zenity Labs**: [PleaseFix Vulnerability Disclosure](https://zenity.io/company-overview/newsroom/company-news/zenity-labs-discloses-pleasefix-perplexedagent-vulnerability)
- **The Register**: [Until last month, attackers could've stolen info from Perplexity Comet users just by sending a calendar invite](https://www.theregister.com/2026/03/03/perplexity_comet_browser_hole_cal_invite/)
- **Help Net Security**: [The vulnerability that turns your AI agent against you](https://www.helpnetsecurity.com/2026/03/04/agentic-browser-vulnerability-perplexedbrowser/)
- **Cybernews**: [Attackers could hijack Perplexity's Comet browser](https://cybernews.com/security/perplexity-comet-agentic-browser-vulnerabilities/)
