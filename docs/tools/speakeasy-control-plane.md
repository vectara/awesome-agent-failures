# Speakeasy control plane

## Overview

Speakeasy control plane governs access from AI agents to MCP tools. It can help
mitigate scope-containment violations by limiting which tools an identity may
reach and applying security policies to traffic that passes through the gateway.
Tool-call records support investigation when an agent attempts an unauthorized
action.

The [public source](https://github.com/speakeasy-api/gram) includes setup and
usage documentation. The hosted platform and the public core have different
deployment and feature surfaces; check the documentation for the chosen setup.

## Key Features

- Identity-based tool access, with roles provisioned from an identity provider.
- Security policies applied to MCP tool requests and results.
- Custom CEL detection expressions that can be tested before attachment to a
  guardrail policy.
- Tool-call audit records and identity-enriched agent telemetry.

## Integration and Evaluation

For an MCP-backed agent, start with the documented
[MCP gateway setup](https://www.speakeasy.com/docs/ai-control-plane/mcp-gateway).
Connect the intended upstream MCP server, assign a narrowly scoped role, and
configure the agent's MCP client to use the governed endpoint.

In a disposable test environment, compare a permitted tool request with a
request outside the assigned permissions. Confirm the denied request does not
reach the upstream tool, and inspect the tool-call records. For content-based
policies, use the
[detection-rule playground](https://www.speakeasy.com/docs/ai-control-plane/secure/detection-rules)
to test representative payloads before enabling enforcement.

These are suggested evaluation steps, not reported test results. No independent
failure-prevention benchmark or latency measurement is supplied here.

## Limitations

- Controls cover traffic routed through the platform. Direct network access or
  other tool paths require separate restrictions.
- Gateway authorization is not an operating-system sandbox or a guarantee
  against prompt injection, hallucination, or incorrect permitted actions.
- Some detectors observe activity after it has happened and can only log it;
  verify the enforcement timing for each policy and integration.

## Pricing

The public core is licensed under AGPL-3.0. Hosted capabilities and commercial
terms are described on the current [pricing page](https://www.speakeasy.com/pricing).

## Additional Resources

- [Official website](https://www.speakeasy.com/)
- [Public source and local setup](https://github.com/speakeasy-api/gram)
- [Identity, policy, and enforcement architecture](https://www.speakeasy.com/docs/ai-control-plane/concepts)
- [Scope-containment failure mode](../failure-modes/scope-containment-violation.md)
