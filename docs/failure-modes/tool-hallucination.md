# Tool Hallucination

## Definition

Tool hallucination occurs when an AI agent's tool (RAG retrieval, database query, API call, etc.) returns incorrect, incomplete, or fabricated information, leading the agent to make decisions based on false data. This is distinct from LLM hallucination - here the tool itself provides wrong information that the agent then trusts and acts upon.

## Common Patterns
1. **RAG Retrieval Errors**: Retrieved documents contain outdated, irrelevant, or incorrect information
2. **SQL Query Failures**: Text2SQL generates syntactically correct but semantically wrong queries
3. **API Misinterpretation**: External API responses are parsed incorrectly or return unexpected formats
4. **Knowledge Base Corruption**: Indexed data contains errors that propagate through the system

## Real-World Examples

### Air Canada Chatbot Case (2024)

**Scenario**: Customer Jake Moffatt asked Air Canada's chatbot about bereavement fare discounts for travel after a family member's death

**Failure**: The RAG system retrieved or generated incorrect policy text stating customers could apply for bereavement discounts up to 90 days after purchase, when this was not actually Air Canada's policy. The chatbot provided incorrect information about bereavement fare eligibility and discount application procedures

**Impact**: Customer relied on this information, purchased full-price tickets worth $1,630, then was denied the refund. Court ruled Air Canada liable, ordering $812 in damages

**Source**: [Air Canada Chatbot Legal Ruling Case Study](../case-studies/air-canada-chatbot-legal-ruling.md)

### South Africa National AI Policy Hallucinated Citations (April 2026)

**Scenario**: South Africa's Department of Communications and Digital Technologies used a generative AI tool to help draft the country's Draft National AI Policy, published in the Government Gazette on April 10, 2026 for public comment.

**Failure**: The AI tool produced citations to academic articles that do not exist. At least 6 of the document's 67 references named real journals (*South African Journal of Philosophy*, *AI & Society*, *Journal of Ethics and Social Philosophy*) but invented the specific articles, and in some cases credited real researchers with work they never wrote. The output was published without anyone verifying a single reference.

**Impact**: News24 exposed the fabrications; on April 27, 2026, Minister Solly Malatsi withdrew the policy, calling it an "unacceptable lapse" that "compromised the integrity and credibility of the draft policy." It is the first known case of a national government pulling an official policy over AI-fabricated content, leaving South Africa without an AI governance framework.

**Source**: [South Africa AI Policy Hallucinations Case Study](../case-studies/south-africa-ai-policy-hallucinations.md)

### EY "Points of Attack" Cybersecurity Report (May 2026)

**Scenario**: EY Canada published a 44-page research report on cyber threats and fraud in loyalty-rewards programs, credited to two partners and a senior manager and drafted with the help of a generative AI tool.

**Failure**: Roughly 72% of the report's 27 citations were fabricated, broken, or misattributed. Cited Gartner, McKinsey, and Forbes documents did not exist; other links pointed to dead pages or generic tag pages. The report also stated the global loyalty market and unredeemed loyalty points were *both* exactly $200 billion — a logical impossibility — and a fabricated McKinsey citation was traced to an earlier blog post that had laundered the same non-existent source.

**Impact**: AI-detection firm GPTZero published the findings on May 14, 2026; EY pulled the report the same day and launched an internal review. The episode showed that AI-fabricated citations reach Big Four professional services, with a "poisoning the well" risk as fabricated statistics get re-cited downstream.

**Source**: [EY Loyalty Report Hallucinations Case Study](../case-studies/ey-loyalty-report-hallucinations.md)

### Mississippi Dual-Counsel AI Hallucination (June 2026)

**Scenario**: In a Mississippi federal contract dispute, attorneys for both the plaintiff and the defendant used separate AI tools — a legal-drafting platform and a general AI legal-research tool — to prepare filings ahead of trial.

**Failure**: Both legal teams' filings cited case law that did not exist: two fabricated cases from the plaintiff's counsel, four from the defense. Neither side's review process caught its own fabrications, and neither side's opposing counsel caught the other's — the adversarial system's usual safety net of "the other side will catch factual errors" failed because both sides had the same blind spot.

