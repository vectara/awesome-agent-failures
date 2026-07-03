# Mississippi Federal Trial Cancelled After Both Sides Cite AI-Hallucinated Cases - June 2026

## Incident Overview

**Court**: U.S. District Court for the Northern District of Mississippi<br>
**Case**: *Withers v. City of Aberdeen* (contract dispute over unpaid legal fees)<br>
**Judge**: Senior U.S. District Judge Sharion Aycock<br>
**Date**: Sanctions Order issued June 8, 2026; widely reported June 11, 2026<br>
**Failure Mode**: [Tool Hallucination](../failure-modes/tool-hallucination.md)<br>
**Impact**: Trial cancelled; all four attorneys in the case removed; two barred from practicing before the court for two years; personal fines of $1,000-$3,500 per attorney<br>
**Technology**: "First Drafts" AI legal-drafting platform (plaintiff's counsel) and a separate, unnamed AI legal-research tool (defense counsel)

## What Happened

Attorney Tom Withers III sued the City of Aberdeen, Mississippi, over unpaid legal fees from work he had performed as outside counsel in a separate matter. As the case approached trial, Judge Sharion Aycock discovered that filings from **both sides** of the dispute cited case law that did not exist.

Wilson, a solo practitioner representing the plaintiff, used an AI-powered legal-drafting platform called First Drafts to prepare a motion. It contained citations to two cases that do not exist. Williams, a partner at the firm Christian & Small representing the defense, used a different AI research tool that produced four fabricated cases across two separate filings. Neither legal team's review process caught the other's fabrications — or their own.

### The Judge's Response

Rather than sanctioning only the attorneys who filed the specific hallucinated motion, Judge Aycock removed all four attorneys involved — two from each side — from the case entirely, and cancelled the scheduled trial to allow both parties to retain new counsel. Wilson was fined $2,500; Williams was fined $3,500. Two additional attorneys connected to the filings, Shauncey Hunter Ridgeway and Mark McClinton, were each fined $1,000 and removed from the case. Wilson and Williams were barred from appearing before the Northern District of Mississippi for two years.

Judge Aycock was explicit about where the duty lies:

> A lawyer's duty to verify the accuracy of their own work "is absolute" and "cannot be outsourced to technology."

## Why This Case Matters

### The Adversarial System's Usual Safety Net Failed

Every prior legal-hallucination case in this repository — [ChatGPT Lawyer Sanctions](chatgpt-lawyer-sanctions.md), [Sullivan & Cromwell](sullivan-cromwell-bankruptcy-hallucinations.md) — involves one side's fabrications being caught, whether by opposing counsel, a court's own review, or a journalist. This case is different: opposing counsel did not catch each other's fabricated citations, because both sides had independently outsourced verification to AI tools that were equally capable of inventing case law. The assumption that an adversarial system provides a built-in check against factual error in filings does not hold when both parties made the same category of mistake using the same category of tool.

### First Documented Case of Simultaneous Sanctions on Opposing Counsel

According to reporting on the order, this is the first instance of a federal court imposing sanctions on both sides of a case simultaneously for the same class of AI misuse — a marker that the volume of AI-hallucinated filings has grown large enough that independent, unrelated fabrications are now colliding within the same case.

## Technical Analysis

### Two Different Tools, One Failure Pattern

Wilson's and Williams' fabrications came from two different AI products — a purpose-built legal-drafting platform and a general AI legal-research tool — yet both produced the same failure signature: syntactically well-formed citations pointing to cases that were never decided. This reinforces a pattern already documented across this repository's legal-hallucination cases: the specific vendor or tool matters less than whether a retrieval-and-verification step sits between generation and filing. Neither tool here appears to have had one, or if it did, neither attorney used it.

### Why Neither Side Caught the Other's Errors

Opposing counsel's review of a filing is typically focused on substance and strategy — whether the citations support the argument being made — not exhaustive verification that each cited case actually exists. Case-existence verification is normally assumed to have already happened during the drafting attorney's own review. When that internal check is skipped by both parties, there is no remaining human step in a typical adversarial workflow that would catch it before it reaches the judge.

## Root Cause Analysis

### AI-Specific Failures

1. **No retrieval-grounding in either tool**: Both First Drafts and the unnamed research tool used by defense counsel generated citations as plausible text rather than verified lookups against an authoritative case-law database.
2. **Uniform confidence regardless of accuracy**: Fabricated citations were presented with the same formatting and tone as real ones in both tools, giving the filing attorneys no internal signal to investigate further.

### Process Failures

1. **No independent citation-verification step on either side**: Neither legal team ran filings through a citation-checking tool (e.g., Westlaw/Lexis validation) before submission.
2. **Reliance on adversarial review as an implicit safety net**: Both teams' processes appear to have assumed that if their own review missed something, opposing counsel's response would catch it — an assumption that fails when both sides have the same blind spot.

## Court's Broader Message

Judge Aycock's order treated the misconduct as a professional-responsibility failure rather than a technology failure, echoing language used in other 2026 AI-hallucination sanctions: the duty to verify a filing's accuracy belongs to the signing attorney, regardless of what tool was used to draft it, and that duty cannot be delegated to the tool itself.

## Lessons Learned

### For Litigators Using AI Drafting or Research Tools

1. **Never assume the other side will catch your errors**: The adversarial process is not a substitute for your own citation verification. If both sides skip it, nothing catches the mistake before the judge does.
2. **Verify every citation against a legal database before filing**: Regardless of which AI tool produced a citation — general-purpose or purpose-built for legal work — treat it as unverified until confirmed against Westlaw, Lexis, or an equivalent authoritative source.
3. **A tool marketed for legal work is not automatically retrieval-grounded**: "First Drafts" is a legal-drafting platform, and it still produced fabricated cases. Domain-specific branding is not a guarantee of factual grounding.

### For Courts

1. **Simultaneous cross-party sanctions may become more common**: As AI-drafting adoption increases on both sides of litigation, courts should expect — and have a framework ready for — cases where the same failure appears in filings from opposing parties.
2. **Case-wide remedies (removing all counsel, cancelling trial) may be necessary**: Sanctioning only the party whose fabrication was noticed first does not address the underlying problem if the opposing party's filings are equally compromised.

## References

- **Legal Cheek**: [US judge stops case after lawyers on both sides cite AI-hallucinated cases](https://www.legalcheek.com/2026/06/us-judge-stops-case-after-lawyers-on-both-sides-cite-ai-fabricated-cases/)
- **Mississippi Free Press**: [AI Hallucinations Prompt Mississippi Judge to Boot All Lawyers From Case](https://www.mississippifreepress.org/ai-hallucinations-prompt-mississippi-judge-to-boot-all-lawyers-from-case-for-blindly-relying-on-technology/)
