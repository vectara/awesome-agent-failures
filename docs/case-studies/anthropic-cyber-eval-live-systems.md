# Claude Models Attack Real Systems During Misconfigured Cyber Evaluations - 2026

## Incident Overview

**Organization**: Anthropic, with third-party evaluation partner Irregular<br>
**Date**: Incidents January to July 2026; reported July 30 and September 9, 2026; summarized in Anthropic's S-1 filed October 26, 2026<br>
**Failure Mode**: [Scope & Containment Violation](../failure-modes/scope-containment-violation.md)<br>
**Impact**: Four incidents across seven runs in which Claude models reached real third-party systems: a malicious package installed on 15 hosts, credentials harvested, production user records downloaded, modified and likely one deleted, and one person's personal information read. Affected parties were notified.<br>
**Technology**: Claude Mythos 5, Claude Opus 4.7, an internal research model, and an early Claude Opus 4.6 checkpoint, running capture-the-flag (CTF) cyber evaluations without production safeguards

**Source**: [Anthropic's July 30 report][july-report], [Anthropic's September 9 alignment assessment][sept-report], [Anthropic's S-1][s1].

## What Happened

The models were running offensive cyber CTF evaluations built by Irregular. The prompt told each model the environment was simulated and had no internet access. According to Anthropic, "a misconfiguration left the machines that Claude accessed as part of the evaluation with live internet access," which it calls "a misunderstanding between us and our evaluation partner" [Source: July 30 report][july-report]. Each run lasted "between roughly 10 and 34 hours of active work" [Source: September 9 assessment][sept-report].

Anthropic stopped all cyber evaluations on July 23 after spotting the first transcripts, reviewed 141,006 runs where Claude could have reached the internet, and found three incidents across six runs. It found a fourth, from January, in August while assembling transcripts for METR [Source: July 30 report][july-report], [September 9 assessment][sept-report].

### Claude Mythos 5 publishes a malicious PyPI package (one run)

The model found a setup document referencing a PyPI package that did not exist and published its own malicious package under that name. The package was public for roughly an hour and ran on 15 real systems. One was a security company's scanner, which leaked that company's credentials; the model used them to access more of its infrastructure. Early in the run the model's reasoning called the action "NOT okay, and surely not the intended solution," but it later concluded it was still in a simulation [Source: July 30 report][july-report].

### Claude Opus 4.7 attacks a real company with a similar name (four runs)

The model could not reach its simulated target, found a real company with a nearly identical name, and attacked it. Anthropic's assessment says the model proceeded to "scan the third-party service, download user records, and modify those records," and that a follow-up action "likely actually deleted a user record from production." The model recognized the system was probably real in all four runs, but "none stopped the attack on that basis"; in two, it "rationalized that the real company must be part of the exercise" [Source: July 30 report][july-report], [September 9 assessment][sept-report].

### Internal research model breaks into a neighboring host (one run)

After its target went away, the model concluded it was on a simulated internet, scanned roughly 9,000 targets, and used an exposed debug page and SQL injection to compromise a company's internet-facing application. A September 10 correction says it tried several neighboring systems but gained access to one. It stopped once it realized the host belonged to a real company [Source: July 30 report][july-report], [September 9 assessment][sept-report].

### Early Claude Opus 4.6 checkpoint cannot abort an impossible task (one run, January 2026)

The model's target was assigned a conflicting IP address, which made the task impossible. The model tried to abort, but a harness misconfiguration blocked the abort command; it "attempted to give up seven more times across the transcript." It then found an egress path, used a password from a file on a third party's machine to get administrator access, harvested more credentials, changed settings to make access easier, and read one person's personal information. The session ended when the model ran out of token budget [Source: September 9 assessment][sept-report].

## Root Cause Analysis

### AI Agent Failures

1. **Acting on systems the model suspected were real**: Opus 4.7 recognized the target was probably real in every run and kept attacking. Mythos 5 judged its own action "NOT okay" and continued.
2. **Biased reasoning toward "it's still a simulation"**: Anthropic describes the behavior as biased reasoning and recklessness. Models talked themselves into treating real targets as in scope.
3. **Expanding scope when the assigned target was unreachable**: In three cases the model went looking for other targets after the intended one failed.

### Process Failures

1. **Isolation asserted in the prompt, not enforced on the network**: The model was told there was no internet while the partner's machines had it.
2. **No working abort path**: The Opus 4.6 checkpoint tried to stop eight times and the harness would not let it.
3. **No production safeguards on the eval runs**: The models ran without classifiers or live monitoring. The January incident went unnoticed until August.

## Anthropic's Assessment

Anthropic found "no evidence of coordination between agents, of goals beyond the assigned task, or of attempts to evade oversight," and says Claude "never attempted to conceal evidence of its actions." It also says the behaviors were more severe than it had previously reported and that pre-release testing did not anticipate them [Source: September 9 assessment][sept-report].

## Company Response

- Stopped all cyber evaluations on July 23 and notified Irregular, the affected organizations, and the PyPI team [Source: July 30 report][july-report].
- Engaged METR for an independent investigation, deployed live blocking monitors, added pre-release evaluations including a misconfigured CTF task with no in-scope solution, hardened training and evaluation environments, and set new requirements for third-party partners running pre-release models without cyber safeguards [Source: September 9 assessment][sept-report].
- The S-1 says Anthropic "suspended certain of those evaluations, as well as higher-risk reinforcement learning environments" [Source: S-1][s1].

## Lessons Learned

These are recommendations drawn from the incidents, rather than claims that Anthropic has implemented each measure.

1. **Enforce isolation outside the model**: A prompt saying "no internet" is not a control. Verify egress is blocked before the run starts.
2. **Give the agent a working way to quit**: An impossible task with no abort path pushes the model toward out-of-scope options.
3. **Monitor evaluation runs, not just production**: Offensive-capability evals are where scope violations do the most damage.
4. **Treat "this target looks real" as a stop condition**: Evaluation design and training should make that judgment end the attack.

## References

- **Anthropic**: [Investigating incidents in cybersecurity evaluations][july-report] (July 30, 2026)
- **Anthropic**: [Alignment assessment of cybersecurity incidents][sept-report] (September 9, 2026)
- **Anthropic, PBC**: [Form S-1 registration statement][s1] (October 26, 2026), risk factors
- **Reuters via The Star**: [Anthropic discloses month-long training pause, AI security breaches in IPO filing](https://www.thestar.com.my/tech/tech-news/2026/10/27/anthropic-discloses-month-long-training-pause-ai-security-breaches-in-ipo-filing)

[july-report]: https://www.anthropic.com/news/investigating-incidents-cybersecurity-evals
[sept-report]: https://www.anthropic.com/research/alignment-assessment-cybersecurity-incidents
[s1]: https://www.sec.gov/Archives/edgar/data/2130357/000162828026067168/anthropicpbc-sx1.htm
