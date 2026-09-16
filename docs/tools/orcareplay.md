# OrcaReplay

## Overview

OrcaReplay records an agent run at the model-provider boundary and replays it
later with no model called. It sits between the agent and the provider as a
proxy, so the agent is unmodified and the recording is the bytes the agent
actually sent and received — not a summary the agent wrote about itself
afterwards.

For this list the relevant property is the one none of the tools above have:
the other five observe or score a run while or shortly after it happens. When a
failure is reported days later, a trace tells you *what* the agent did; a
recording lets you run it again — same files, same conversation prefix, no
tokens — and change one variable at a time until the failure is explained. The
contributing guide asks failure cases to "include reproduction steps when
possible"; a recording is what makes that step mechanical rather than a
best-effort retelling.

The fork mode is the part that turns a reproduction into an experiment:
`orca replay last --from 4 --model <other>` replays the run up to step 4 from
the recording and then continues on a different model. Everything before the
fork point is byte-identical, so the model is the only variable.

## Key Features

- **Proxy-level capture**: records the provider request/response stream; the
  agent is unmodified and needs no SDK integration
- **Offline replay**: `orca replay last` serves the recorded turns; no network,
  no key, no token spend
- **Fork from a step**: `--from N --model M` replays a prefix and diverges onto
  another model, isolating the model as the single variable
- **Divergence report**: replay reports how many turns were served from the
  trace, how many matched exactly, and how many diverged
- **Scrubbing**: captured prompts and traces are scrubbed of machine-local paths
  and credentials before they are written
- **Portable trace format**: the on-disk format is documented in
  [`spec/orca-trace-v0.md`](https://github.com/Continuum-AI-Corp/OrcaReplay/blob/main/spec/orca-trace-v0.md)
  (CC BY 4.0), so a recording outlives the CLI that wrote it

## Limitations

Stated plainly, because they matter for how the tool is used here:

- **A matching replay is not a determinism result.** Replay proves the recorded
  exchange reproduces; it does not prove the agent is deterministic, and it does
  not prove the *same* failure would recur against a live model.
- **`egress=blocked` refers to the model provider only.** Replay blocks calls to
  the provider. It is not a sandbox: the agent's own tool calls still touch the
  real filesystem and the real network.
- **Not every call is captured by the default adapter.** Embedding calls, for
  example, are not; a run whose failure lives in retrieval may replay cleanly
  and still not reproduce the bug.
- Requires Node 20+.

## Pricing

- **Open Source**: Apache-2.0, free and self-hosted; `npm i -g orcareplay`
- **No account needed for record or replay**: replay calls no model and needs no
  key
- **Optional**: `orca setup` and `orca compare` default to OrcaRouter, a paid
  multi-provider gateway from the same team, but any provider base URL works

## Additional Resources

- **GitHub Repository**: [https://github.com/Continuum-AI-Corp/OrcaReplay](https://github.com/Continuum-AI-Corp/OrcaReplay)
- **Trace format spec**: [orca-trace-v0](https://github.com/Continuum-AI-Corp/OrcaReplay/blob/main/spec/orca-trace-v0.md)
- **Zero-setup demo**: `orca quickstart` writes a small buggy project plus a
  recording of an agent fixing it, then replays the recording with no model
  called
