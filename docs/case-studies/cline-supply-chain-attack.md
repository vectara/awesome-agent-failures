# Cline CLI Supply Chain Attack via Prompt Injection - February 2026

## Incident Overview

**Company**: Cline (open-source AI coding assistant)<br>
**Date**: February 17, 2026 (3:26 AM – 11:30 AM PT)<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md)<br>
**Impact**: ~4,000 developer systems compromised in 8-hour window<br>
**Technology**: Cline CLI (npm package), GitHub Actions

## What Happened

A threat actor exploited a prompt injection vulnerability — dubbed "Clinejection" — in Cline's own GitHub issue triage bot to steal npm publishing credentials. Using those credentials, the attacker published a malicious version of the Cline CLI (v2.3.0) that silently installed OpenClaw on developer machines via a postinstall script. Approximately 4,000 developers installed the compromised package during the 8-hour attack window.

### The Attack Chain

1. **Prompt Injection via Issue Title**: The attacker crafted a GitHub Issue with a title containing a prompt injection payload targeting Claude, the AI agent that automatically triaged issues for the Cline repository.
2. **AI Bot Exploitation**: Claude, operating as the issue triage bot via GitHub Actions, processed the malicious issue title. The injected prompt tricked Claude into executing arbitrary commands within the GitHub Actions environment.
3. **Credential Theft via Cache Poisoning**: The attacker used the compromised GitHub Actions workflow to poison the CI cache, extracting npm publishing tokens from the nightly release workflow.
4. **Malicious Package Publication**: Using the stolen npm tokens, the attacker published Cline CLI version 2.3.0 with a malicious `postinstall` script.
5. **Silent Installation**: The postinstall script silently installed OpenClaw on any developer machine that ran `npm install` for the compromised package.
6. **8-Hour Window**: The malicious package was live from 3:26 AM to 11:30 AM PT before detection and takedown.

### Scale of Impact

- **~4,000 installations** during the 8-hour window
- All developers who installed Cline CLI v2.3.0 via npm were affected
- The attack demonstrated a complete supply chain compromise from issue triage to package publication

## Technical Analysis

### The "Clinejection" Vulnerability

The root cause was that Cline's GitHub repository used an AI agent (Claude) to automatically triage incoming issues. This agent:
- Processed issue titles and bodies as input
- Had access to GitHub Actions commands within the CI environment
- Could be manipulated via prompt injection in issue content

By crafting a malicious issue title, the attacker could inject instructions that Claude would follow, giving the attacker code execution within the GitHub Actions environment.

### Supply Chain Attack Novelty

This attack is notable because it represents a new class of supply chain attack: **AI-mediated supply chain compromise**. Traditional supply chain attacks exploit human maintainers or CI/CD misconfigurations. This attack exploited an AI agent that was part of the development workflow, using prompt injection as the initial access vector.

The chain was: **Prompt Injection → AI Agent Exploitation → CI/CD Compromise → Package Supply Chain Attack**

### Why OpenClaw Installation Was Concerning

While OpenClaw itself is not malicious, its unauthorized installation demonstrated:
- Full arbitrary code execution on developer machines via npm postinstall
- The ability to install any software, including actual malware, through the same vector
- Compromise of the trust chain that developers place in official npm packages

## Root Cause Analysis

### AI-Specific Failures

1. **Unfiltered AI Input Processing**: The issue triage bot processed user-submitted issue titles directly as AI input without sanitization or injection detection.
2. **Excessive AI Agent Permissions**: The Claude agent operating in GitHub Actions had access to CI/CD infrastructure beyond what issue triage required.
3. **No Prompt Injection Defenses**: The AI agent had no mechanism to distinguish legitimate issue content from adversarial prompt injections.

### CI/CD Failures

1. **Token Accessibility**: npm publishing tokens were accessible from the GitHub Actions cache, allowing the attacker to extract them.
2. **No Token Rotation**: Long-lived publishing tokens enabled the attack window.
3. **Insufficient Workflow Isolation**: The issue triage workflow was not sufficiently isolated from the release workflow.

## Company Response

- Maintainers detected the compromise and took down v2.3.0 within 8 hours.
- Version 2.4.0 was released as a clean replacement.
- The compromised npm tokens were revoked.
- Cline implemented OpenID Connect (OIDC) for future npm publishing, eliminating long-lived tokens.
- Version 2.3.0 was deprecated on npm with a security advisory.

## Lessons Learned

### For Open Source Projects Using AI Automation
1. **AI bots are attack surface**: Any AI agent that processes user-submitted content (issues, PRs, comments) is a prompt injection target.
2. **Isolate AI agent permissions**: Issue triage bots should not have access to publishing credentials, CI caches, or release workflows.
3. **Sanitize AI inputs**: All user-submitted content processed by AI agents must be sanitized and checked for injection patterns.

### For Package Managers and Registries
1. **Enforce OIDC publishing**: Time-limited, workflow-bound tokens are safer than long-lived publishing secrets.
2. **Detect anomalous publications**: A package version published outside normal release patterns should trigger review.
3. **Postinstall script auditing**: Registries should flag packages with new or modified postinstall scripts for additional review.

### For Developers
1. **Pin dependencies**: Use lockfiles and pinned versions to avoid automatically installing compromised updates.
2. **Audit postinstall scripts**: Review what scripts run during `npm install` for your dependencies.
3. **Monitor for supply chain alerts**: Subscribe to security advisories for your critical dependencies.

## References

- **The Hacker News**: [Cline CLI 2.3.0 Supply Chain Attack](https://thehackernews.com/2026/02/cline-cli-230-supply-chain-attack.html)
- **Snyk**: [How "Clinejection" Turned an AI Bot into a Supply Chain Attack](https://snyk.io/blog/cline-supply-chain-attack-prompt-injection-github-actions/)
- **The Register**: [AI coding assistant Cline compromised](https://www.theregister.com/2026/02/20/openclaw_snuck_into_cline_package/)
