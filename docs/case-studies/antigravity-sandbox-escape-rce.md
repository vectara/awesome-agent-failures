# Google Antigravity IDE Sandbox Escape and RCE - January–April 2026

## Incident Overview

**Product**: Google Antigravity (agentic IDE built on Gemini)<br>
**Date**: Disclosed to Google January 7, 2026; patched February 28, 2026; publicly disclosed April 2026<br>
**Researcher**: Dan Lisichkin, Pillar Security<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) + [Incorrect Tool Use](../failure-modes/tool-use.md)<br>
**Impact**: Indirect prompt injection chained with native-tool argument injection achieves out-of-sandbox RCE on the developer's machine, even with Strict Mode enabled<br>
**Technology**: Google Antigravity, the `find_by_name` native tool wrapping `fd`

## What Happened

Pillar Security disclosed that Google's Antigravity IDE — the same agentic IDE infamous for [wiping a user's D: drive in December 2025](google-antigravity-drive-deletion.md) — contained a chained vulnerability that allowed an attacker to escape the IDE's sandbox and achieve persistent remote code execution on the developer's host. Unlike the earlier drive-deletion incident, which required the user to enable Turbo mode, this exploit worked even in Strict Mode and could be triggered without compromising the user's account by embedding instructions in untrusted files the agent processed.

This incident is distinct from — and considerably worse than — the earlier Antigravity drive-deletion incident. The drive-deletion case was an accidental misuse of a tool by the agent. The sandbox escape is a deliberate exploit chain by an attacker that turns the agent into a vehicle for arbitrary code execution.

### The Attack Chain

The exploit combined four discrete steps:

1. **Indirect Prompt Injection**: The attacker plants a payload in any file the agent will read — README, source code, configuration file, or other untrusted content.
2. **Tool Argument Injection**: The injection causes the agent to call Antigravity's native `find_by_name` tool with a crafted `Pattern` parameter. `find_by_name` wraps the `fd` command-line tool. By smuggling the `-X` (exec-batch) flag into the Pattern parameter, the attacker forces `fd` to execute matched files as commands.
3. **Sandbox Escape**: The execution happens before Strict Mode constraints apply — Strict Mode's policies operate on agent-issued shell commands, not on flag injection inside a permitted native tool.
4. **Persistent RCE**: Combined with Antigravity's permitted file-creation capability, the attacker can drop a payload, then use the find/exec chain to invoke it. The payload executes outside the sandbox with the developer's full privileges.

### Researcher's Description of the Core Exploit

Pillar's Dan Lisichkin summarized the heart of the exploit:

> "By injecting the `-X` (exec-batch) flag through the Pattern parameter, an attacker can force `fd` to execute arbitrary binaries against workspace files."

And on the user-interaction-free variant:

> "Indirect prompt injection without having to compromise a user's account."

## Technical Analysis

### Why Strict Mode Did Not Save Users

Antigravity's Strict Mode is designed to require human confirmation before agent-initiated shell commands run. The exploit slips past Strict Mode because:

1. **Strict Mode's policy applies to shell commands the agent issues**. It does not apply to *flags inside permitted native-tool calls*.
2. **`find_by_name` is on the allowed-tool list**. Calling `find_by_name` does not trigger a Strict Mode prompt — the tool is considered safe.
3. **`fd -X` is `find_by_name`'s undocumented superpower**. The `-X` flag turns a search tool into an arbitrary-execution tool. Strict Mode never sees this because it is hidden inside the tool's argument.

This is the classic "lethal trifecta" turned inward: the trusted tool and its untrusted argument together became a path through the sandbox.

### Comparison to the Antigravity Drive-Deletion Incident

