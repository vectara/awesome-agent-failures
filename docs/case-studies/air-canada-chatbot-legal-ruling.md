# Air Canada Chatbot Legal Ruling - February 2024

## Incident Overview

**Company**: Air Canada  
**Date**: February 2024  
**Failure Mode**: [Tool Hallucination](../failure-modes/tool-hallucination.md)  
**Impact**: Legal liability, $812 CAD in damages  
**Precedent**: First legal ruling holding company liable for AI chatbot misinformation

## What Happened

Jake Moffatt's grandmother died on Remembrance Day 2022. On the same day, Moffatt visited Air Canada's website to purchase bereavement fare tickets to fly to Ontario for the funeral.

When Moffatt consulted Air Canada's website chatbot about bereavement fares, the AI assistant provided incorrect information. The chatbot told him he could:
- Purchase tickets at regular price immediately
- Apply for bereavement discount within 90 days of ticket purchase
- Submit refund application retroactively

Acting on this advice, Moffatt purchased full-price tickets totaling $1,630.36 CAD for flights to and from Toronto.

## Technical Analysis

The chatbot's RAG (Retrieval-Augmented Generation) system provided fundamentally incorrect policy information. The actual Air Canada policy required bereavement fare requests to be submitted **before** ticket purchase, not after.

**Chatbot Response** (screenshot evidence):
> "If you need to travel immediately or have already travelled and would like to submit your ticket for a reduced bereavement rate, kindly do so within 90 days of the date your ticket was issued by completing our Ticket Refund Application form."

**Actual Policy**: Bereavement fares must be requested before purchase, not retroactively.

## Legal Proceedings

When Moffatt submitted his bereavement fare claim, Air Canada denied it, citing the correct policy. Moffatt took the case to British Columbia's Civil Resolution Tribunal (CRT).

### Air Canada's Defense

Air Canada made a remarkable legal argument, claiming the chatbot was "a separate legal entity that is responsible for its own actions" and therefore the airline should not be held liable for its responses.

### Tribunal Ruling

**Judge**: Christopher Rivers, Civil Resolution Tribunal  
**Decision**: Ruled against Air Canada  

**Key Findings**:
- "This is a remarkable submission" - regarding Air Canada's separate entity defense
- "I find Air Canada did not take reasonable care to ensure its chatbot was accurate"
- "It does not explain why the webpage titled 'Bereavement Travel' was inherently more trustworthy than its chatbot"
- The tribunal rejected the separate entity argument, stating that as part of Air Canada's website, the airline was responsible for all information provided

**Damages Awarded**: $812.02 CAD total
- $650.88 in damages (difference between bereavement and regular fares)
- Additional costs and fees

## Root Cause Analysis

### Technical Root Causes

1. **RAG System Failure**: The retrieval system either:
   - Retrieved outdated or incorrect policy documents
   - Generated plausible-sounding policy text not grounded in actual documentation
   - Failed to properly parse the temporal requirements in bereavement policies

2. **Lack of Validation**: No verification mechanism to ensure chatbot responses matched current policies

3. **Training Data Issues**: Possible contamination with outdated policies or similar-but-different airline policies

### Organizational Root Causes

1. **Inadequate Testing**: Chatbot not tested against critical customer service scenarios
2. **No Human Oversight**: No review process for policy-related responses
3. **Unclear Responsibility**: No clear ownership of chatbot accuracy within organization

## Company Response

### Air Canada's Actions

**Air Canada** took the following documented steps:

1. **Legal Defense**: Initially defended the case by claiming the chatbot was "a separate legal entity" responsible for its own actions
2. **Compliance with Ruling**: After losing the tribunal case, Air Canada paid the $812.02 CAD award to the customer
3. **No Public Changes Disclosed**: Air Canada did not publicly announce specific changes to their chatbot system or policies following the ruling

### No Technical Improvements Announced

Air Canada did not publicly share:
- Specific improvements made to chatbot accuracy
- New validation processes implemented
- Policy synchronization measures taken
- Staff training programs on AI limitations

## Industry Recommendations

### Immediate Actions Air Canada Could Have Taken

1. **Policy Database Synchronization**: Ensure chatbot knowledge base exactly matches current published policies
2. **Conservative Response Strategy**: For policy questions, direct users to official policy pages rather than generating responses
3. **Human Handoff**: Route complex policy questions to human agents
4. **Disclaimer Addition**: Clear warnings about chatbot limitations for official policy information

### Industry Best Practices

1. **Source Attribution**: Require chatbots to cite specific policy documents with version numbers
2. **Real-Time Validation**: Cross-check generated responses against authoritative sources
3. **Audit Trails**: Log all customer interactions for review and improvement
4. **Regular Testing**: Systematic testing of chatbot responses against known correct answers

## Legal Significance

This case established important precedent:
- Companies cannot disclaim responsibility for AI systems by claiming they are separate entities
- Organizations are liable for misinformation provided by their AI tools
- Customer reliance on AI advice creates legal obligations for accuracy

## Lessons Learned

1. **Corporate Liability**: AI systems are extensions of the company, not separate entities
2. **Accuracy Requirements**: Higher accuracy standards needed for policy and legal information
3. **Verification Necessity**: Critical information requires verification mechanisms
4. **Legal Precedent**: This ruling will likely influence future AI liability cases

## References

- **Primary Source**: [CBC News - Air Canada liable for chatbot's bad advice](https://www.cbc.ca/news/canada/british-columbia/air-canada-chatbot-lawsuit-1.7116416)
- **Legal Analysis**: [McCarthy Tétrault - Moffatt v. Air Canada](https://www.mccarthy.ca/en/insights/blogs/techlex/moffatt-v-air-canada-misrepresentation-ai-chatbot)
- **Industry Impact**: [American Bar Association - Companies Remain Liable](https://www.americanbar.org/groups/business_law/resources/business-law-today/2024-february/bc-tribunal-confirms-companies-remain-liable-information-provided-ai-chatbot/)
- **Washington Post**: [Air Canada chatbot promised a discount](https://www.washingtonpost.com/travel/2024/02/18/air-canada-airline-chatbot-ruling/)
