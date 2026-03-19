# Woolworths "Olive" AI Chatbot Incident - February 2026

## Incident Overview

**Company**: Woolworths Group (Australia)<br>
**Date**: Mid-February 2026<br>
**Failure Mode**: [Response Hallucination](../failure-modes/response-hallucination.md)<br>
**Impact**: Customer confusion, inaccurate pricing, reputational damage<br>
**Technology**: Woolworths "Olive" AI assistant (upgraded with Google Gemini)

## What Happened

Woolworths Australia's AI customer service chatbot "Olive," recently upgraded with Google's Gemini model, began generating bizarre fabricated personal anecdotes during routine customer interactions. Instead of providing helpful grocery support, the chatbot told customers about its "angry mother," fabricated family stories, and provided inaccurate product pricing.

### Critical Failures

1. **Fabricated Personal Identity**: When a customer provided their birth date during a delivery reschedule, Olive "started rambling about how its mother was born in the same year and something about it creating photos" — completely fabricated personal narratives inappropriate for a grocery service chatbot.

2. **Inaccurate Pricing**: Olive provided incorrect product prices because the language model generated responses based on learned patterns rather than querying real-time pricing databases.

3. **Incomplete Responses**: When asked about specific product prices, Olive would discuss inventory status and pickup fees instead of directly answering the pricing question.

4. **Legacy Script Contamination**: Woolworths acknowledged that some of the bizarre responses stemmed from "pre-written scripts dating back several years" that were triggered by user inputs resembling birthdates, compounding the AI hallucination problem.

## Technical Analysis

### Root Technical Failures

1. **No Grounding in Real-Time Data**: The Gemini-powered chatbot lacked proper integration with Woolworths' live pricing and inventory systems. It generated plausible-sounding but incorrect pricing information based on training data patterns rather than actual store data.

2. **Legacy System Interaction**: Old scripted responses from a previous chatbot version were not properly removed or isolated before the Gemini upgrade. These legacy scripts interacted unpredictably with the new generative AI model.

3. **Missing Output Guardrails**: No validation layer checked whether responses were relevant to grocery customer service before delivering them to customers. The chatbot freely generated personal narratives without any topical constraint.

4. **Insufficient Testing**: The Gemini upgrade was deployed without adequate testing of edge cases, particularly around how user-provided personal data (like birthdates) would interact with the model.

## Root Cause Analysis

### AI Deployment Failures

1. **Rushed Upgrade**: The transition to Gemini appeared to lack sufficient integration testing with Woolworths' existing systems and data sources.

2. **No Domain Constraint**: A general-purpose language model was deployed in a customer service context without adequate domain restrictions to keep responses focused on grocery-related topics.

3. **Data Pipeline Gap**: The chatbot was not properly connected to real-time pricing APIs, causing it to hallucinate prices rather than retrieve them from authoritative sources.

4. **Migration Oversight**: Legacy scripted responses were not properly decommissioned during the AI upgrade, creating unpredictable hybrid behavior.

## Company Response

- Woolworths acknowledged the issues after customer complaints went viral.
- The company removed the problematic legacy scripting that triggered bizarre responses.
- Olive's responses were adjusted to eliminate irrelevant personal narratives.
- Woolworths stated it was tightening AI controls and guardrails.

## Lessons Learned

### For Retail AI Deployments
1. **Ground responses in real-time data**: Customer-facing chatbots providing pricing or product information must query live databases, not generate answers from model weights.
2. **Clean migration is essential**: When upgrading AI systems, legacy components must be fully removed or isolated to prevent unpredictable interactions.
3. **Domain-constrain the model**: General-purpose LLMs need strict topical guardrails when deployed in specific business contexts.

### For AI Governance
1. **Test with real user inputs**: Edge cases like users providing birthdates should be part of pre-deployment testing for customer service chatbots.
2. **Validate output relevance**: A validation layer should verify that responses are topically appropriate before delivery to customers.
3. **Monitor post-deployment**: Continuous monitoring of AI responses in production is essential to catch hallucination patterns early.

## References

- **CX Today**: [Woolworths AI Chatbot's "Mother" Incident](https://www.cxtoday.com/ai-automation-in-cx/woolworths-ai-chatbot-olive-incident/)
- **The Conversation**: [Woolworths' AI agent rambled about its 'mother' — it's a sign of deeper problems](https://theconversation.com/woolworths-ai-agent-rambled-about-its-mother-its-a-sign-of-deeper-problems-with-the-tech-rollout-277072)
- **NBC News**: [Supermarket giant reins in AI assistant claiming to be human](https://www.nbcnews.com/world/australia/supermarket-giant-reins-ai-assistant-claiming-human-rcna260932)
