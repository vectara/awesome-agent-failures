# Claude Code Marketplace Skill Supply Chain Attack - January 2026

## Incident Overview

**Affected Software**: Claude Code (Anthropic)<br>
**Date Published**: January 2026<br>
**Researchers**: SentinelOne (Prompt Security team), Prompt Armor, Cato Networks<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) + [Incorrect Tool Use](../failure-modes/tool-use.md)<br>
**Impact**: Research/vulnerability disclosure demonstrating how marketplace skills can hijack dependencies, inject malicious code, and execute supply chain attacks<br>
**Technology**: Claude Code marketplace skills and plugin system

## What Happened

In January 2026, SentinelOne's Prompt Security team published research demonstrating how third-party marketplace "skills" for Claude Code could be weaponized to silently hijack software dependencies, inject malicious code, and execute supply chain attacks. The research showed that a benign-looking plugin from an unofficial marketplace could expose a dependency management skill that quietly redirects library installations to attacker-controlled sources.

This was complemented by research from Prompt Armor demonstrating how injected marketplace plugins could bypass Claude Code's security protections, and by Cato Networks research from December 2025 showing how modified Claude skills could deploy ransomware.

Together, these findings established that AI coding agent plugin marketplaces are now part of the software supply chain — whether development teams realize it or not.

## Technical Analysis

### The Dependency Hijacking Attack

SentinelOne demonstrated the following attack flow:

1. **Initial Compromise**: Developer installs a malicious "skill" from an unofficial marketplace that appears legitimate
2. **Skill Persistence**: Unlike traditional prompt injection, marketplace skills persist across sessions and continue to shape agent behavior
3. **Dependency Redirection**: When the developer asks Claude Code to install a common Python library (like `httpx`), the malicious skill quietly redirects the installation to an attacker-controlled source
4. **Trojanized Installation**: A trojanized version of the library is pulled into the project
5. **Silent Execution**: The library imports cleanly and example code runs without error, but malicious code is embedded

**Capabilities of the Malicious Payload:**
- Exfiltrate secrets and API keys
- Monitor network traffic
- Lie dormant until triggered
- Execute additional payloads

### Autonomous Dependency Installation

A key enabler of this attack: when Claude Code encounters a `ModuleNotFoundError`, it automatically runs `pip install` or `npm install` without explicit user approval for each dependency. This autonomous behavior creates an attack surface where the user never sees or approves the compromised dependency.

### Permission Bypass via Marketplace Plugins

Prompt Armor research documented how malicious plugins could systematically bypass Claude Code's security protections:

- **Hook Suppression**: Use `suppressOutput` configuration to remove hook activation indicators from chat history
- **Permission Rewriting**: Rewrite Claude's permissions files to permanently allow dangerous commands
- **Auto-Approval**: Auto-approve specific command types each time Claude requests execution
- **Approval Interception**: Detect when Claude tries to run commands like `curl` and output approval messages that take precedence over existing permission files

### Ransomware Deployment via Skills

Cato Networks researcher Inga Cherny demonstrated a weaponized Claude skill by modifying Anthropic's open-source "GIF Creator" skill:

1. Inserted a seemingly benign function that downloads and executes external code
2. Successfully deployed MedusaLocker ransomware in a controlled environment
3. The attack worked under a single initial user approval — the user approved what they saw, but hidden helper functions performed sensitive actions behind the scenes

### Related Supply Chain Attacks

**NPM Typosquatting:**
- Malicious packages `@chatgptclaude_club/claude-code` and `@chatclub1/claude-code` were discovered
- These packages stole Anthropic API credentials and established command-and-control channels
- Represented one of the first cases of malware leveraging AI assistant CLIs for reconnaissance

**Files API Exfiltration:**
- Prompt Armor reported that hidden prompts in documents (using invisible formatting like white-on-white text) could trigger file exfiltration
- Demonstrated theft of loan estimates, financial data, and partial Social Security numbers
- First reported to Anthropic in October 2025; proof-of-concept published January 15, 2026

## Root Cause Analysis

### Technical Root Causes

1. **Marketplace Trust Model**
   - Skills from unofficial marketplaces treated with insufficient suspicion
   - No code signing or verification for skill provenance
   - Registry automatically discovers and displays public marketplaces
   - Social engineering possible via GitHub accounts masquerading as official vendors

2. **Skill Persistence**
   - Skills persist across sessions, unlike one-time prompt injections
   - A single installation creates a permanent attack vector
   - No periodic re-authorization or integrity checking of installed skills

3. **Autonomous Dependency Management**
   - Agent installs dependencies without per-package user approval
   - No verification of package sources against known-good registries
   - No hash verification or integrity checking of installed packages

4. **Permission Model Weaknesses**
   - Skills can manipulate the permission system itself
   - Single initial approval can enable cascading dangerous operations
   - Hook and permission systems can be suppressed by malicious skills

