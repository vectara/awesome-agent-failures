# Personal AI Assistant Gets Its User Banned From Resy - September 2026

## Incident Overview

**Organizations**: Instinct (personal AI assistant); Resy, owned by American Express (platform); JC Bahr-de Stefano, a New York venture capitalist (user)<br>
**Date**: Polling from Friday, September 4, 2026; account deactivated Sunday, September 6; reinstated by Tuesday, September 8, 2026<br>
**Failure Mode**: [Verification & Termination](../failure-modes/verification-termination.md)<br>
**Impact**: The user's Resy account was deactivated and all of its future reservations were canceled. The account was reinstated two days later with a warning that a repeat could close both the Resy account and his American Express cards. No harm to Resy or the restaurant was reported.<br>
**Technology**: Instinct, an invite-only assistant that users talk to by text message; the underlying model is not reported

**Sources**: [The user's posts on X][ban-post] for the activity log, the deactivation email, and the reinstatement; [Business Insider via Yahoo][bi] for the timeline and American Express's statement; [CNN via KEYT][cnn] for the user's account of his instructions.

## What Happened

Bahr-de Stefano connected Instinct to his Resy account to get a table at 4 Charles Prime Rib, a West Village steakhouse whose reservations sell out within seconds ([Business Insider][bi]). He told [CNN][cnn] he gave it a goal, not a method: "I didn't lay out explicit actions to take" and "I just said, 'I want you to do this. Go do it.'" When it couldn't find a table, he encouraged it to keep going and turned to booking somewhere else himself.

On Sunday, September 6, he found he couldn't log in. Resy's email, which he [posted on X][ban-screenshot], said the account "displayed behavior in violation of Resy's Terms of Service" and that Resy had "deactivated the account and canceled all future reservations associated with this email address."

He then asked Instinct for its activity log and [posted its answer][ban-post]:

> "Total: roughly 200 API requests per hour, around the clock
>
> 1. 4 Charles availability sweep every 10 minutes, 24/7 since Friday morning - each run hit their API ~17-19 times (one per date in a 21-day window). ~100-115 calls/hour
> 2. Every morning at 9am drop time, a 2.5-minute burst polling every 0.4 seconds - 200-375 requests per drop
> 3. Occasional session-token refreshes"

According to [Business Insider][bi], he did not know the agent was doing this. He wrote that the ban was "Totally fair action from Resy."

On September 8 he [posted][reinstated-post] that the account was back, quoting Resy's warning that "any future instances of misuse may result in a permanent termination of your Resy account(s) & cancelation of all associated AmEx Card products." An American Express spokesperson told Business Insider that Resy "does not currently permit unapproved third-party bots or agents" to access the platform and that "unapproved automated activity can introduce risks to the platform." Resy declined to comment on his case.

### A similar case

In August, tech entrepreneur Brian Distelburger [wrote on X][distel-post] that his account was suspended after a subagent he built on Hermes to watch Resy for openings hit the site too often. In his words: "I never set a rate limit because honestly it didn't occur to me." Resy reinstated his account as a "one time courtesy reinstatement." CNN also reports this ban.

## Root Cause Analysis

The request figures come from the agent's own summary of its activity log, as posted by the user. They are not Resy's measurements, and the itemized lines (about 100-115 calls per hour plus up to 375 per morning burst) add up to less than the "roughly 200" total the agent reported. The reporting does not establish what retry limits, request budgets, or stopping conditions Instinct had, whether it asked before starting a 24/7 polling job, or how it accessed Resy. No statement from Instinct about the incident was found.

### AI Agent Failures

1. **Sustained unsuccessful polling**: After the user encouraged it to keep trying, the agent polled around the clock for about two days until the account was deactivated. Whether it had an end date or another stopping condition is unknown.
2. **Frequent automated requests**: The agent's posted summary describes repeated availability sweeps and a 0.4-second burst at release time. The reporting does not establish who configured that frequency or whether a request budget existed.
3. **Platform rules violated**: Resy prohibits unapproved bots or agents, and its email cited a Terms of Service violation when deactivating the account. The reporting does not show whether the agent checked those rules or considered the risk to the account.
4. **Silent long-running work**: The user learned what the agent had been doing only after the ban, by asking for the log.

### Process Failures

1. **Safeguards remain unknown**: The reporting does not establish Instinct's product-level request caps or stopping conditions. Missing or ineffective safeguards are possible explanations, not validated causes.
2. **Consequences fall on the user**: The account, its existing reservations, and, under Resy's warning, his credit cards were at risk. The agent bore none of that.
3. **A similar outcome in another product**: The Hermes user explicitly reported setting no rate limit. That supports a missing-limit explanation for his case; it does not establish Instinct's configuration.

## Lessons Learned

These are recommendations drawn from the incidents, rather than claims about what Instinct or Resy have changed.

1. **Give recurring tasks a budget and an end**: Maximum request rate, maximum duration, and a point at which the agent reports back instead of continuing.
2. **Show the plan before polling a third party**: "I'll check every 10 minutes for 21 days of dates, and every 0.4 seconds at 9am" is something a user would have stopped. Bahr-de Stefano told Business Insider he will now ask Instinct to outline its plan first.
3. **Check the platform's automation rules before acting on a user's account**: If a site prohibits unapproved agents, the agent should say so rather than proceed.
4. **Use sanctioned channels where they exist**: According to the American Express spokesperson, Resy offers reservations through its ChatGPT and Claude integrations.

## References

- **JC Bahr-de Stefano on X**: [Activity log post, September 6, 2026](https://x.com/jbahrdestefano/status/2096676801204404604); [Resy deactivation email](https://x.com/jbahrdestefano/status/2096670445063831899); [Reinstatement, September 8, 2026](https://x.com/jbahrdestefano/status/2097373015642099887)
- **Brian Distelburger on X**: [Hermes subagent ban, August 17, 2026](https://x.com/bdistel/status/2089421994450170039)
- **Business Insider (via Yahoo)**: [This VC used AI to try to snag a restaurant reservation. Resy wasn't having it](https://tech.yahoo.com/ai/deals/articles/vc-used-ai-try-snag-164417524.html)
- **CNN (via KEYT)**: [Now AI is trying to gobble up dinner reservations](https://keyt.com/news/money-and-business/cnn-business-consumer/2026/09/23/now-ai-is-trying-to-gobble-up-dinner-reservations/)
- **Upstarts Media**: [Inside the invite-only app for booking New York's hottest restaurant tables](https://www.upstartsmedia.com/p/inside-the-invite-only-app-for-booking)

[ban-post]: https://x.com/jbahrdestefano/status/2096676801204404604
[ban-screenshot]: https://x.com/jbahrdestefano/status/2096670445063831899
[reinstated-post]: https://x.com/jbahrdestefano/status/2097373015642099887
[distel-post]: https://x.com/bdistel/status/2089421994450170039
[bi]: https://tech.yahoo.com/ai/deals/articles/vc-used-ai-try-snag-164417524.html
[cnn]: https://keyt.com/news/money-and-business/cnn-business-consumer/2026/09/23/now-ai-is-trying-to-gobble-up-dinner-reservations/