**Impact**: Judge Sharion Aycock cancelled the trial and removed all four attorneys from the case, fining them $1,000-$3,500 each and barring two from practicing before the court for two years — the first documented case of simultaneous federal sanctions against opposing counsel for the same class of AI misuse.

**Source**: [Mississippi Dual-Counsel AI Hallucination Case Study](../case-studies/mississippi-dual-counsel-ai-hallucination.md)

### Ninth Circuit Precedential AI-Hallucination Sanctions (June 2026)

**Scenario**: Attorneys Mike Singh Sethi and William Rounds filed briefs in a Ninth Circuit immigration appeal, drafted in part by an unlicensed law-school graduate at their firm using unauthorized generative AI.

**Failure**: The briefs cited two Ninth Circuit cases that never existed ("*Eduardo v. Garland*" and "*Lay v. Holder*") and misrepresented the holdings of real cases. When questioned, the attorneys first claimed the errors were typos, and at oral argument Rounds denied AI use three separate times before conceding it was "more likely than not" the source.

**Impact**: In a published, precedential order, the court imposed $2,500 personal sanctions on each attorney, a six-month suspension from practicing before the Ninth Circuit, and a two-year firm-wide requirement to disclose AI use and certify personal citation review in every future filing. The panel held that the violation occurs "at the point of signing and filing," not at the point of using AI to draft — and treated the attorneys' repeated denials as an independent aggravating factor.

**Source**: [Ninth Circuit AI-Hallucination Sanctions Case Study](../case-studies/ninth-circuit-ai-hallucination-sanctions.md)

### KPMG "Agentic AI" Report Hallucinations (June 2026)

**Scenario**: KPMG published "Total Experience: Redefining Excellence in the Age of Agentic AI," a research report on enterprise adoption of agentic AI systems, in October 2025.

**Failure**: GPTZero's forensic review found only 5 of the report's 45 citations correctly matched their claimed sources, with roughly half of its factual claims false, unsupported, or misattributed — including named but unsubstantiated case studies at UBS, Swiss Federal Railways, and Transport for London, a fabricated flight-booking capability attributed to Emirates' "Sara" assistant, and a headline statistic (55% of CEOs ranking AI as top priority) that directly contradicted KPMG's own 2025 CEO Outlook survey, which had reported 71% for the same figure.

**Impact**: KPMG withdrew the report from its websites following the Financial Times/GPTZero findings, reported June 12, 2026. The self-contradicting internal statistic was catchable without any external verification — simply checking the report against KPMG's own prior publication — underscoring that even the cheapest verification step was skipped.

**Source**: [KPMG Agentic AI Report Hallucinations Case Study](../case-studies/kpmg-agentic-ai-report-hallucinations.md)

## Why It Happens

1. **RAG Quality Issues**
   - Low precision (retrieved chunks don't align with query intent) or low recall (failure to retrieve all relevant information) in RAG
   - Outdated information during data ingest or relevant data not ingested into RAG system, or conflicting information from multiple sources
   - Poor data preprocessing: bad chunking strategies, inaccurate processing of tables or image data

2. **Query Generation Failures in SQL Tools**
   - Text2SQL systems misunderstanding database schema
   - Ambiguous natural language leading to wrong entity resolution

3. **Tool Integration Issues**
   - API response not properly processed by agent due to unknown format
   - Authentication or permission failures returning default/cached responses, or empty responses

Recent research reveals significant tool reliability challenges. [RAG systems depend heavily on knowledge base quality](https://www.k2view.com/blog/rag-hallucination/), while [Text2SQL systems face inherent limitations and hallucinations](https://arxiv.org/html/2502.15723v3). Entity confusion is particularly problematic, with systems often selecting incorrect but similar-sounding entities.

## Detection and Mitigation Strategies

1. **RAG Evaluation**: Implement continuous RAG evaluation to identify and fix low quality RAG responses.
2. **Confidence Scoring**: Implement retrieval confidence metrics with thresholds and reject tool outputs below certain confidence levels
3. **Output Validation**: Verify tool results against known constraints, data types, and implement systematic checks on tool responses
4. **Human-in-the-Loop**: Require human validation for high-stakes decisions with fallback mechanisms for uncertain outputs
5. **Audit Trails**: Log all tool queries and responses for debugging, analysis, and source attribution
6. **Schema Evolution**: Keep Text2SQL systems updated with database schema changes
