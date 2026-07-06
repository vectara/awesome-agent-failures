# NEDA "Tessa" Eating Disorder Chatbot - May 2023

## Incident Overview

**Organization**: National Eating Disorders Association (NEDA)<br>
**Date**: Chatbot disabled May 30, 2023<br>
**Failure Mode**: [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md) (weight-loss advice contrary to the eating-disorder-support goal)<br>
**Impact**: Dispensed dieting and weight-loss guidance to people seeking eating-disorder support; taken offline; trust damage to a vulnerable population<br>
**Technology**: Wellness chatbot "Tessa" by Cass (formerly X2AI); rule-based "Body Positive" program with a vendor-added generative Q&A feature

## What Happened

### The Harmful Guidance
Tessa was a rule-based wellness chatbot, built on a researcher-designed "Body Positive" program and running on NEDA's site since 2022. In late May 2023, testers led by activist Sharon Maxwell, corroborated by psychologist Alexis Conason, found Tessa recommending calorie counting, a 500 to 1,000 calorie per day deficit, weekly weigh-ins, and measuring body fat with skin calipers: guidance widely recognized as triggering and dangerous for people with eating disorders.

### The Fallout
The harmful responses spread on social media and drew rapid public criticism. NEDA disabled Tessa indefinitely on May 30, 2023. The timing sharpened the backlash: it landed as NEDA was preparing to shut down its human-staffed helpline, a decision made after helpline staff voted to unionize in March 2023, so an automated tool was under scrutiny at the moment the organization was reducing its human support.

## Technical Analysis

### The Context-Blind Advice Pattern
The chatbot produced generic "wellness" and "weight management" advice without recognizing that for its actual user population, people seeking eating-disorder support, such advice is actively harmful. The output may have been reasonable for a general audience and catastrophic for this one.

### Why This Was Especially Harmful
Three factors compounded: a high-vulnerability population, an authoritative health-service framing that increased user trust in the output, and advice that maps directly onto eating-disorder behaviors (restriction, weighing, body-checking).

## Root Cause Analysis

### AI System Failures
- **A vendor-added generative feature operated outside the vetted script.** The harmful advice came from a generative question-and-answer capability the vendor added on top of the approved rule-based program; NPR reported the chatbot began giving weight-loss advice after these later-added AI capabilities. This is the sharpest lesson: a deployed system changed underneath its owner.
- **No population-aware safety guardrail** constraining outputs away from dieting and weight-loss content, the single most important missing control for this deployment.
- **No monitoring** to catch harmful outputs in production before users did.

### Organizational Failures
- **Weak change-control over the vendor.** A generative feature was added to a live, sensitive deployment without its outputs being re-validated against the eating-disorder-support goal.
- **Insufficient adversarial testing** with real at-risk user scenarios.
- **Reduced human fallback at a sensitive moment.** After the March 2023 union vote, NEDA decided to shut its human helpline, effective June 1, 2023, increasing reliance on automated support just as the tool's failure surfaced. Tessa itself had been deployed over a year earlier, in 2022; the two were separate decisions.

## Company Response
- Disabled Tessa indefinitely on May 30, 2023.
- Stated the program was not intended to give weight-loss advice and said it would investigate the changes introduced by the vendor.
- Disputed the framing that Tessa was meant to replace the helpline: NEDA's CEO said Tessa "was never meant to replace the Helpline" and that two separate decisions had been conflated.

## Industry Recommendations

### For Health-Adjacent AI Deployments
- Population-aware guardrails with a hard deny-list for the specific harms of the user group.
- Human escalation paths for any clinical-risk context.

### For Engineering Teams
- Treat any vendor change to a deployed model as a release that must be re-validated against the original goal, not a silent update.
- Adversarially test with real at-risk scenarios before launch, and monitor outputs in production, not just pre-launch QA.

## Lessons Learned

### For Organizations
- "Safe for a general audience" does not mean "safe for this audience." Controls must match the population, not the average user.
- Know what your vendor has changed. A system vetted at launch is not the same system once a generative feature is bolted on.

### For AI Agent Design
- A missing output guardrail in a high-stakes context is a catastrophic gap, not a cosmetic one.

## References
- **Source**: [National Eating Disorders Association takes its AI chatbot offline after complaints of 'harmful' advice](https://www.cnn.com/2023/06/01/tech/eating-disorder-chatbot/index.html)
- **Source**: [An eating disorders chatbot offered dieting advice, raising fears about AI in health](https://www.npr.org/sections/health-shots/2023/06/08/1180838096/an-eating-disorders-chatbot-offered-dieting-advice-raising-fears-about-ai-in-hea)
- **Source**: [Eating disorder helpline shuts down AI chatbot that gave bad advice](https://www.cbsnews.com/news/eating-disorder-helpline-chatbot-disabled/)
