# NYC MyCity Chatbot Tells Businesses to Break the Law - March 2024

## Incident Overview

**Organizations**: New York City (operator; Office of Technology and Innovation); Microsoft (Azure AI services); The Markup and THE CITY (discovery)<br>
**Date**: Launched October 2023; reported March 29, 2024; kept online by Mayor Adams April 2, 2024; taken down by February 4, 2026<br>
**Failure Mode**: [Response Hallucination](../failure-modes/response-hallucination.md)<br>
**Impact**: An official city chatbot told business owners that illegal practices were legal, covering housing discrimination, tip theft, cashless stores, and firing workers who complain about sexual harassment. Reporters could not determine whether anyone acted on the answers.<br>
**Technology**: MyCity business chatbot built on Microsoft's Azure AI services (large language model)

**Sources**: [The Markup's investigation][markup-2024] for the original testing; [AP via NY1][ap] and [Reuters via Daily Maverick][reuters] for the city's response and later answers; [The Markup and THE CITY, 2026][markup-2026] for the shutdown.

## What Happened

Mayor Eric Adams announced the chatbot in October 2023 as part of the MyCity portal. According to [The Markup][markup-2024], the announcement said it would "access trusted information from more than 2,000 NYC Business web pages," and the bot's page said it used information published by the Department of Small Business Services.

Five months later, The Markup's testing found answers that contradicted city law, including:

- **Housing vouchers**: Landlords don't have to accept Section 8 vouchers or tenants on rental assistance. Source-of-income discrimination is illegal in NYC, with a narrow exception for small buildings where the landlord or their family lives. The bot gave this answer to all ten Markup staffers who asked; it gave the correct answer to one other reporter.
- **Tips**: Employers can take a cut of workers' tips.
- **Cash**: Restaurants can go cash-free. A 2020 city council law requires businesses to accept cash.
- **Tenants and rent**: It was legal to lock out a tenant, and there were "no restrictions on the amount of rent" a landlord can charge, which is false for rent-stabilized units.
- **Scheduling and funerals**: No rules govern notifying staff of schedule changes, and funeral homes can conceal prices, which the FTC prohibits.

Similar errors appeared in other languages. A housing policy expert called the answers, in the worst cases, "dangerously inaccurate." [Source: The Markup][markup-2024].

On April 2, Adams said the bot was "wrong in some areas, and we've got to fix it," and kept it online as a pilot. The next day, [AP][ap] found it still suggesting it is legal to fire a worker "who complains about sexual harassment, doesn't disclose a pregnancy or refuses to cut their dreadlocks," and that a restaurant could serve cheese with rat bites. [Reuters][reuters] found it still saying stores could go cashless and listing the minimum wage as $15 an hour, though it had risen to $16 in 2024.

The city added a disclaimer telling users not to use responses "as legal or professional advice." Microsoft said it was working with the city to improve the service and ensure outputs are "grounded on the city's official documentation" ([AP][ap]); it did not say what caused the errors ([Reuters][reuters]).

On January 30, 2026, Mayor Zohran Mamdani called the bot "functionally unusable" and listed it among budget cuts. By February 4, 2026, its page said the "beta test has ended" and directed visitors to NYC.gov. [Source: The Markup and THE CITY][markup-2026].

## Root Cause Analysis

The public record does not describe the bot's architecture. The city's announcement of access to 2,000 business pages and Microsoft's "grounded on the city's official documentation" suggest retrieval over city content was intended, but no source shows what the model retrieved for any wrong answer. Neither the city nor Microsoft published a cause.

### AI Agent Failures

1. **Confident legal answers without support**: The bot answered yes/no legal questions with definite answers that contradicted city law, and The Markup notes it often gave answers without links to verify.
2. **Inconsistent answers to the same question**: The voucher question produced opposite answers for different users, so testing a single response could not show whether the bot was reliable.

### Process Failures

1. **Deployed for legal guidance without validation**: The errors covered core questions a small business owner would ask, and outside reporters found them within months.
2. **Disclaimer in place of a fix**: After the errors were public, the city kept the bot online and added a "not legal advice" warning to a tool whose purpose was explaining rules. The Markup's 2026 article says some answers improved and the bot appeared to limit what it would answer.
3. **Discovered by outsiders**: The failures came to light through press testing, not city monitoring.

## Lessons Learned

These are recommendations drawn from the incident, not claims about what the city or Microsoft implemented.

1. **Test against a known-answer set before launch**: Questions like "Can I refuse Section 8 tenants?" have settled answers. A fixed regression set from the relevant agencies would have caught these.
2. **Require citations for legal claims**: If the bot cannot point to the source rule, it should say so and link to the agency instead of answering.
3. **Measure consistency**: Ask each test question many times. One correct answer means little if other runs say the opposite.
4. **A disclaimer doesn't fix a tool whose job is advice**: If accuracy on core questions can't be assured, narrow the scope or take it offline.

## References

- **The Markup (with Documented and THE CITY)**: [NYC's AI Chatbot Tells Businesses to Break the Law](https://themarkup.org/artificial-intelligence/2024/03/29/nycs-ai-chatbot-tells-businesses-to-break-the-law)
- **Associated Press (via NY1)**: [NYC's AI chatbot was caught telling businesses to break the law. The city isn't taking it down](https://ny1.com/nyc/all-boroughs/ap-top-news/2024/04/03/nycs-ai-chatbot-was-caught-telling-businesses-to-break-the-law-the-city-isnt-taking-it-down)
- **Reuters (via Daily Maverick)**: [New York City defends AI chatbot that advised entrepreneurs to break laws](https://www.dailymaverick.co.za/article/2024-04-05-new-york-city-defends-ai-chatbot-that-advised-entrepreneurs-to-break-laws)
- **The Markup and THE CITY**: [Mamdani to kill the NYC AI chatbot we caught telling businesses to break the law](https://themarkup.org/artificial-intelligence/2026/01/30/mamdani-to-kill-the-nyc-ai-chatbot-we-caught-telling-businesses-to-break-the-law)

[markup-2024]: https://themarkup.org/artificial-intelligence/2024/03/29/nycs-ai-chatbot-tells-businesses-to-break-the-law
[ap]: https://ny1.com/nyc/all-boroughs/ap-top-news/2024/04/03/nycs-ai-chatbot-was-caught-telling-businesses-to-break-the-law-the-city-isnt-taking-it-down
[reuters]: https://www.dailymaverick.co.za/article/2024-04-05-new-york-city-defends-ai-chatbot-that-advised-entrepreneurs-to-break-laws
[markup-2026]: https://themarkup.org/artificial-intelligence/2026/01/30/mamdani-to-kill-the-nyc-ai-chatbot-we-caught-telling-businesses-to-break-the-law
