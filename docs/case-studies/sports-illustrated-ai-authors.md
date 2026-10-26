# Sports Illustrated Publishes Product Reviews Under Fake Authors - November 2023

## Incident Overview

**Organizations**: The Arena Group (Sports Illustrated and TheStreet publisher); AdVon Commerce (content vendor); Futurism (discovery)<br>
**Date**: Futurism report November 27, 2023; follow-up investigation of AdVon May 8, 2024<br>
**Failure Mode**: [Response Hallucination](../failure-modes/response-hallucination.md) (loose fit: AI-written text is alleged, not proven; the proven fabrication is of author identities)<br>
**Impact**: Product reviews ran on Sports Illustrated and TheStreet under invented writers whose headshots were listed for sale on an AI-generated headshot site. Arena removed the content and ended its AdVon partnership.<br>
**Technology**: AI-generated headshots (confirmed); AI-generated article text (alleged by anonymous sources, denied by Arena and AdVon)

**Sources**: [Futurism's original report][futurism-si] for the fake authors and Arena's statement; [Futurism's AdVon investigation][futurism-advon] for the vendor's AI tooling and the volleyball review edit history.

## What Happened

[Futurism][futurism-si] found Sports Illustrated product reviews bylined "Drew Ortiz," whose bio described an outdoorsy writer. Ortiz's headshot was listed on a site that sells AI-generated headshots, described as a "neutral white young-adult male." His profile was later replaced by "Sora Tanaka," a "fitness guru" whose headshot was listed on the same site. Articles were reattributed between personas without editor's notes.

The reviews read poorly. A volleyball buying guide said the sport "can be a little tricky to get into, especially without an actual ball to practice with." TheStreet, another Arena title, had the same pattern: writers with detailed bios and headshots from the same marketplace, rotated out over time with no AI disclosure.

Two anonymous sources involved in creating the content spoke to Futurism. One said there were many fake authors with invented bios. The other said: "The content is absolutely AI-generated." [Source: personas, headshots, and anonymous sources][futurism-si].

After Futurism contacted Arena, the authors and their articles were removed from Sports Illustrated without explanation. Arena then said the articles were product reviews licensed from AdVon Commerce, and that AdVon "has assured us that all of the articles in question were written and edited by humans." Arena acknowledged that AdVon "had writers use a pen or pseudo name in certain articles," said it did not condone this, and said it had ended the partnership. [Source: Arena's statement][futurism-si].

### The vendor

Futurism's [May 2024 investigation of AdVon][futurism-advon] described an internal AI tool called MEL. A training video showed a manager generating a bicycle review from a headline and Amazon links and telling workers to "just keep regenerating" until they had something usable. A Yoga Journal review by another fake SI writer, "Damon Ward," was labeled "AI" in AdVon's CMS. AdVon said it uses AI "responsibly in combination with human writers and editors" but that Sports Illustrated "was not one of those AI partners," and supplied Google Docs edit histories as proof of human authorship. One edit history shows a Sports Illustrated volleyball review of about 2,200 words written in five minutes. Futurism notes that AI output could have been pasted into the document. [Source: MEL, CMS label, edit histories, AdVon statements][futurism-advon].

### Later events at Arena

The Arena board terminated CEO Ross Levinsohn on December 11, 2023, citing "operational efficiency and revenue." An Arena representative declined to say whether this was related to the AI story. [Source: Levinsohn termination][levinsohn-exit]. In January 2024, Authentic Brands revoked Arena's Sports Illustrated license, citing "failure to pay its quarterly license fee," and Arena moved to lay off much of SI's staff. [Source: license revocation][license]. Neither source attributes these events to the fake-author story.

## Root Cause Analysis

The sources establish that the author identities were fabricated and the headshots came from an AI-generated image marketplace; AdVon did not dispute that the authors were fake. They do not establish that the Sports Illustrated article text was AI-generated. That rests on anonymous sources, writing quality, and an implausibly fast edit history. Futurism's direct evidence of AI-labeled text is a Yoga Journal article, not an SI one. The sources also don't show what review, if any, Arena applied to licensed content before publishing it.

### Failures

1. **Fabricated authorship**: Readers were told named people with expertise and backstories wrote the reviews. Those people did not exist. This holds whether a human or a model wrote the text.
2. **No disclosure of how content was produced**: Personas were rotated and articles reattributed without editor's notes.
3. **Outsourced content published under the masthead**: Arena published vendor content under the Sports Illustrated brand and relied on the vendor's assurance about how it was produced.

### Failure mode fit

This is not a case of a model inventing facts from source data. The established fabrication is of authors, done by the publishing operation, so it fits better as an institutional and disclosure failure than as response hallucination in the technical sense.

## Lessons Learned

These are recommendations drawn from the incident, not claims about what Arena or AdVon changed.

1. **Disclose AI use and synthetic personas**: If a byline isn't a real person, say so on the page.
2. **Verify vendor content before publishing it under your brand**: A vendor's assurance that "humans wrote it" is not a review process. Ask for the production workflow, not after-the-fact edit histories.
3. **Treat reattribution as a correction**: Changing a byline on a published article should leave a visible note.

## References

- **Futurism**: [Sports Illustrated Published Articles by Fake, AI-Generated Writers](https://futurism.com/sports-illustrated-ai-generated-writers)
- **Futurism**: [Meet AdVon, the AI-Powered Content Monster Infecting the Media Industry](https://futurism.com/advon-ai-content)
- **Awful Announcing**: [Arena Group terminates CEO Ross Levinsohn (December 11, 2023)](https://awfulannouncing.com/arena-group/sports-illustrated-ross-levinsohn-exit.html)
- **OPB (NPR)**: ['Sports Illustrated' to lay off most of its staff amid severed licensing deal](https://www.opb.org/article/2024/01/19/sports-illustrated-to-lay-off-most-of-its-staff-amid-severed-licensing-deal/)

[futurism-si]: https://futurism.com/sports-illustrated-ai-generated-writers
[futurism-advon]: https://futurism.com/advon-ai-content
[levinsohn-exit]: https://awfulannouncing.com/arena-group/sports-illustrated-ross-levinsohn-exit.html
[license]: https://www.opb.org/article/2024/01/19/sports-illustrated-to-lay-off-most-of-its-staff-amid-severed-licensing-deal/
