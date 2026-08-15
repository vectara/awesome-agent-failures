# agent-qa

## Overview

agent-qa is a source-available QA harness for natural-language web and mobile regression tests. It is aimed at verifying the software outcomes produced by coding agents: tests run against the application, execution evidence is retained, and lessons from past runs can inform later executions.

This is outcome-focused testing rather than general-purpose LLM tracing. It can help expose cases where a coding agent stops with a plausible implementation even though a user flow is still broken.

## Key Features

- **Natural-Language Tests**: Defines browser and mobile actions and assertions in human-readable test files
- **Web and Mobile Runners**: Uses Playwright for web flows and Appium for mobile flows
- **Persistent Execution Memory**: Carries product, suite, test, and healed-step observations into later runs
- **Self-Healing Execution**: Re-observes the interface and tries another path when an individual interaction fails
- **Reviewable Evidence**: Retains test definitions, run artifacts, and execution context for failure triage
- **Agent Interfaces**: Provides a CLI, MCP server, and three Agent Skills alongside a dashboard
- **Model Choice**: Supports OpenAI- and Anthropic-compatible endpoints, Gemini, and local models

## Failure Modes Addressed

- **Verification and Termination**: Replays acceptance flows before an agent or team treats a change as complete
- **Goal Misinterpretation**: Encodes expected user-visible outcomes independently from the implementation produced by a coding agent
- **Regression Accumulation**: Re-runs durable workflows after later changes and retains evidence from prior execution

## Limitations and Safety

- agent-qa is not an LLM hallucination detector or a full tracing platform for arbitrary agent internals.
- Test reliability still depends on the environment, model, assertions, and quality of the application state provided to a run.
- Browser and mobile tests may perform real write actions. Use scoped test accounts and disposable environments for destructive or externally visible flows.
- Docker is required only when using the optional sandboxed hook runtime.

## Pricing and License

- **Package**: Published on npm with no package fee documented
- **Operating Costs**: Model-provider, device, and infrastructure costs depend on the chosen setup
- **License**: FSL-1.1-ALv2, converting to Apache-2.0 after two years

## Additional Resources

- **GitHub Repository**: [vostride/agent-qa](https://github.com/vostride/agent-qa)
- **Documentation**: [agent-qa docs](https://vostride.com/docs/agent-qa)
- **Quickstart**: [Installation and first run](https://vostride.com/docs/agent-qa/quickstart)
- **Agent Skills**: [Source skill pack](https://github.com/vostride/agent-qa/tree/main/skills)
- **npm Package**: [agent-qa](https://www.npmjs.com/package/agent-qa)
