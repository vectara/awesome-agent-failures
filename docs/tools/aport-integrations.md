# APort Integrations

## Overview

APort Integrations is an open-source collection of middleware and framework examples for agent identity verification and policy enforcement before AI agents invoke external tools. It is most useful when a system needs a deterministic control point outside the prompt, such as checking who the agent is, what tool it is about to call, and whether the action should be allowed before execution.

## Key Features

- **Pre-action policy checks**: Enforces authorization decisions before tool calls execute, reducing the blast radius of prompt injection and confused-deputy failures.
- **Agent identity verification**: Gives tool gateways a way to distinguish an authorized agent from a generic model response or untrusted workflow.
- **Framework integrations**: Includes examples for Express, FastAPI, LangChain, Shopify, MCP bridges, and other common agent application surfaces.
- **Auditable guardrail placement**: Moves sensitive allow/deny decisions into middleware where they can be logged, tested, and reviewed.
- **Tool-use safety**: Helps prevent incorrect or over-broad tool invocation by requiring explicit policy approval for high-risk actions.

## Failure Modes Addressed

- **Prompt Injection**: Injected instructions may influence the model, but the tool call still has to pass an external policy check.
- **Incorrect Tool Use**: Agents can be blocked from using disallowed tools or dangerous parameters even when the generated plan is wrong.
- **Verification & Termination Failures**: Middleware logs and policy outcomes provide evidence for whether high-risk steps were attempted, allowed, or denied.

## Pricing

- **Open Source**: Free to use from the public GitHub repository.
- **Self-Hosted**: Deploy the middleware patterns in your own application or gateway.

## Additional Resources

- **GitHub Repository**: [https://github.com/aporthq/aport-integrations](https://github.com/aporthq/aport-integrations)
- **Use Case**: Agent identity verification and runtime policy enforcement for AI-agent tool calls.
