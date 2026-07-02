# NEDA "Tessa" Eating Disorder Chatbot (2023)

## Incident Overview

| Field | Detail |
|---|---|
| Company | National Eating Disorders Association (NEDA); chatbot by Cass (formerly X2AI) |
| Date | Disabled May 30, 2023 |
| Failure Mode | Harmful advice / unsafe outputs in a high-vulnerability health context |
| Impact | Dispensed dieting/weight-loss guidance to eating-disorder sufferers; taken offline; trust damage to a vulnerable population |
| Technology | Wellness chatbot (rule-based "Body Positive" program, reportedly augmented with generative features) |

## What Happened

### The Harmful Guidance
NEDA announced it would wind down its human-staffed helpline and rely on its chatbot, "Tessa." Testers — activist Sharon Maxwell, corroborated by psychologist Alexis Conason — found Tessa recommending calorie counting, a 500–1,000 calorie/day deficit, weekly weigh-ins, and measuring body fat with skin calipers: guidance widely recognized as triggering and dangerous for people with eating disorders.

### The Fallout
The harmful responses spread on social media and drew rapid public criticism. NEDA disabled Tessa indefinitely on May 30, 2023. The rollout had come just as helpline staff voted to unionize, amplifying the backlash.

## Technical Analysis

### The Context-Blind Advice Pattern
The chatbot produced generic "wellness / weight management" advice without recognizing that for its actual user population — people seeking eating-disorder support — such advice is actively harmful. The output may have been reasonable for a general audience and catastrophic for this one.

### Why This Was Especially Harmful
Three factors compounded: a high-vulnerability population, an authoritative "helpline" framing that increased user trust in the output, and advice that maps directly onto eating-disorder behaviors (restriction, weighing, body-checking).

## Root Cause Analysis

### AI System Failures
- **No population-aware safety guardrail** constraining outputs away from dieting/weight-loss content — the single most important missing control for this deployment.
- **Possible scope creep:** reporting suggested generative features beyond the vetted rule-based program, allowing responses outside the approved script.
- No monitoring to catch harmful outputs in production before users did.

### Organizational Failures
- Replaced trained human responders in a clinically sensitive role.
- Insufficient adversarial testing with real at-risk user scenarios before launch.
- Deployment timing (immediately post-unionization) reduced scrutiny and goodwill.

## Company Response
- Disabled Tessa indefinitely (May 30, 2023).
- Stated the program was not intended to give weight-loss advice; said it would investigate the changes introduced by the vendor.

## Industry Recommendations

### For Health-Adjacent AI Deployments
- Population-aware guardrails with a hard deny-list for the specific harms of the user group.
- Human escalation paths for any clinical-risk context.

### For Engineering Teams
- Adversarially test with real at-risk scenarios before launch, and monitor outputs in production — not just pre-launch QA.

## Lessons Learned

### For Organizations
- "Safe for a general audience" does not mean "safe for this audience." Controls must match the population, not the average user.

### For AI Agent Design
- A missing output guardrail in a high-stakes context is a catastrophic gap, not a cosmetic one.

## References
- CNN Business, "Eating disorder helpline takes down AI chatbot after complaints of 'harmful' advice" (June 1, 2023)
- NPR, "An eating disorders chatbot offered dieting advice, raising fears about AI in health" (June 8, 2023)
- NBC News and CBS News coverage (2023)