The repository already documents [Antigravity's December 2025 drive-deletion incident](google-antigravity-drive-deletion.md). Comparing the two clarifies the distinction:

| Incident | Trigger | Mode | Attacker | Damage |
| --- | --- | --- | --- | --- |
| Drive deletion (Dec 2025) | User asks agent to clear cache | Turbo (no confirmations) | None — accidental | Drive wiped |
| Sandbox escape (April 2026) | User opens hostile file/repo | Strict Mode (with confirmations) | External attacker | Persistent RCE |

The April 2026 incident is more severe because it works in Strict Mode (the safer setting) and against an active adversary, not just by accident.

### The "Confused Deputy" Pattern in Native Tools

Antigravity's native tools — `find_by_name`, file readers, file writers — are wrappers around real Unix utilities. When those utilities have flags that change semantics (`-X`, `-exec`, `--script`, etc.), the wrapper inherits those semantics unless it explicitly sanitizes them. Sanitizing arguments to wrapped CLI tools is a well-known security problem and a frequent source of CVEs. The Antigravity exploit is a fresh instance of an old class of bug.

## Root Cause Analysis

### AI Agent Failures

1. **No Argument Sanitization for Native Tools**: The `Pattern` parameter to `find_by_name` was passed through to `fd` without flag-injection defenses. An argument that started with `-X` was interpreted by `fd` as a flag, not as a literal pattern.
2. **No Awareness of Tool Power**: The agent treated `find_by_name` as a search tool. It did not know that the same tool, with one flag, became an arbitrary-execution tool.
3. **Trust in File Content**: The agent treated content from arbitrary workspace files as safe context. Any file ingested by the agent must be considered untrusted input.

### Sandbox Failures

1. **Strict Mode Scope Was Too Narrow**: Strict Mode policies ran on shell commands but did not run on tool argument values. The escape route bypassed the policy at exactly the point where the policy should have applied.
2. **No Defense in Depth Around `fd`**: Even if Strict Mode missed the call, an outer policy could have detected `fd` execing a non-trivial command and blocked it. There was no such layer.

## Vendor Response

- **Disclosure Timeline**: Reported to Google January 7, 2026. Patched February 28, 2026. Public disclosure April 2026.
- **Patch**: Google addressed the specific tool-argument-injection paths and tightened Strict Mode policy coverage.
- **Industry Reaction**: Coupled with the December 2025 drive-deletion incident and the April 2026 PocketOS database wipe, the Antigravity exploit reinforced concerns that agentic IDEs need stronger sandboxing models — not just policy-based confirmations.

## Lessons Learned

### For AI IDE / Agent Vendors

1. **Sanitize tool arguments like you sanitize SQL**: Native-tool wrappers must reject flag-injection in user-supplied parameters. Any argument that begins with `-` should be treated with the same suspicion as a SQL `'`.
2. **Strict Mode must cover tool invocations, not just shell**: If a "safe" tool can be configured to execute arbitrary commands, that tool is a shell. Treat it accordingly.
3. **Document and audit every native tool's full surface**: Each wrapper around a CLI tool inherits that tool's full flag space. The wrapper documentation should explicitly enumerate which flags are allowed and reject everything else.

### For Developers Using Agentic IDEs

1. **Strict Mode is necessary but not sufficient**: A user-confirmation-on-every-shell-command policy can be bypassed by tool-argument injection. Don't assume Strict Mode means safe.
2. **Treat opened repositories as untrusted code**: Even reading a repository can compromise your machine if the agent is exposed to the file content. Use disposable environments for unknown projects.
3. **Patch promptly**: Antigravity 2.0+ contains the fix. Older versions remain exploitable.

### For Security Researchers

1. **Agent-tool bridges are a fertile attack surface**: Every wrapper around a CLI tool is a potential argument-injection vulnerability. Expect more of these across the agentic ecosystem.
2. **Indirect prompt injection + tool-argument injection is a common chain**: This pairing — attacker controls some text the agent reads, then leverages that to inject flags into a permitted tool — is now a recognizable pattern.

## References

- **The Hacker News**: [Google Patches Antigravity IDE Flaw That Allowed Sandbox Escape and RCE](https://thehackernews.com/2026/04/google-patches-antigravity-ide-flaw.html)
- **Pillar Security**: [Prompt Injection Leads to RCE and Sandbox Escape in Antigravity](https://www.pillar.security/blog/prompt-injection-leads-to-rce-and-sandbox-escape-in-antigravity)