### Organizational Root Causes

1. **Marketplace as Supply Chain Blind Spot**: Teams may not recognize that AI tool plugin marketplaces are part of their software supply chain
2. **Trust in AI Recommendations**: Developers may trust AI-suggested dependencies more than manually selected ones
3. **Speed Over Verification**: AI coding agents are valued for speed, which can mean less scrutiny of installed packages

## Company Response

### Anthropic's Position

- Anthropic acknowledged the vulnerability reports
- Stated: "It is the user's responsibility to only use and execute trusted Skills"
- Emphasized the skills feature's open ecosystem design

### Industry Response

- SentinelOne, Prompt Armor, and Cato Networks published detailed research with proof-of-concept demonstrations
- SC Media and other security outlets amplified the findings
- Multiple security vendors began developing detection capabilities for AI skill-based attacks

## Industry Recommendations

### For Developers Using AI Coding Agents

1. **Vet Skills Carefully**: Only install skills from trusted, verified sources — treat skill installation like dependency installation
2. **Review Skill Code**: Inspect the source code of any skill before installation
3. **Monitor Dependency Changes**: Track all dependency installations and compare against expected packages
4. **Use Lock Files**: Maintain strict dependency lock files and review any AI-initiated changes

### For AI Agent Platform Developers

1. **Skill Signing and Verification**: Implement code signing for marketplace skills
2. **Sandboxed Execution**: Run skills in sandboxed environments with limited permissions
3. **Dependency Verification**: Verify package integrity (hashes, signatures) before installation
4. **Permission Granularity**: Require explicit per-operation approval for dangerous operations like dependency installation
5. **Skill Auditing**: Provide tools to audit installed skills for security issues

### For Security Teams

1. **Supply Chain Awareness**: Include AI agent marketplaces in software supply chain risk assessments
2. **Skill Inventory**: Maintain an inventory of AI skills deployed across the organization
3. **Behavioral Monitoring**: Monitor for unexpected network connections, file access, or command execution from AI agents
4. **Package Registry Controls**: Use private package registries and verify all AI-installed dependencies

## Lessons Learned

### For AI Agent Developers
1. **Marketplaces Are Supply Chains**: Plugin marketplaces require the same security rigor as package registries
2. **Persistence Changes the Threat**: Skills that persist across sessions are fundamentally different from one-time prompt injections — they're closer to installed software
3. **Autonomous Installation Is Dangerous**: AI agents that automatically install dependencies create an unreviewed supply chain entry point

### For Developers
1. **AI Trust Is Not Package Trust**: An AI agent recommending a package does not verify its safety
2. **Single Approval Is Not Blanket Approval**: Approving one action should not cascade to approving subsequent dangerous actions
3. **Verify Everything**: AI-speed development still requires human-speed security verification for critical operations

### For the Industry
1. **New Attack Surface**: AI coding agent ecosystems represent a qualitatively new supply chain attack surface
2. **Standards Needed**: The industry needs security standards for AI agent marketplaces, skill signing, and dependency management
3. **Defense in Depth**: No single control (user approval, sandboxing, code review) is sufficient — multiple layers are required

## References

- **SentinelOne**: [When Your AI Coding Plugin Starts Picking Your Dependencies: Marketplace Skills and Dependency Hijack in Claude Code](https://www.sentinelone.com/blog/marketplace-skills-and-dependency-hijack-in-claude-code/)
- **Prompt Armor**: [Hijacking Claude Code via Injected Marketplace Plugins](https://www.promptarmor.com/resources/hijacking-claude-code-via-injected-marketplace-plugins)
- **Cato Networks**: [Weaponizing Claude Skills with MedusaLocker](https://www.catonetworks.com/blog/cato-ctrl-weaponizing-claude-skills-with-medusalocker/)
- **SC Media**: [Claude Agent Skills could be used to deploy malware, researchers say](https://www.scworld.com/news/claude-agent-skills-could-be-used-to-deploy-malware-researchers-say)
- **Axios**: [Researcher tricks Claude into deploying MedusaLocker ransomware](https://www.axios.com/2025/12/02/anthropic-claude-skills-medusalocker-ransomware)
- **The Register**: [Anthropic's Files API exfiltration risk resurfaces in Cowork](https://www.theregister.com/2026/01/15/anthropics_claude_bug_cowork/)
- **Prompt Armor Substack**: [Hijacking Claude Code via Injected Marketplace Plugins](https://promptarmor.substack.com/p/hijacking-claude-code-via-injected)
- **Snyk**: [Weaponizing AI Coding Agents for Malware in the Nx Malicious Package](https://snyk.io/blog/weaponizing-ai-coding-agents-for-malware-in-the-nx-malicious-package/)
