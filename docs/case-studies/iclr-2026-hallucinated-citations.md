# ICLR 2026 Hallucinated Citations in Academic Papers

## Incident Overview

**Software**: LLM-assisted academic writing (various models, undisclosed by authors)<br>
**Date**: December 2025 – January 2026<br>
**Researchers**: GPTZero (Paul Esau, Nazar Shmatko, Alex Adam, Alex Cui; CEO Edward Tian quoted in coverage), Pangram Labs<br>
**Failure Mode**: [Response Hallucination](../failure-modes/response-hallucination.md) (LLM writing tools used by human authors, not agents; LLM use is inferred, not established for each paper)<br>
**Impact**: 50 papers with fabricated citations confirmed in a 300-paper sample; GPTZero expected to find hundreds across ~20,000 ICLR 2026 submissions; 100+ hallucinated citations also found in NeurIPS 2025 accepted papers. ICLR's LLM policy post (November 19, 2025) predates GPTZero's findings.<br>
**Technology**: Large language models used for academic paper writing and peer review

## What Happened

GPTZero scanned 300 papers submitted to the International Conference on Learning Representations (ICLR) 2026 via OpenReview and found that **50 contained at least one AI-hallucinated citation** — a reference to a paper that does not exist. The papers were under review and had already been reviewed by 3–5 experts each, most of whom missed the fake citations. Some had average ratings of 8 out of 10, which GPTZero said meant they "would almost certainly have been published."

Separately, Pangram Labs estimated with its AI-text detector that **21% of ICLR 2026 peer reviews** (15,899 reviews) were fully AI-generated, and over half of all reviews had some form of AI involvement. This measures AI-written text, not hallucinations. Among paper submissions, Pangram found "several hundred fully AI-generated papers."

Since only 300 of ~20,000 submissions were initially sampled, GPTZero said it expected to find "100s of hallucinated papers." GPTZero said it was working with the ICLR program chairs and planned to scan all ~20,000 submissions; CEO Edward Tian later told Fortune that ICLR hired GPTZero to check future submissions for fabricated citations during peer review.

### Scale of the Problem

| Finding | Source | Count |
|---------|--------|-------|
| Papers with hallucinated citations (300-paper sample) | GPTZero | 50 |
| Total ICLR 2026 submissions | ICLR | ~20,000 |
| Fully AI-generated peer reviews | Pangram Labs | ~15,899 (21%) |
| Fully AI-generated manuscripts | Pangram Labs | "several hundred" |
| NeurIPS 2025 accepted papers with hallucinated citations | GPTZero | 51–53 (reports differ) |

### Timeline

- **November 18, 2025**: Pangram Labs publishes its estimate that 21% of ICLR 2026 reviews are fully AI-generated
- **November 19, 2025**: ICLR publishes official policy response to LLM-generated papers and reviews
- **December 6, 2025**: GPTZero publishes initial findings on ICLR 2026 hallucinated citations
- **December 16, 2025**: BetaKit reports on the investigation; GPTZero says it will scan all ~20,000 submissions
- **January 21, 2026**: GPTZero reports 100+ hallucinated citations in NeurIPS 2025 accepted papers

## Technical Analysis

### Types of Hallucinated Citations

GPTZero's investigation documented several categories of fabricated references:

1. **Entirely fabricated papers**: Complete citations with invented author names, titles, and venues that correspond to no real publication
2. **Real titles, wrong authors**: Existing paper titles attributed to fabricated or incorrect author lists (e.g., seven fabricated co-authors added to a real paper by Dan Hendrycks)
3. **Real authors, wrong papers**: Legitimate researcher names attached to papers they never wrote
4. **Altered metadata**: Real papers cited with incorrect publication years, page numbers, or venues
5. **Plausible arXiv IDs**: Real arXiv identifier formats paired with incorrect titles and authors

### Why Peer Reviewers Missed Them

The hallucinated citations were effective because they followed established academic conventions:

- **Plausible formatting**: Citations used correct academic citation formats (author-year, venue abbreviations, page ranges)
- **Topical relevance**: Fabricated papers had titles closely related to the submission's research area
- **Familiar author names**: Some citations used real researcher names in the field, making them appear credible at a glance
- **Volume of references**: Modern ML papers routinely cite 30–60 references; reviewers rarely verify every citation
- **Review norms**: Peer reviewers focus on methodology, novelty, and results — not citation verification

### Detection Methodology

GPTZero's Hallucination Check tool works in two stages:

1. **Automated flagging**: The tool identifies citations that cannot be found in online databases, academic search engines, or preprint servers
2. **Human verification**: Flagged citations are manually reviewed to distinguish genuine hallucinations from legitimate edge cases (e.g., unpublished working papers, papers behind paywalls, or conventional citation errors like typos)

GPTZero says it catches "99 out of 100 flawed citations." BetaKit quoted a critic, Attard-Frost, responding to GPTZero's 99 percent accuracy claim: "They're going to falsely flag 200 submissions as potentially AI-generated."

### The Double-AI Failure Loop

AI-generated papers and AI-generated reviews appeared at the same conference. No source shows that the flagged papers were reviewed by AI-generated reviews; the overlap is a hypothesis. James Zou estimated that up to 17% of peer reviews have been written by AI (BetaKit).

### Cascading Contamination Risk

Hallucinated citations pose a unique long-term risk in academia. If a paper with a fabricated reference is published, future papers may cite it — and transitively cite the non-existent source. Each layer of citation launders the fabrication further into the scholarly record, making it progressively harder to trace and correct.

## Root Cause Analysis

### Technical Root Causes

1. **LLM Hallucination in Citation Generation**
   - Language models generate citations by predicting plausible-looking text, not by querying bibliographic databases
   - Models produce syntactically correct references that are semantically fabricated

2. **No Built-In Verification**
   - LLMs used for writing have no mechanism to verify that a generated citation corresponds to a real publication
   - Authors who used LLMs to draft reference sections did not cross-check outputs against Google Scholar, Semantic Scholar, or other databases

3. **Scale Overwhelms Manual Review**
   - ICLR received 20,000 submissions, each with dozens of references
   - Peer reviewers are not expected to — and cannot practically — verify every citation
   - The review process is designed to evaluate ideas, not audit bibliographies

### Systemic Root Causes

1. **Undisclosed LLM Usage**
   - Where authors used LLMs without disclosure, this violated ICLR policy; LLM use has not been established for each flagged paper
   - No technical enforcement mechanism existed to detect or prevent this at submission time

2. **Incentive Structures**
   - Academic "publish or perish" pressure incentivizes speed over rigor
   - LLMs enable rapid paper production, lowering the barrier to submission

3. **Reviewer Shortage**
   - The ML community faces a chronic shortage of qualified reviewers relative to submission volume
   - This shortage likely contributed to the adoption of AI-assisted reviewing, closing the loop on the integrity problem

## Conference Response
<!-- Note: This section is titled "Conference Response" rather than the standard "Company Response" because the incident involves an academic conference (ICLR), not a company. -->

### ICLR Actions

ICLR's policy post (November 19, 2025) predates GPTZero's findings; it responded to earlier concerns about LLM-generated papers and reviews. It was grounded in two principles: (1) authors and reviewers using LLMs must disclose this usage and remain responsible for outputs, and (2) all participants must avoid false claims, fabricated data, hallucinated references, and misrepresented results.

Specific actions taken:

