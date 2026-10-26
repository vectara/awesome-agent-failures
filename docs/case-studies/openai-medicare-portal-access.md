# OpenAI Agent Accesses Australia's Medicare Statistics Portal - June 2026

## Incident Overview

**Organizations**: OpenAI (agent operator); Services Australia (portal operator); Australian Signals Directorate (forensic investigation)<br>
**Date**: Access June 18, 2026; OpenAI detection August 11; Services Australia notified September 10; public disclosure September 24, 2026 (Australian time); OpenAI apology September 29; parliamentary testimony October 6, 2026<br>
**Failure Mode**: [Scope & Containment Violation](../failure-modes/scope-containment-violation.md)<br>
**Impact**: Non-public files on the Medicare Statistics Reporting Service were accessed, according to the Prime Minister. OpenAI says the data included aggregate health statistics and internal file names, and found no evidence of patient records being accessed. The portal, a standalone statistics service, has been taken offline. How much of the access was non-public is disputed.<br>
**Technology**: An unnamed "experimental, internal-only" OpenAI model with live internet access during internal training and evaluation

**Sources**: [Prime Minister's press conference transcript][pm-transcript] and [ABC News][abc-0924] for the government's account and timeline; [The Record][record-0924] for OpenAI's initial statement; [The Record's follow-up][record-doubts] for archive evidence disputing the "hack" framing; [The Nightly][nightly] for OpenAI's September 29 apology; [The Medical Republic][medrepublic] for OpenAI's testimony to parliament.

## What Happened

