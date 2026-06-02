# EY "Points of Attack" Cybersecurity Report Pulled Over AI Hallucinations - May 2026

## Incident Overview

**Firm**: EY Canada (Ernst & Young)<br>
**Document**: "Points of Attack: Uncovering Cyber Threats and Fraud in Loyalty Systems" (44-page report, credited to two partners and a senior manager)<br>
**Date**: Investigation published and report withdrawn May 14, 2026<br>
**Investigators**: GPTZero<br>
**Failure Mode**: [Tool Hallucination](../failure-modes/tool-hallucination.md)<br>
**Impact**: A Big Four firm withdrew a published research report after roughly 72% of its 27 citations were found to be fabricated, broken, or misattributed<br>
**Technology**: Generative AI used in drafting (EY did not specify the tool)

## What Happened

EY Canada published a report on cyber threats and fraud in loyalty-rewards programs. AI-detection company GPTZero analyzed it and found the report was riddled with fabricated and misattributed sources alongside AI-written text. GPTZero published its findings on May 14, 2026; EY pulled the report from its website the same day and opened an internal review.

Of the report's 27 citations, more than 70% were fabricated, broken, or incorrectly referenced. References attributed to *Forbes*, *McKinsey & Company*, *Gartner*, *TechCrunch*, *WIRED*, *BleepingComputer*, and *Cisco Talos* led to missing pages, generic tag pages, or articles that never existed.

### The Catalogue of Errors

GPTZero's investigation documented several distinct problems:

- **Non-existent sources**: Cited Gartner, McKinsey, and Forbes documents that do not exist.
- **Broken or wrong links**: Citations pointing to dead URLs or to generic publication tag pages presented as if they were specific articles.
- **A self-contradicting headline statistic**: The report valued the global loyalty market at exactly $200 billion on one page, then stated that *unredeemed* loyalty points were also exactly $200 billion on another — a logical impossibility that would require a market well over $400 billion.
- **Citation "laundering"**: A fabricated McKinsey citation was traced to an earlier low-quality finance blog post that had itself cited the same non-existent McKinsey source — a fabrication propagating from one document into another.
- **Shifting attributions**: A "72% fraud" statistic was attributed to different sources (Paystone, Forter) on different pages, with the apparent original being an unrelated 2017 survey; an "89% increase" claim was given two different interpretations on different pages.

### EY's Response

EY removed the report and said, "EY Canada takes the accuracy of all the content we publish seriously," and confirmed the report was not connected to client work. The firm launched an internal review.

## Why This Case Matters

### Hallucinated Citations Reach Professional Services

The repository already documents fabricated-citation failures in legal filings ([Sullivan & Cromwell](sullivan-cromwell-bankruptcy-hallucinations.md)), academic papers ([ICLR 2026](iclr-2026-hallucinated-citations.md)), and government policy ([South Africa's National AI Policy](south-africa-ai-policy-hallucinations.md)). This case extends the same failure into Big Four consulting — published thought-leadership research from a firm whose entire value proposition is rigor and trustworthiness. The pattern is now visibly sector-agnostic: any institution that lets generative AI draft reference-heavy material and publishes it without verification is exposed.

### The "Poisoning the Well" Risk

Because the report was published under EY's name, its fabricated statistics and citations could be picked up and re-cited by other researchers, analysts, and journalists. The McKinsey-citation example shows this is not hypothetical: the investigation found a fabrication that had already been laundered from one document into another. Authoritative-looking but invented references degrade the broader information record, not just the single document.

## Technical Analysis

### Generation Without Retrieval

An LLM generates a citation as plausible text, not as a database lookup. A reference like a Gartner report on loyalty fraud is a statistically likely completion that matches the form of a real citation while corresponding to nothing. Without a retrieval-and-verification step that confirms each source exists and says what the document claims, fabrications at this density are the expected outcome — not a surprise.

### Why It Survived to Publication

- **Brand authority**: A report on EY letterhead carries a presumption of accuracy that suppresses scrutiny — internally and from readers.
- **Plausible specificity**: Round, quotable statistics ("$200 billion," "72%," "89%") and brand-name sources (McKinsey, Gartner, Forbes) read as authoritative precisely because they are specific and familiar.
- **No citation audit**: Catching the fabrications required checking each of 27 references against its supposed source — work that GPTZero's three-person review did and EY's publication process did not.

## Root Cause Analysis

### AI-Specific Failures

1. **No external verification step**: Citations and statistics generated with an AI tool were published without being checked against the named sources.
2. **Confidence without grounding**: Fabricated references were formatted identically to legitimate ones, giving reviewers no signal of which were invented.
3. **Internal inconsistency went unnoticed**: The $200B / $200B contradiction was detectable from the text alone, without any external lookup — yet it shipped, indicating the document received little substantive review.

### Process Failures

1. **No fact-checking gate before publication**: A firm built on assurance published external-facing research without a citation-verification step.
2. **Undisclosed AI use without compensating controls**: AI appears to have been used to draft the report without the verification that such use demands.
3. **Brand authority substituted for review**: The firm's reputation did not — and cannot — replace the work of checking sources.

## Lessons Learned

### For Firms Publishing AI-Assisted Research

1. **Verification must be a hard gate, not a guideline**: Every citation and headline statistic in an external-facing report should be confirmed against its source before publication, with a step that cannot be skipped.
2. **Check internal consistency, too**: Some fabrications (like the $200B contradiction) are catchable by reading the document critically, with no external lookup required. A basic editorial pass should catch them.
3. **Reputation raises the stakes, it does not lower the bar**: The more authoritative the publisher, the further its fabrications propagate. Brand authority is a reason for more verification, not less.

### For Readers and Downstream Users

1. **Treat statistics and citations as claims to verify**: Even from a Big Four firm, a specific-looking statistic or citation may be AI-fabricated. Trace it to a primary source before relying on or re-citing it.
2. **Watch for citation laundering**: A fabrication can enter the record through one document and be re-cited by others. A familiar-sounding statistic with no traceable origin is a warning sign.

## References

- **GPTZero**: [Investigation: Hallucinations in Ernst & Young Report on Loyalty Fraud](https://gptzero.me/investigations/ey)
- **Cybernews**: ["Poisoning the well": EY retracts cyber report packed with AI slop](https://cybernews.com/ai-news/ernst-young-ai-slop-gptzero/)
- **International Accounting Bulletin**: [EY removes loyalty rewards study after AI hallucinations found](https://www.internationalaccountingbulletin.com/news/ey-removes-loyalty-rewards-study-after-ai-hallucinations-found/)
- **Computing**: [EY cybersecurity report pulled after probe finds 'AI hallucinations'](https://www.computing.co.uk/news/2026/ai/ey-cybersecurity-report-withdrawn-ai-hallucinations)
