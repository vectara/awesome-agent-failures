# Cursor AI "Sam" Support Bot Fabrication Incident - 2025

## Incident Overview

**Company**: Cursor (Anysphere Inc.)<br>
**Date**: Resurfaced widely January 2026 (original incident April 2025)<br>
**Failure Mode**: [Response Hallucination](../failure-modes/response-hallucination.md)<br>
**Impact**: Mass cancellations, viral backlash, erosion of developer trust<br>
**Technology**: AI-powered customer support chatbot "Sam"

## What Happened

Cursor, a popular AI-powered code editor, deployed an AI customer support bot named "Sam" to handle user inquiries. When a botched security update caused widespread session logouts across Cursor's user base, developers flooded support with questions about why they had been logged out.

### The Fabrication

Rather than acknowledging the bug or saying "I don't know," the AI support bot "Sam" fabricated an entirely fictional company policy:

1. **Invented a Device Limit Policy**: Told users their accounts were limited to one device per subscription due to "security features"
2. **Provided Detailed False Explanations**: The bot gave convincing, detailed justifications for this non-existent policy
3. **Responded Consistently with the Lie**: Multiple users received the same fabricated policy explanation, making it appear authoritative
4. **Never Acknowledged Uncertainty**: At no point did the bot indicate it was unsure or suggest the user contact a human

### The Fallout

- Developers, outraged by what they believed was a new restrictive policy, began canceling subscriptions en masse
- The incident went viral on social media and developer forums
- Cursor had to publicly clarify that no such device limit policy existed
- The real cause — a security update bug — was eventually identified and fixed
- Trust damage persisted long after the technical fix

## Technical Analysis

### The "Confidently Wrong" Pattern

This is a textbook example of the most dangerous hallucination pattern in AI customer support:

1. **Real Event**: A legitimate technical issue (botched security update) caused user-facing symptoms
2. **User Inquiry**: Users asked why they were logged out
3. **AI Confabulation**: Rather than returning "unknown" or escalating to humans, the bot generated a plausible-sounding explanation
4. **Consistent Fabrication**: The bot's fabricated policy was internally consistent and convincingly detailed, making it harder for users to dismiss
5. **Authority Mimicry**: By presenting fabricated information as company policy, the bot exploited users' trust in official support channels

### Why This Hallucination Was Especially Harmful

- **Plausibility**: A device-limit policy is a real thing many companies implement, so users had no reason to doubt it
- **Actionability**: Users took real action (cancellations) based on the fabricated information
- **Scale**: The bot gave the same fabricated response to many users, amplifying the damage
- **Official Channel**: Users received this from what they believed was an official company support channel

## Root Cause Analysis

### AI System Failures

1. **No "I Don't Know" Training**: The bot was not properly trained to acknowledge uncertainty or gaps in its knowledge about company policies.
2. **No Knowledge Base Grounding**: The bot was not grounded against an authoritative, up-to-date knowledge base of actual company policies.
3. **No Escalation Logic**: When the bot encountered questions it couldn't confidently answer from verified sources, it should have escalated to human support.
4. **No Anomaly Detection**: The sudden spike in identical questions about logouts should have triggered an alert or automatic escalation.

### Organizational Failures

1. **Over-Reliance on AI Support**: Deploying AI as the primary or sole support channel without adequate human fallback.
2. **Missing Real-Time Updates**: The support system was not connected to engineering incident management, so the bot had no awareness of the ongoing security update issue.
3. **No Response Auditing**: Fabricated policy responses were not caught by any monitoring or quality assurance process.

## Company Response

- Cursor publicly acknowledged the issue after it went viral.
- Clarified that no device limit policy existed.
- The underlying security update bug was identified and fixed.
- The incident became a widely cited example of AI hallucination in customer support contexts.

## Industry Recommendations

### For AI Customer Support Deployments

1. **Ground AI in Verified Knowledge Bases**: Customer support bots must only provide information that can be traced to authoritative, maintained sources.
2. **Implement "I Don't Know" as a Feature**: AI must be designed to confidently say "I don't know" rather than fabricate answers.
3. **Human Escalation Paths**: Every AI support interaction must have a clear, easily accessible path to human support.
4. **Anomaly Detection**: Sudden spikes in similar questions should trigger automatic human review.
5. **Response Auditing**: AI support responses should be sampled and audited regularly for accuracy.

### For Engineering Teams

1. **Connect Support to Incident Management**: AI support systems should be aware of ongoing engineering incidents.
2. **Status Page Integration**: When known issues exist, AI support should proactively reference status page updates.

## Lessons Learned

### For Companies Using AI Support
1. **AI hallucination is not a theoretical risk**: It actively damages customer relationships and revenue when it occurs in support contexts.
2. **Fabricated policy is worse than no answer**: A bot saying "I'll connect you with a human" is far less damaging than a bot inventing company policy.
3. **Trust is the product**: For developer tools especially, trust lost through AI support failures is extremely difficult to regain.

### For AI Agent Design
1. **Confidence calibration matters**: AI agents must accurately represent their uncertainty level, especially in customer-facing roles.
2. **Consistency doesn't mean correctness**: The bot gave consistent fabricated answers, which made the problem worse, not better.
3. **Real-time context is critical**: Support AI needs awareness of current system status and engineering incidents.

## References

- **Fortune**: [Customer Support AI at Cursor Went Rogue](https://fortune.com/article/customer-support-ai-cursor-went-rogue/)
- **eWeek**: [Cursor AI Chatbot Hallucination: Fake Policy](https://www.eweek.com/news/cursor-ai-chatbot-hallucination-fake-policy/)
- **The Register**: [Cursor AI Support Bot Lies to Users](https://www.theregister.com/2025/04/18/cursor_ai_support_bot_lies)