According to [OpenAI's apology as reported by The Nightly][nightly], the model was asked to research government spending on skin medicines in Victoria. It had difficulty finding the information, "discovered a way to gain non-public access to the service", and "used this access to review technical system information and source code related to the service" while still trying to find the requested figures.

The Prime Minister described the portal pushing back. From the [press conference transcript][pm-transcript]:

> "There were blocks clearly which were coming back telling the AI agent, no. The AI agent found a way around those blocks."

He said the agent "accessed public and non-public information within the portal" and "engaged in writing files as well to the internal server." [The Record][record-0924] attributes the file-writing claim to Services Australia. Neither the government nor OpenAI has described the technique. At the [October 6 parliamentary hearing][medrepublic], OpenAI chief strategy officer Jason Kwon called it "a non-public access point" and said the method was "not super sophisticated."

### Disputed technical account

A day after the announcement, [The Record][record-doubts] checked archived versions of the portal and reported:

- A March 2025 upgrade added a login page but also enabled guest access, which signs any visitor in automatically.
- The portal's own `SetupEnvironment.js` sent statistics visitors to an unauthenticated guest endpoint, and also exposed internal file names and server paths.
- The portal has written date-stamped chart images to a temporary folder on every chart request since at least 2018, which may explain the "files written."

If that holds, the agent may have followed the site's own code rather than bypassing a control. Neither OpenAI nor Services Australia has released logs, and OpenAI declined to answer The Record's technical questions. OpenAI's own wording ("non-public access", "source code") still describes access beyond what the task needed.

### Detection and notification

OpenAI found the activity on August 11 during a review of "misaligned model activity" ([ABC News][abc-0924]). [ABC News][abc-0926] reports that review grew out of the investigation into the [Hugging Face intrusion](openai-huggingface-agent-intrusion.md). OpenAI said it was validating the facts before notifying ([The Record][record-0924]). The [ABC timeline][abc-0924] then runs:

- **September 1**: Sam Altman meets Deputy Prime Minister Richard Marles; the incident is not raised. Kwon later said Altman did not know of it ([The Medical Republic][medrepublic]).
- **September 10**: OpenAI emails a public mailbox used to report security weaknesses.
- **September 11**: Services Australia sees the email; it notifies the Australian Signals Directorate on September 15.
- **September 22**: First technical exchange between OpenAI and Services Australia.
- **September 24**: The Prime Minister, in New York, calls Altman and announces the incident. [The Record][record-0924] places the call on Wednesday, which is September 23 in New York.

Asked whether the government would have known without OpenAI's email, Kwon said it was "possible that it would not have been discovered" ([The Medical Republic][medrepublic]).

### Other sites

Agents also contacted the Australian Institute of Health and Welfare (AIHW), the NSW Bureau of Crime Statistics and Research, and the Victorian Department of Health. The government said only the Medicare portal had confirmed unauthorized access ([Australian Cyber Security Magazine][acsm]); OpenAI's apology says all four were accessed in ways its models were not authorized to ([The Nightly][nightly]).

## Root Cause Analysis

Sources do not establish the technique, the agent's network permissions or full instructions, what the written files were, or whether guest access made the "non-public" data reachable to any visitor. OpenAI publishes [misalignment reports](https://alignment.openai.com/misalignment-reports/) with fictionalized names; none can be matched to this incident from public information.

### AI Agent Failures

1. **Persistence past a refusal**: Per the Prime Minister and OpenAI, the agent kept looking for another route after the portal said no.
2. **Reaching beyond the data needed**: A question about medicine spending did not require reviewing system information or source code.

### Process Failures

1. **Live internet access during training and evaluation**: The model could reach a production government system. OpenAI says it has since strengthened research security and monitoring ([The Medical Republic][medrepublic]).
2. **Retrospective detection**: The access was found nearly two months later in a review, not by real-time monitoring.
3. **Slow, low-visibility notification**: A further month passed before OpenAI sent an email to a general mailbox. Kwon conceded OpenAI should have contacted ministers directly ([The Medical Republic][medrepublic]).

## Company and Government Response

- **OpenAI**: Apologized on September 29, saying it "should have shared preliminary findings sooner" ([The Nightly][nightly]). At the hearing Kwon cited a pause on training some frontier models, a local task force expected to finish by the end of 2026, and direct notification of affected parties in future ([The Medical Republic][medrepublic]).
- **Australian government**: Set up a taskforce led by the Department of the Prime Minister and Cabinet, is seeking advice on whether offences occurred and whether to refer the matter to the Australian Federal Police, and referred the incident to the Joint Select Committee on Artificial Intelligence ([PM transcript][pm-transcript]). The portal, a legacy system already scheduled for retirement, is now offline ([Australian Cyber Security Magazine][acsm]). As of October 26, 2026, we found no published taskforce findings or outcome of the legal advice.

## Lessons Learned

These are recommendations drawn from the incident, rather than claims that OpenAI has implemented each measure.

1. **No live internet in training without an allowlist**: A research task should reach only the sources it needs, read-only.
2. **Treat a refusal as the answer**: An error or access denial should end the attempt, and "data not available" should be a scoreable result.
3. **Notify the victim promptly and directly**: Call the affected operator's security team when misuse is suspected, then confirm details, rather than validating for a month and emailing a public mailbox.
4. **Publish logs when making claims**: Without logs, neither "hacked" nor "followed the site's own guest link" can be confirmed.

## References

- **Prime Minister of Australia**: [Press conference - New York](https://www.pm.gov.au/media/press-conference-new-york)
- **ABC News**: [OpenAI hacked Medicare portal, Prime Minister Anthony Albanese says](https://www.abc.net.au/news/2026-09-24/ai-agent-accessed-australian-government-site-pm-says/107189078)
- **ABC News**: [OpenAI says dozens affected by rogue agents amid new detail about Australian incidents](https://www.abc.net.au/news/2026-09-26/openai-review-rogue-agents-australia-medicare-hack/107199074)
- **The Record**: [OpenAI agent breached Australian government health website, Albanese says](https://therecord.media/openai-australia-health-breach)
- **The Record**: [Doubts grow over claims OpenAI agent hacked Australian Medicare portal](https://therecord.media/openai-australia-breach-cyber)
- **Australian Cyber Security Magazine**: [OpenAI agent contacted four Australian government websites as Canberra launches security review](https://australiancybersecuritymagazine.com.au/openai-agent-contacted-four-australian-government-websites-as-canberra-launches-security-review/)
- **The Nightly**: [OpenAI issues lengthy apology statement as government fumes over AI agent's website breach](https://thenightly.com.au/society/technology/openai-issues-lengthy-apology-statement-as-government-fumes-over-ai-agents-website-breach-c-22940558)
- **The Medical Republic**: ['We are sorry': OpenAI apologises for Medicare breach](https://www.medicalrepublic.com.au/we-are-sorry-openai-apologises-for-medicare-breach/129694)

[pm-transcript]: https://www.pm.gov.au/media/press-conference-new-york
[abc-0924]: https://www.abc.net.au/news/2026-09-24/ai-agent-accessed-australian-government-site-pm-says/107189078
[abc-0926]: https://www.abc.net.au/news/2026-09-26/openai-review-rogue-agents-australia-medicare-hack/107199074
[record-0924]: https://therecord.media/openai-australia-health-breach
[record-doubts]: https://therecord.media/openai-australia-breach-cyber
[acsm]: https://australiancybersecuritymagazine.com.au/openai-agent-contacted-four-australian-government-websites-as-canberra-launches-security-review/
[nightly]: https://thenightly.com.au/society/technology/openai-issues-lengthy-apology-statement-as-government-fumes-over-ai-agents-website-breach-c-22940558
[medrepublic]: https://www.medicalrepublic.com.au/we-are-sorry-openai-apologises-for-medicare-breach/129694
