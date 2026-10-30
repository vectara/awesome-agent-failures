# Taco Bell AI Drive-Thru Viral Failures - 2025

## Incident Overview

**Company**: Taco Bell (Yum! Brands)<br>
**Date**: Voice AI rolled out from 2023; viral clips and Taco Bell's reassessment reported late August 2025<br>
**Failure Mode**: [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)<br>
**Impact**: Viral videos viewed millions of times; Taco Bell said it would rethink where it uses voice AI and coach staff on when to step in<br>
**Technology**: Voice AI ordering at more than 500 U.S. drive-throughs, built on Yum's Byte platform with Nvidia tools according to Restaurant Technology News

**Source**: [BBC][bbc], [Restaurant Technology News][rtn], [Jalopnik][jalopnik].

## What Happened

Since 2023, Taco Bell has introduced voice AI ordering at more than 500 U.S. locations, "with the aim of reducing mistakes and speeding up orders" [Source: BBC][bbc]. In August 2025, customer videos of the system going wrong spread on social media.

### The 18,000 Water Cups Order

In one clip, "a customer seemingly crashed the system by ordering 18,000 water cups" [Source: BBC][bbc]. The Wall Street Journal described the order as trolling [Source: Jalopnik][jalopnik]. The sources don't say whether the order was entered or rejected, only that the system appeared to fail.

### The Drink Loop

An Instagram clip viewed over 21.5 million times "shows a man ordering 'a large Mountain Dew' and the AI voice continually replying 'and what will you drink with that?'" BBC describes the customer getting "increasingly angry as the AI repeatedly asked him to add more drinks to his order" [Source: BBC][bbc].

This is the failure that fits goal misinterpretation: the system kept following its drink prompt after the customer had already ordered a drink.

## Root Cause Analysis

Neither Taco Bell nor Yum has published a technical cause. The clips show the outcomes:

1. **Following a script past its purpose**: The drink prompt repeated even though the order already contained a drink.
2. **No graceful handling of absurd input**: A prank quantity appeared to break the system rather than being flagged or handed to staff.
3. **Unattended operation at busy times**: Taco Bell's own response (below) is to have staff monitor and step in, particularly when restaurants are busy.

## Company Response

Taco Bell's Chief Digital and Technology Officer Dane Mathews told the Wall Street Journal: "Sometimes it lets me down, but sometimes it really surprises me." He said the company was "learning a lot", and "We'll help coach teams on when to use voice AI and when it's better to monitor or step in." BBC reports he said there are times when humans are better placed to take orders, especially when restaurants get busy [Source: BBC][bbc].

Taco Bell also said two million orders had been processed successfully with the voice AI since its introduction [Source: BBC][bbc].

## Lessons Learned

These are recommendations drawn from the incident, rather than claims about what Taco Bell has implemented.

1. **Check the order state before prompting**: An upsell prompt should depend on what is already in the order.
2. **Flag implausible requests**: Quantities far outside normal orders should go to a human, not break the system.
3. **Define when staff take over**: Decide in advance when a human monitors or takes the order, such as peak hours or repeated customer corrections.

## Related Incidents

- [McDonald's AI Drive-Thru Partnership Failure](mcdonalds-ai-drive-thru-failure.md): McDonald's ended its IBM voice-ordering test in 2024 after videos of wrong orders, including bacon on ice cream and hundreds of dollars of McNuggets.

## References

- **BBC**: [Taco Bell rethinks AI drive-through after man orders 18,000 waters][bbc] (August 29, 2025)
- **Restaurant Technology News**: [How Taco Bell's AI Drive-Thru Became a Viral Sensation for the Wrong Reasons][rtn] (August 30, 2025)
- **Jalopnik**: [18,000 Waters In One Order Causes Taco Bell To Pause AI Drive-Through Rollout][jalopnik] (September 2, 2025)
- **Wall Street Journal**: [Taco Bell Rethinks Future of Voice AI at the Drive-Through](https://www.wsj.com/articles/taco-bell-rethinks-future-of-voice-ai-at-the-drive-through-72990b5a) (paywalled; not checked directly, quotes taken from BBC)

[bbc]: https://www.bbc.com/news/articles/ckgyk2p55g8o
[rtn]: https://restauranttechnologynews.com/2025/08/how-taco-bells-ai-drive-thru-became-a-viral-sensation-for-the-wrong-reasons/
[jalopnik]: https://www.jalopnik.com/1956939/taco-bell-drive-through-18000-waters/
