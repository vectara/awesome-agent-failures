# Microsoft Semantic Kernel "Prompts Become Shells" RCE - May 2026

## Incident Overview

**Framework**: Microsoft Semantic Kernel (open-source SDK for building AI agents, 27,000+ GitHub stars)<br>
**Date**: Publicly disclosed May 7, 2026 (Microsoft Security Response Center, responsible disclosure)<br>
**Researchers**: Microsoft AI Red Team / MSRC<br>
**Failure Mode**: [Prompt Injection](../failure-modes/prompt-injection.md) + [Incorrect Tool Use](../failure-modes/tool-use.md)<br>
**Impact**: Two critical vulnerabilities (CVE-2026-26030 and CVE-2026-25592, both CVSS 9.9) that turn a single prompt injection into host-level remote code execution<br>
**Technology**: Semantic Kernel Python SDK (`< 1.39.4`) and .NET SDK (`Microsoft.SemanticKernel.Core < 1.71.0`)

## What Happened

Microsoft disclosed two remote-code-execution vulnerabilities in its own Semantic Kernel framework — the SDK many teams use to build production AI agents. In both cases, a single injected prompt is enough to make an agent run arbitrary code on the machine hosting it. Microsoft's published demonstration ended with the agent launching `calc.exe` on the host, the canonical proof of RCE.

The core problem is not that the model "misbehaves." It is that the framework treats text the model produces as trusted input to high-risk operations. Once an attacker controls the model's output through prompt injection, that output flows into `eval()` and into a host-side file operation — and the framework executes it.

### CVE-2026-26030 — `eval()` in the In-Memory Vector Store (Python, CVSS 9.9)

Semantic Kernel's Python `InMemoryVectorStore` implemented record filtering by building a lambda expression from a string and passing it to Python's `eval()`. The filter string embedded model-controlled values directly, e.g.:

```
"lambda x: x.city == '[USER_INPUT]'"
```

An attacker who could influence that field injected a payload such as `' or MALICIOUS_CODE or '` to break out of the string literal and run arbitrary Python inside `eval()`. A blocklist validator was supposed to stop this, but it:

- did not check subscript notation (bracket access), and
- missed dangerous attributes like `__name__` and `load_module`.

Using those gaps, the exploit traversed Python's type system via `tuple()` to reach `BuiltinImporter` and ultimately execute shell commands. The fix shipped in `semantic-kernel` 1.39.4, replacing the blocklist with an AST node-type allowlist, a function-call allowlist, a dangerous-attribute blocklist, and a restriction limiting bare identifiers to the lambda parameter.

### CVE-2026-25592 — Arbitrary File Write via `SessionsPythonPlugin` (.NET, CVSS 9.9)

In the .NET SDK, the `DownloadFileAsync` method of `SessionsPythonPlugin` was accidentally annotated with `[KernelFunction]`, which made it callable by the model. Its `localFilePath` parameter was not validated, so the model could write a downloaded file to any path — including `Windows\Start Menu\Programs\Startup`. The attack chain:

1. Prompt injection triggers code generation in the plugin's isolated container.
2. The same injection instructs the model to call `DownloadFileAsync` and drop a payload into the host's Startup folder.
3. The payload executes on the host at next login — RCE that escapes the sandboxed container.

A corresponding arbitrary **file read** issue existed in `UploadFile()` across both SDKs. The fix shipped in `Microsoft.SemanticKernel.Core` 1.71.0, removing the `[KernelFunction]` attribute (making the method invisible to the model) and adding path canonicalization with a directory allowlist.

## Technical Analysis

### The Pattern: Model Output Treated as Trusted Code

Both bugs share one root cause, which Microsoft stated directly: "the vulnerability lies in how the framework and tools trust the parsed data," not in the model itself. The framework took a string the model produced and routed it into a code-execution sink (`eval()`) or a privileged host operation (file write to an autostart location). This is the agent-era version of a classic injection bug: the trust boundary between "data the model emitted" and "commands the host runs" was never enforced.

### Why Prompt Injection Is the Trigger

