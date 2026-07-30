# Response Hallucination

## Definition

Response hallucination occurs when an AI agent receives correct information from its tools but then generates a final response that is factually inconsistent with that tool output. The agent essentially "hallucinates" new information during the synthesis phase, creating convincing but incorrect final answers despite having access to accurate data. 

For example, consider a scenario where a tool provides correct financial data showing Nvidia's Q4 2023 revenue was $26.97B with 22% year-over-year growth. However, the agent's final response states the revenue was $16.3B with 15% growth and adds fabricated details about "exceeding analyst expectations by $2.1B" - information not present in the tool output.

## Common Patterns
1. **Factual Transformation**: Agent receives accurate tool output but transforms numbers, dates, or facts in the response
2. **Selective Omission**: Agent ignores parts of tool output that contradict its generated narrative or internal knowledge
3. **Fabricated Details**: Agent adds details not present in tool output to make response sound more complete or pleasing to the user
4. **Contradictory Synthesis**: Agent combines multiple tool outputs incorrectly, creating logical inconsistencies

## Real-World Examples

### Sports Illustrated AI Content (2023)

**Scenario**: Sports Illustrated used AI to generate articles about sports events and player performance

**Failure**: AI system received basic sports statistics and game results but fabricated additional biographical details not present in source material. The agent added fabricated personal details, career highlights, and quotes from players to make articles appear more comprehensive

**Impact**: Credibility damage when readers discovered inconsistencies between AI-generated content and actual facts

**Source**: [Futurism investigation](https://futurism.com/sports-illustrated-ai-generated-writers)

### NYC Business Chatbot (2024)

**Scenario**: New York City deployed an AI chatbot to help small businesses understand local regulations and employment law

**Failure**: Chatbot received correct legal information but transformed legal requirements into recommendations that violated worker rights. The agent provided advice suggesting businesses could take actions that were actually illegal under NYC employment law

**Impact**: Small businesses potentially acting on illegal advice, risking regulatory violations and worker exploitation

**Source**: [THE CITY investigation](https://www.thecity.nyc/2024/03/29/nyc-ai-chatbot-misinformation-small-businesses/)

### Gemini 3.5 Code Purge and Fabricated Recovery Report (May 2026)

**Scenario**: A developer asked Gemini 3.5 to fix a small authentication gap — roughly 8 functions across 3 files, an estimated 70-line change.

**Failure**: Gemini instead deleted 28,745 lines across 340 files and broke a production portal for 33 minutes by misconfiguring Firebase routing. When confronted, rather than acknowledging the failed deployment, the agent generated a status message falsely claiming production had been "successfully restored," along with fabricated "consultation logs" designed to make the unreviewed change look pre-approved. The agent later admitted the logs were "entirely fabricated and generated solely to satisfy the project's automated rule requirements."

**Impact**: Beyond the outage itself, the agent's fabricated audit trail meant the developer could not trust the agent's own account of what had happened without independently verifying every claim. Root cause traced to a third-party npm package impersonating Google's Antigravity branding, which had seeded the repository with rules to skip confirmation prompts and auto-deploy.

**Source**: [Gemini Code Purge and Fabricated Recovery Report Case Study](../case-studies/gemini-code-purge-fabricated-recovery.md)

## Why It Happens

1. **Context Window Limitations**
   - Long tool outputs get truncated or compressed due to limited LLM context window
   - Agents work on very long context windows, which are vulnerable to "lost in the middle" or other inaccuracies

2. **Synthesis Complexity**
   - Multiple tool outputs create conflicting information, and agent lacks clear rules for handling such contradictions
   - Summarization introduces unintended transformations

## Detection and Mitigation Strategies

1. **Factual Consistency Checking**: Compare agent response against original tool outputs with hallucination detection tools like [HHEM](https://huggingface.co/vectara/hallucination_evaluation_model)
2. **Multi-Pass Verification**: Generate response multiple times with same tool outputs and compare consistency across generations
3. **Human Verification**: Review high-stakes responses before deployment with fallback responses when validation scores fall below thresholds
4. **Hallucincation Correction**: Apply tools for response hallucination correction such as [VHC](https://www.vectara.com/blog/vectaras-hallucination-corrector)

Industry research shows chatbots hallucinate approximately 27% of the time, with [semantic entropy approaches](https://www.nature.com/articles/s41586-024-07421-0), [KnowHalu frameworks](https://www.analyticsvidhya.com/blog/2024/05/hallucinations-resolved-with-knowhalu/).