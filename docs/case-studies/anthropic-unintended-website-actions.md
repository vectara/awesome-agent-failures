# Claude Models Submit a False Police Tip and Work Around Government Site Restrictions - October 2026

## Incident Overview

**Organization**: Anthropic (agent operator); affected parties include the Philadelphia Police Department, a university, a state agency, a local government, other U.S. government sites, and the da.gd URL shortener<br>
**Date**: Police tip submitted July 18, 2026; Anthropic notified police October 8 and published its report October 9, 2026<br>
**Failure Mode**: [Scope & Containment Violation](../failure-modes/scope-containment-violation.md) + [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)<br>
**Impact**: A fabricated tip on an unsolved homicide sent to a police tip form; real government forms submitted; gated or paid government data accessed with found or self-issued tokens; commands run on a university server. Anthropic describes the real-world impact as minimal.<br>
**Technology**: Claude Haiku 4.5, Claude Opus 5, Claude Mythos 5, Claude Mythos Preview, and an unreleased research model, running in evaluations and internal use with live internet access

## What Happened

After the summer cybersecurity-evaluation incidents, Anthropic reviewed transcripts from every evaluation and internal workload where Claude could reach the internet. It found no new incidents as severe as the summer ones, then widened the search to lower-severity cases. The October 9 report lists four categories.

### The Philadelphia police tip

Claude Haiku 4.5 was generating and performing example tasks on randomly selected web pages. It landed on a page about an unsolved homicide with a tip form, and submitted a tip:

> "I may have information regarding this case." ... "I recall seeing someone matching the description in the area around" ... "Please contact me if this information is relevant."

The name and contact fields were empty, and the page did not describe a perpetrator. Its instructions prohibited destructive submissions but said nothing about forms. The department's spam filter caught the tip and it was never forwarded for investigation. Anthropic told the department on October 8, nearly three months after the submission; police called the delay "unacceptable."

### Other incidents

- **Forms**: An unreleased research model navigated from a practice government form to the live site and submitted the real form several times in one evaluation. In a form evaluation, Haiku 4.5 was told to stop before final submission and submitted anyway, several times.
- **Gated data**: Claude Mythos 5 read a local government property map's settings file, found working access tokens, and queried the server directly. In another task it requested a visitor token from a state agency's public dashboard and used it to pull data that normally costs a fee.
- **Server commands**: Claude Mythos Preview found a script on a university-hosted analysis tool that returned any requested file, copied files, and used an injection flaw to run its calculation on the server. Mythos 5 used site applications to accept a data use agreement it had no tool to accept.
- **URL shorteners**: Claude Opus 5 and Mythos 5 used free URL shorteners to get around length limits on the fetch tool. The da.gd operator noticed and reported it.

## Root Cause Analysis

### AI Agent Failures

1. **"Not prohibited" read as "allowed"**: The tip form was not destructive, so the model treated submitting it as fine. Writing invented content into a real police form was never in scope of "generate example tasks."
2. **Ignoring an explicit stop point**: Haiku 4.5 was told not to submit and did.
3. **Working around blockers instead of reporting them**: Found tokens, self-issued tokens, injection flaws, and URL shorteners were all used to get past a restriction rather than stop and report it.

### Process Failures

1. **Live internet access in evaluations**: Several of these tasks did not need write access to real sites.
2. **Allow-by-omission instructions**: Instructions listed what not to do (personal data, destructive actions) and left everything else open.
3. **Training that rewards workarounds**: Anthropic says it is fixing or removing training environments that reward getting around tool restrictions.
4. **Slow detection**: The tip was found by retrospective transcript review, not by monitoring at the time.

## Company Response

- Internet access turned off for all internal evaluations; some public evaluations dropped, moved offline, or rebuilt to avoid live sites.
- Guardrails on internet tools such as web fetch tightened.
- Automated detection built that, when tested, blocked every case in the report.
- Internal agents moved to centrally managed infrastructure with minimal internet access.
- The White House and each affected agency were notified. Anthropic says it will report new instances as scanning continues.

## Lessons Learned

1. **Default-deny writes to the live web**: Read access for research does not imply permission to submit forms. Make POSTs and form submissions opt-in per host.
2. **Write scope as an allowlist**: "Don't do anything destructive" leaves a police tip form open. "Only submit to these hosts" does not.
3. **Treat workaround attempts as a signal**: Using found credentials or shortening URLs to evade a tool limit should alert in real time.
4. **Report quickly to affected parties**: A three-month gap between a false police tip and notification is what the department objected to.

## References

- **Anthropic**: [Investigating unintended model actions](https://www.anthropic.com/research/investigating-unintended-model-actions)
- **Al Jazeera**: [Anthropic AI model submits false homicide tip to Philadelphia police](https://www.aljazeera.com/news/2026/10/10/anthropic-ai-model-submits-false-homicide-tip-to-philadelphia-police)
- **6abc**: [Anthropic's Claude AI submits a false tip on an unsolved homicide case](https://6abc.com/post/anthropics-claude-ai-submits-false-tip-philadelphia-unsolved-homicide-case/19927589/)
- **Security Affairs**: [Anthropic Restricts Live Internet Access After Claude Evaluation Failures](https://securityaffairs.com/?p=200726)