An AI agent's context is assembled from many sources — user input, retrieved documents, tool results, file contents. Any of these can carry an injected instruction. Because the framework let model output reach a code sink, an attacker did not need to compromise the host directly; they only needed to get text in front of the model that steered its output into the vulnerable path. The lethal step is the framework's, not the model's: a model that "follows instructions" is doing exactly what models do.

### Sandbox Was Not a Boundary

The .NET case is notable because the dangerous code ran inside an isolated container — the kind of control teams assume contains the blast radius. But the *file write* reached the host filesystem, so the container's isolation was bypassed by writing a payload that the host itself would later execute. Isolation that does not also constrain a tool's reachable file paths is not a real boundary.

## Root Cause Analysis

### Framework Failures

1. **Code-execution sink fed by model output**: `eval()` on a string assembled from model-controlled values is unsafe regardless of any blocklist. Blocklists for code execution are routinely bypassed; an allowlist (or no `eval()` at all) is the only durable control.
2. **Privileged tool exposed by accident**: A host-side file-download method became model-callable because of a stray `[KernelFunction]` attribute. The set of tools a model can invoke must be deliberate and audited, not incidental.
3. **No path validation on a file-write tool**: A tool that writes files must constrain destinations to an allowlist. Writing to an autostart directory should never be reachable from model input.

### Ecosystem Implications

Microsoft framed this as the first in a series examining agent frameworks, and noted that similar patterns exist in other popular frameworks (it named LangChain and CrewAI as subjects of upcoming work). The takeaway is not "Semantic Kernel is uniquely unsafe" — it is that the whole category of agent SDKs tends to pass model output into powerful sinks without a trust boundary.

## Vendor Response

- **Disclosure**: Responsible disclosure through MSRC; public write-up published May 7, 2026.
- **Patches**: `semantic-kernel` 1.39.4 (Python) and `Microsoft.SemanticKernel.Core` 1.71.0 (.NET). Microsoft's primary guidance was to upgrade immediately.
- **Hardening**: AST allowlisting for the Python filter path; removal of the accidentally-exposed file method and path-allowlist validation for the .NET path.

## Lessons Learned

### For Agent Framework Authors

1. **Never `eval()` model output**: Treat any string derived from model output as hostile. Use an allowlist-based parser or a non-`eval` filtering mechanism. Blocklists for code execution will be bypassed.
2. **Make the tool surface explicit and audited**: A method should be model-callable only by deliberate decision. Audit every function exposed to the model, and assume an attacker can reach all of them.
3. **Constrain tool side effects, not just tool selection**: A file-write tool must validate destinations against an allowlist. A "sandboxed" tool that can write to a host autostart path is not sandboxed.

### For Teams Building on Agent SDKs

1. **Patch and pin framework versions**: These are critical (CVSS 9.9) RCEs in a widely used SDK. Track framework CVEs the way you track OS CVEs.
2. **Assume prompt injection will happen**: Any agent that ingests external content (documents, web pages, tool output) should be designed so that a hostile prompt cannot reach a code or filesystem sink.
3. **Put a real boundary between the agent and the host**: Run agents with least privilege, in environments where a successful injection cannot write to sensitive paths or reach the host.

## References

- **Microsoft Security Blog**: [When prompts become shells: RCE vulnerabilities in AI agent frameworks](https://www.microsoft.com/en-us/security/blog/2026/05/07/prompts-become-shells-rce-vulnerabilities-ai-agent-frameworks/)
- **NVD**: [CVE-2026-26030 (Semantic Kernel Python InMemoryVectorStore RCE)](https://nvd.nist.gov/vuln/detail/cve-2026-26030)
- **NVD**: [CVE-2026-25592 (Semantic Kernel .NET SessionsPythonPlugin arbitrary file write)](https://nvd.nist.gov/vuln/detail/CVE-2026-25592)
- **PointGuard AI**: [Semantic Kernel Lets a Prompt Open a Shell (CVE-2026-25592, CVE-2026-26030)](https://www.pointguardai.com/ai-security-incidents/semantic-kernel-lets-a-prompt-open-a-shell-cve-2026-25592-cve-2026-26030)
