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

### Silent Document Extraction Errors (Ongoing)

**Scenario**: AI agents extract structured data from documents (invoices, contracts, financial reports) using OCR or LLM-based extraction tools, producing JSON output for downstream processing

**Failure**: Extraction tools return well-formed JSON with plausible but incorrect values — line item amounts that don't sum to the stated total, dates that are close but wrong, vendor names subtly altered by OCR artifacts or LLM "corrections," and tax calculations using the wrong rate. The output passes schema validation and survives human spot-checks because the values look reasonable

**Impact**: Financial systems auto-processing extracted invoices pay wrong amounts. Compliance workflows trusting extracted dates miss deadlines. Audit trails become unreliable when extracted records don't match source documents. These errors compound silently across thousands of documents before anyone notices the pattern

**Mitigation**: Add a mechanical verification step between extraction and downstream action: arithmetic validation (do line items sum to the total?), cross-field consistency checks (does the tax amount match the rate times the subtotal?), and automated source-document comparison. This should be automated rather than human review to scale with agent throughput

**Source**: Common pattern observed in invoice processing pipelines using Textract, Azure Document Intelligence, and LLM-based extraction. See also [Vectara Hallucination Leaderboard](https://github.com/vectara/hallucination-leaderboard) for variation in model accuracy on document summarization tasks

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