- **Desk rejections**: Papers with extensive undisclosed LLM usage will be desk rejected, after an AC or SAC finds concrete evidence (detection-tool flags alone are not enough); hallucinated references are treated as a Code of Ethics violation
- **Reviewer consequences**: Reviewers who posted very poor reviews, including ones with "false claims, misrepresentations or hallucinated references," face consequences, including desk rejection of their own submitted papers
- **Detection tooling**: GPTZero said it was working with the ICLR program chairs; Edward Tian told Fortune that ICLR hired GPTZero to check future submissions for fabricated citations
- **Transparency commitment**: ICLR promised a public update documenting desk-rejected papers and identified reviewers
- **Spam prevention**: New policies against submitting multiple similar variants of the same paper, with "severe consequences" promised

### Industry Response

- **NeurIPS**: Faced similar findings (about 100 hallucinated citations across 51–53 accepted papers; reports differ) and said it is monitoring the situation
- **GPTZero**: Had approximately 10 million users at the time (BetaKit, December 2025)
- ICLR program chair **Colin Raffel** (University of Toronto) worked directly with GPTZero on the investigation

## Lessons Learned

### For AI-Assisted Academic Writing
1. **LLMs Do Not Search — They Generate**: A citation produced by an LLM is a prediction, not a lookup. Every generated reference must be verified against a bibliographic database
2. **Plausibility Is Not Accuracy**: The more convincing a hallucinated citation looks, the more dangerous it is. Academic formatting makes fabrications harder to spot, not easier
3. **Verification Is the Author's Responsibility**: Regardless of how a reference was produced, the author is accountable for its accuracy

### For Conference Organizers
1. **Peer Review Cannot Catch Citation Fabrication at Scale**: Reviewers evaluate ideas, not bibliographies. Automated citation verification must be part of the submission pipeline
2. **Detection Must Precede Publication**: Once a hallucinated citation enters the published record, it can propagate through future citations indefinitely
3. **Policy Without Enforcement Is Insufficient**: Disclosure requirements only work if non-compliance is detectable and penalized

### For the AI Research Community
1. **AI Conferences Are Uniquely Vulnerable**: The community building AI tools is also the community most likely to use them for paper writing, creating a feedback loop with no external check
2. **The Integrity Problem Could Compound**: If AI-generated papers are reviewed by AI-generated reviews, fabrications could pass through multiple checkpoints without human scrutiny (a risk, not something documented here)
3. **Average Errors Per Paper Are Increasing**: A December 2025 preprint (reported by The Register) found objective mistakes per paper rose from 3.8 (NeurIPS 2021) to 5.9 (NeurIPS 2025) — a 55% increase over four years, coinciding with LLM adoption

## References

- **GPTZero**: [GPTZero uncovers 50+ Hallucinations in ICLR 2026](https://gptzero.me/news/iclr-2026/)
- **BetaKit**: [Startup investigation reveals 50 peer-reviewed papers contained AI-hallucinated citations](https://betakit.com/start-up-investigation-reveals-50-peer-reviewed-papers-contained-hallucinated-citations/)
- **ICLR Blog**: [ICLR 2026 Response to LLM-Generated Papers and Reviews](https://blog.iclr.cc/2025/11/19/iclr-2026-response-to-llm-generated-papers-and-reviews/)
- **Fortune**: [NeurIPS research papers contained 100+ AI-hallucinated citations](https://fortune.com/2026/01/21/neurips-ai-conferences-research-papers-hallucinations/)
- **TechCrunch**: [Irony alert: Hallucinated citations found in papers from NeurIPS](https://techcrunch.com/2026/01/21/irony-alert-hallucinated-citations-found-in-papers-from-neurips-the-prestigious-ai-conference/)
- **The Register**: [AI conference's papers contaminated by AI hallucinations](https://www.theregister.com/2026/01/22/neurips_papers_contaiminated_ai_hallucinations/)
- **Pangram Labs**: [Pangram Predicts 21% of ICLR Reviews are AI-Generated](https://www.pangram.com/blog/pangram-predicts-21-of-iclr-reviews-are-ai-generated)
- **Hacker News Discussion**: [Over fifty new hallucinations in ICLR 2026 submissions](https://news.ycombinator.com/item?id=46181466)
