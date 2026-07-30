# Ninth Circuit's Precedential Sanctions Order for AI-Hallucinated Briefs - June 2026

## Incident Overview

**Court**: U.S. Court of Appeals for the Ninth Circuit<br>
**Case**: *Lnu v. Blanche*, No. 24-4790 (immigration appeal)<br>
**Panel**: Judges Richard Paez, Carlos Bea, and Danielle Forrest<br>
**Date**: Published, precedential order issued June 3, 2026<br>
**Failure Mode**: [Tool Hallucination](../failure-modes/tool-hallucination.md)<br>
**Impact**: $2,500 personal sanction against each of two attorneys; six-month suspension from practicing before the Ninth Circuit; two-year firm-wide requirement to disclose AI use and certify personal review in every future filing<br>
**Technology**: Unspecified generative AI tool, used by an unlicensed law-school-graduate brief writer at the attorneys' firm

## What Happened

Attorneys Mike Singh Sethi and William Rounds filed briefs in an immigration appeal before the Ninth Circuit containing multiple nonexistent case citations, misattributed quotations, and gross misrepresentations of real case holdings. Among the fabrications: an opening brief cited "*Eduardo v. Garland*, 28 F.4th 742 (9th Cir. 2022)" and "*Lay v. Holder*, 729 F.3d 962 (9th Cir. 2013)" — two case citations that do not exist and never existed.

### Repeated Denial Before Admission

When the fabrications were flagged, Sethi and Rounds did not immediately acknowledge AI involvement. They first told the court the errors were simple typographical mistakes. At oral argument, Rounds asserted three separate times that generative AI "was not used" in preparing the brief. Only after continued questioning did the attorneys concede it was "more likely than not" that the errors originated from an unlicensed law-school graduate at their firm using unauthorized AI tools to draft the brief.

### The Sanctions

The panel imposed:

- **$2,500 in personal monetary sanctions** against each of Sethi and Rounds
- **A six-month suspension** from practicing before the Ninth Circuit for both attorneys
- **A requirement to serve a copy of the order** on their clients, opposing counsel, and the presiding judge in every pending state or federal case where they are counsel of record, and on every attorney at their firm
- **A two-year, firm-wide disclosure mandate**: for two years, any filing by Sethi, Rounds, or any attorney at their firm must include a sworn statement addressing whether generative AI was used, identifying the specific tool if so, and certifying that the signing attorney personally reviewed the filing and verified that every citation and quotation refers to real authority

### The Court's Key Holding

The panel was explicit that the misconduct was not the underlying use of AI itself:

> "We do not sanction Sethi and Rounds for the simple fact that they or their subordinates used generative AI."

Instead, the court located the violation at the point of signing and filing, not at the point of research and drafting:

> If an attorney files a brief with cases or quotations that do not exist, or completely misrepresents what a real authority stands for, it generally does not matter if he pulled the hallucination or misrepresentation from the output of an artificial intelligence tool or from his own natural intelligence.

The court also warned that AI-driven *inaccuracies* — misrepresenting what a real case actually holds — may be more dangerous than outright fabrications, because a plausible misstatement about a real, citable case is harder for a reviewer to catch than a citation to a case that simply does not exist. And it left no ambiguity about the effect of the attorneys' initial denials on the outcome: "there is no upside to denying... AI use or passing off hallucinations as innocent errors."

## Why This Case Matters

### The First Precedential Circuit-Level Standard

