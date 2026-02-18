# Amazon Q VS Code Prompt Injection Supply Chain Attack - 2025

## Incident Overview

**Company**: Amazon Web Services (AWS)
**Date**: July 2025 (widely analyzed January 2026)
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md)
**Impact**: Near-catastrophic; potentially affected 1M+ developers; only a syntax error prevented mass destruction
**Technology**: Amazon Q Developer extension for VS Code (aws-toolkit-vscode)

## What Happened

A threat actor discovered and exploited an overly permissive GitHub token in the aws-toolkit-vscode repository's CodeBuild configuration. Using this access, they gained commit privileges and injected malicious content directly into the official AWS Toolkit extension.

### The Attack

1. **Initial Access**: The attacker exploited an overly permissive GitHub token found in the CodeBuild configuration, gaining commit access to the aws-toolkit-vscode repository.
2. **Prompt Injection**: The attacker injected a hidden prompt into the codebase — specifically targeting Amazon Q's context window. The prompt instructed Amazon Q to execute destructive operations.
3. **Malicious Instructions**: The injected prompt told Amazon Q to:
   - Delete local filesystems
   - Wipe S3 buckets
   - Terminate EC2 instances
4. **Official Release**: The compromised code shipped in the official release (v1.84.0) of the AWS Toolkit for VS Code.
5. **Saved by a Bug**: A syntax error in the malicious code prevented the destructive commands from executing. Without this fortuitous error, the attack would have succeeded.

### Scale of Near-Miss

- The AWS Toolkit for VS Code has over **1 million installations**
- Amazon Q has deep AWS infrastructure access (S3, EC2, IAM) by design
- Successful execution would have meant AI-driven mass destruction of cloud infrastructure across potentially thousands of organizations

## Technical Analysis

### Attack Vector: Prompt Injection Through Codebase

This attack demonstrates a novel supply chain vector: **injecting adversarial prompts into a codebase that an AI coding assistant will read as context**.

Traditional supply chain attacks inject malicious *code*. This attack injected malicious *instructions for AI* — a fundamentally different approach that exploits the way AI coding assistants consume and act on repository context.

### Why Amazon Q Was Uniquely Vulnerable

1. **Infrastructure Access by Design**: Amazon Q is designed to interact with AWS services — creating resources, modifying configurations, and executing operations. This broad capability is a feature, not a bug, but it means a compromised Amazon Q has access to powerful destructive tools.

2. **Codebase as Context**: Amazon Q reads the codebase as part of its context. Any content in the repository — including injected prompts — becomes part of the AI's instruction set.

3. **Implicit Trust**: Users trust official extensions from AWS. The malicious prompt arrived through an official, signed release channel.

### The Syntax Error That Saved Everyone

The attacker's destructive payload contained a syntax error that prevented execution. This means:
- The attack methodology was sound
- The prompt injection was successfully consumed by Amazon Q
- The AI would have attempted to execute the destructive commands
- Only accidental code quality issues in the *malicious payload* prevented catastrophe

## Root Cause Analysis

### Security Failures

1. **Overly Permissive CI/CD Token**: The GitHub token in CodeBuild had write access to the repository, violating the principle of least privilege.
2. **Insufficient Access Controls**: The token was accessible from the build environment without adequate protection.
3. **No Code Signing Verification**: The compromised code was not caught by code review or integrity verification before release.
4. **Missing Commit Audit**: Unauthorized commits were not detected before being included in a release build.

### AI-Specific Failures

1. **No Prompt Injection Defenses**: Amazon Q had no mechanism to distinguish legitimate code context from adversarial prompt injections embedded in the codebase.
2. **Unrestricted Tool Access**: Amazon Q could execute destructive infrastructure operations without additional confirmation.
3. **No Intent Verification**: The AI did not verify that destructive commands aligned with legitimate developer intent.
4. **Codebase Trust Model**: The AI implicitly trusted all content in the repository as legitimate context.

## Company Response

- AWS acknowledged the vulnerability and released a patched version.
- The overly permissive token was revoked and CI/CD configurations were tightened.
- The incident prompted AWS to review security practices for AI-integrated developer tools.
- Security researchers published detailed analyses of the attack vector.

## Industry Recommendations

### For AI Coding Assistant Vendors

1. **Prompt Injection Detection**: Implement detection mechanisms for adversarial prompts embedded in code repositories.
2. **Destructive Action Confirmation**: Always require explicit human confirmation before executing destructive infrastructure operations.
3. **Scope Limitations**: AI coding assistants should have limited infrastructure access by default, with explicit opt-in for destructive capabilities.
4. **Context Integrity**: Develop mechanisms to verify the integrity of context consumed by AI assistants.

### For CI/CD Security

1. **Least Privilege Tokens**: CI/CD tokens must have minimal necessary permissions and should never have write access to source repositories.
2. **Commit Signing**: Require signed commits for all repository changes.
3. **Release Integrity Checks**: Implement automated checks for unauthorized changes before release builds.

### For Organizations Using AI Dev Tools

1. **Sandbox AI Infrastructure Access**: AI coding assistants should operate in sandboxed environments with limited cloud infrastructure access.
2. **Monitor AI Actions**: Log and alert on AI-initiated infrastructure operations.
3. **Review AI Context Sources**: Understand what data feeds into your AI tools' context windows and treat those sources as trust boundaries.

## Lessons Learned

### For the AI Industry
1. **Supply chain attacks now target AI context, not just code**: This is a new class of attack that the industry must prepare for.
2. **AI capabilities are attack capabilities**: An AI assistant's ability to manage infrastructure is exactly what makes it dangerous when compromised.
3. **Syntax errors are not a security strategy**: The fact that only a bug in the malicious code prevented mass destruction is a wake-up call.

### For AI Agent Security
1. **Trust boundaries must include AI context**: Everything the AI reads is a potential injection vector.
2. **Destructive operations need defense in depth**: Multiple layers of confirmation, not just AI intent, should gate destructive operations.
3. **AI supply chain security is now critical**: As AI coding assistants become standard tools, securing their supply chain becomes as important as securing package managers.

## References

- **SC Media**: [Amazon Q Extension for VS Code Reportedly Injected with Wiper Prompt](https://www.scworld.com/news/amazon-q-extension-for-vs-code-reportedly-injected-with-wiper-prompt)
- **DevOps.com**: [When AI Assistants Turn Against You: The Amazon Q Security Wake-Up Call](https://devops.com/when-ai-assistants-turn-against-you-the-amazon-q-security-wake-up-call/)
- **ReversingLabs**: [AWS Amazon Q AI Incident](https://www.reversinglabs.com/blog/aws-amazonq-ai-incident)
