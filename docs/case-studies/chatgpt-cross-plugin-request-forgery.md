# ChatGPT Plugins Cross Plugin Request Forgery - May 2023

## Incident Overview

**Organizations**: OpenAI (ChatGPT plugins platform); Zapier and WebPilot (plugins used in the demo); Johann Rehberger, Embrace The Red (researcher)<br>
**Date**: Proof of concept shared May 19, 2023; write-up published May 28, 2023<br>
**Failure Mode**: [Tool Use](../failure-modes/tool-use.md)<br>
**Impact**: Researcher proof of concept, not an observed attack. A webpage read through one plugin made ChatGPT read the user's latest email through the Zapier plugin and send a summary of it to an attacker-controlled URL. Zapier added an authenticated confirmation step. No victims were reported.<br>
**Technology**: ChatGPT with plugins (beta), WebPilot browsing plugin, Zapier plugin with OAuth access to Gmail

**Sources**: [Rehberger's write-up][embrace] for the attack and Zapier's fix; [Simon Willison's post][willison] for the plugins and data involved; [Zapier's sunset notice][zapier-sunset] for the end of plugins.

## What Happened

In 2023, ChatGPT plugins let the model call third-party APIs during a conversation. A user could enable several at once, for example a browsing plugin and Zapier, which the user had connected to email, Drive, and Slack through OAuth. [Source: plugin setup][embrace].

Rehberger first noticed that a plugin response could make ChatGPT invoke a different plugin. In an earlier demo, text on a website caused ChatGPT to run a flight search through the Expedia plugin without the user asking for it. [Source: Expedia demo][embrace].

He then built a data-exfiltration proof of concept:

1. The user asks ChatGPT to read a webpage with the WebPilot plugin. The page contains hidden instructions.
2. Following those instructions, ChatGPT uses the Zapier plugin to retrieve the user's latest email, summarizes it, and URL-encodes the summary.
3. ChatGPT appends the summary to an attacker-controlled URL and loads it with WebPilot, which sends the data to the attacker's server.

[Source: proof-of-concept steps][embrace]. Willison described it as "the first working proof of concept I've seen of a prompt injection attack against ChatGPT Plugins that demonstrates exfiltration of private data," and noted that Rehberger did not publish the injection text. [Source: Willison's summary][willison].

Rehberger called this a confused deputy attack, or Cross Plugin Request Forgery. His conclusion: "Random webpages and data will hijack your AI, steal your stuff and spend your money." [Source: write-up][embrace]. No purchases were made in the demos; buying groceries appears only as a hypothetical.

### Response

Zapier "promptly mitigated the problem on their side by adding an authenticated confirmation requirement." Rehberger stressed that the confirmation must be authenticated, because otherwise an attacker could instruct ChatGPT to confirm. The write-up does not describe any fix from OpenAI and hopes ChatGPT will offer a generic one in the future. [Source: vendor response][embrace].

OpenAI later wound down the plugins beta: new plugin conversations were disabled by March 19, 2024, and existing ones stopped working on April 9, 2024, as OpenAI moved to GPTs. [Source: Zapier's sunset notice][zapier-sunset]. We found no source tying that decision to this vulnerability.

## Root Cause Analysis

The following analysis draws on [Rehberger's write-up][embrace]. The sources do not establish whether anyone used this technique against real users, whether other plugins were exploitable in the same way, or what OpenAI changed in response.

### AI Agent Failures

1. **Data treated as instructions**: Text fetched from an untrusted webpage was followed as if the user had written it.
2. **One plugin's output triggered another plugin**: Nothing stopped content returned by WebPilot from causing a call to Zapier, which held the user's email access.
3. **Private data sent to an arbitrary URL**: The same browsing tool that read the attack page could also send data out by loading a crafted URL.

### Process Failures

1. **No confirmation for sensitive actions**: Before Zapier's fix, reading email through the plugin required no user confirmation.
2. **Fixes left to each plugin vendor**: Zapier fixed its own plugin. The write-up does not describe a platform-level control for cross-plugin calls.

## Lessons Learned

These are recommendations drawn from the incident, not claims about what OpenAI or plugin vendors implemented.

1. **Treat tool output as untrusted**: Content from a webpage or API response should not be able to start new tool calls without the user's approval.
2. **Confirm sensitive actions outside the model**: Reading private data or sending messages should require a confirmation the model cannot click on the user's behalf.
3. **Limit outbound requests after reading private data**: A session that has read email should not be able to load arbitrary URLs.
4. **Don't rely on each integration to fix it**: Platform-level controls cover every plugin; per-vendor fixes cover one.

## References

- **Embrace The Red**: [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./)
- **Simon Willison**: [Let ChatGPT visit a website and have your email stolen](https://simonwillison.net/2023/May/19/chatgpt-prompt-injection/)
- **Zapier**: [Sunsetting the Zapier ChatGPT plugin: what you need to know](https://help.zapier.com/hc/en-us/articles/24785309335565)

[embrace]: https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./
[willison]: https://simonwillison.net/2023/May/19/chatgpt-prompt-injection/
[zapier-sunset]: https://help.zapier.com/hc/en-us/articles/24785309335565