Unlike most AI-hallucination sanctions in this repository — which are one-off district-court orders or firm apology letters, like [Sullivan & Cromwell's](sullivan-cromwell-bankruptcy-hallucinations.md) emergency letter to a bankruptcy court — this is a **published, precedential opinion** from a U.S. Court of Appeals. It establishes binding guidance across the entire Ninth Circuit: personal review and verification of every citation is mandatory regardless of who or what drafted the brief, and the two-year disclosure requirement creates an enforceable, ongoing compliance obligation rather than a one-time penalty.

### Candor as an Independent Aggravating Factor

Most legal-hallucination cases in this repository turn on the fabrication itself. This case adds a distinct, compounding dimension: the attorneys' **repeated, specific denials** that AI was involved — under direct questioning, at oral argument — became an independent factor the court weighed in setting the severity of discipline. The lesson generalizes beyond AI: covering up an error after the fact carries its own cost, separate from the cost of the original error.

## Technical Analysis

### Fabrication vs. Misrepresentation

The court's opinion draws a technically useful distinction not always made explicit in hallucination discussions: a **fabrication** is a citation to a case that does not exist at all (like the two nonexistent Ninth Circuit cases here); a **misrepresentation** or **inaccuracy** is a citation to a *real* case that does not actually say what the brief claims it says. The court flagged the latter as arguably more dangerous precisely because it survives a shallow "does this case exist?" check — the kind of check an attorney might perform without actually reading the case.

### Verification Requires Reading, Not Just Existence-Checking

The opinion states that a competent, diligent attorney must "read and reason" about cited authority, not merely confirm that a citation resolves to a real document. This raises the bar above the simplest mitigation (checking that a citation exists in a legal database) to a substantive requirement: the signing attorney must have actually understood what the cited case holds and confirmed it supports the argument being made.

## Root Cause Analysis

### AI-Specific Failures

1. **Unretrieved, unverified citation generation**: The unauthorized AI tool used by the unlicensed brief writer produced citations without grounding them in an actual case-law database.
2. **Plausible misrepresentation of real authority, not just fabrication**: Some of the tool's output misstated the holdings of real cases — a failure mode the court specifically identified as harder to catch than pure fabrication.

### Process Failures

1. **Unauthorized AI use by unsupervised staff**: An unlicensed law-school graduate at the firm used AI tools without the signing attorneys' knowledge or a review process designed to catch it.
2. **No personal verification before signing**: Neither Sethi nor Rounds appears to have personally read and confirmed the cited authority before filing, despite being the attorneys of record.
3. **Initial denial instead of disclosure**: When first questioned, the attorneys denied AI involvement rather than investigating and disclosing — compounding the original error with a candor failure that the court treated as an aggravating factor in its own right.

## Lessons Learned

### For Attorneys and Firms Using (or Supervising Staff Who Use) AI

1. **Verification is the attorney's non-delegable duty**: Responsibility for a filed brief's accuracy rests with the signing attorney regardless of who — or what — drafted it, including unsupervised junior staff using unauthorized tools.
2. **Distinguish "does this case exist" from "does this case say what I claim it says"**: A citation-existence check alone is not verification. Read the cited authority and confirm it supports the argument.
3. **Disclose immediately if AI involvement is suspected**: The court explicitly rewards prompt, honest disclosure and penalizes denial. If asked whether AI was used and you are not certain, investigate before answering rather than denying by default.

### For Courts

1. **Precedential orders create durable, ongoing obligations**: A published opinion with a multi-year disclosure requirement is a stronger deterrent than a one-time monetary sanction, because it creates continuing accountability rather than a single penalty that ends once paid.
2. **Candor at oral argument is a distinct axis courts can and should weigh**: Treating denial-then-admission as an aggravating factor, separate from the underlying hallucination, gives courts a tool to address cover-up behavior specifically.

## References

- **Ninth Circuit (primary source)**: [*Lnu v. Blanche*, No. 24-4790 (9th Cir. June 3, 2026)](https://cdn.ca9.uscourts.gov/datastore/opinions/2026/06/03/24-4790.pdf)
- **Reason / Volokh Conspiracy**: [Ninth Circuit on AI Hallucinations](https://reason.com/volokh/2026/06/03/ninth-circuit-on-ai-hallucinations/)
- **Let's Data Science**: [Ninth Circuit Warns Against AI Hallucinations in Filings](https://letsdatascience.com/news/ninth-circuit-warns-against-ai-hallucinations-in-filings-36f1f0f6)
