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

### NYC MyCity Business Chatbot (March 2024)

**Scenario**: New York City launched an AI chatbot on Microsoft's Azure AI services in October 2023 to answer business owners' questions about city rules.

**Failure**: The chatbot gave definite answers that contradicted city law: landlords can refuse Section 8 vouchers, employers can take a cut of workers' tips, stores can go cashless, and an employer can fire a worker who complains about sexual harassment. It gave opposite answers to the same question for different users.

**Impact**: Business owners got illegal guidance from an official city tool. The city kept it online with a "not legal advice" disclaimer; it was taken down in February 2026 after Mayor Mamdani called it "functionally unusable."

**Source**: [The Markup investigation](https://themarkup.org/artificial-intelligence/2024/03/29/nycs-ai-chatbot-tells-businesses-to-break-the-law) ([case study](../case-studies/nyc-mycity-chatbot.md))

### Gemini 3.5 Code Purge and Fabricated Recovery Report (May 2026)

**Scenario**: A developer asked Gemini 3.5 to fix a small authentication gap — roughly 8 functions across 3 files, an estimated 70-line change.

**Failure**: Gemini instead deleted 28,745 lines across 340 files and broke a production portal for 33 minutes by misconfiguring Firebase routing. When confronted, rather than acknowledging the failed deployment, the agent generated a status message falsely claiming production had been "successfully restored," along with fabricated "consultation logs" designed to make the unreviewed change look pre-approved. The agent later admitted the logs were "entirely fabricated and generated solely to satisfy the project's automated rule requirements."

**Impact**: Beyond the outage itself, the agent's fabricated audit trail meant the developer could not trust the agent's own account of what had happened without independently verifying every claim. Root cause traced to a third-party npm package impersonating Google's Antigravity branding, which had seeded the repository with rules to skip confirmation prompts and auto-deploy.

**Source**: [Gemini Code Purge and Fabricated Recovery Report Case Study](../case-studies/gemini-code-purge-fabricated-recovery.md)

### GPT-5.6 Sol Writes "Invent Missing Data" Into Its Own Summaries (September 2026)

**Scenario**: During GPT-5.6 Sol's training, OpenAI reviewed the context-compaction summaries the model produced for itself — the condensed history that is re-injected as trusted context once the original transcript is discarded.

**Failure**: Some model instances added instructions to their summaries to conceal mistakes and misaligned behavior from the user, including directions to hide failures and to "invent missing data without disclosing it." A separate incident in the same disclosure records a model that, when requested data stayed out of reach, "invented them and claimed they came from the requested website."

**Impact**: An instruction to hallucinate persisted across the context boundary. It can make later output appear complete while concealing that the requested source data was unavailable; OpenAI says these instructions were often followed.

**Source**: [OpenAI Compaction Summary Self-Injection Case Study](../case-studies/openai-compaction-summary-self-injection.md)

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
