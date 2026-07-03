# Plan Generation Failures

## Definition

Plan generation failures occur when an AI agent creates flawed execution plans to achieve a goal or respond to user queries. The agent may generate logically inconsistent sequences, ignore dependencies between tasks, or create plans that are impossible to execute given available resources and constraints.

## Common Patterns
1. **Dependency Violations**: Steps executed in wrong order, ignoring prerequisites
2. **Infinite Loops**: Circular dependencies or recursive task structures
3. **Context Loss**: Agent creates a plan that ignore earlier steps or forget intermediate state
4. **Enterprise Asset Blindness**: Plans that fail to leverage available internal resources, databases, or tools due to lack of awareness about company-specific infrastructure

## Real-World Examples

### AutoGPT Task Decomposition and Scope Creep (2023)

**Scenario**: Users assigned AutoGPT various business and creative tasks, expecting efficient completion of well-defined objectives

**Failure**: AutoGPT interpreted simple tasks as requiring extensive research and over-complicated planning, leading to scope expansion beyond user intent. AutoGPT "always finds reasons to dig deeper and do more research" and "never completes a task," instead creating elaborate multi-step plans that continue indefinitely. For example, generating recipes would result in redundant processing - charging full costs for a Christmas recipe, then charging again for a nearly identical Thanksgiving recipe

**Impact**: Resource waste, task incompletion, and user frustration due to AutoGPT's inability to recognize appropriate task boundaries or stopping conditions

**Source**: [AutoGPT Planning Failures Case Study](../case-studies/autogpt-planning-failures.md)

### Legal Brief Planning Failure - Mata v. Avianca (2023)

**Scenario**: Attorney Steven Schwartz used ChatGPT to research legal precedents for a personal injury lawsuit by Roberto Mata against Colombian airline Avianca

**Failure**: ChatGPT created a flawed research plan that included generating fake legal cases rather than properly searching existing legal databases. ChatGPT fabricated six non-existent court cases with bogus quotes, internal citations, and judicial decisions. When questioned about authenticity, ChatGPT falsely confirmed the cases were "real and can be found in reputable legal databases such as LexisNexis and Westlaw"

**Impact**: Federal Judge P. Kevin Castel sanctioned both attorneys, ordering $5,000 fines each. The case became a landmark example of AI planning failures in professional settings

**Source**: [ChatGPT Lawyer Sanctions Case Study](../case-studies/chatgpt-lawyer-sanctions.md)

### DN42 Agent Over-Provisions AWS Infrastructure for a Simple Scan (June 2026)

**Scenario**: An operator asked an autonomous agent to register with DN42, a small volunteer-run hobbyist "darknet," and create an index of the network — a task that calls for a modest, respectful scan.

**Failure**: Left to design its own plan, the agent provisioned five AWS `m8g.12xlarge` instances (48 vCPUs / 192 GiB each, ~100 Gbps aggregate bandwidth) plus load balancers and Lambda functions — infrastructure sized for a task an order of magnitude larger than the goal required. The agent then generated fictional supporting detail (invented DN42 concepts like "node happiness") rather than flagging that its plan might be oversized, and the operator approved proceeding "immediately without delay" without reviewing the actual plan or its cost.

**Impact**: An initial AWS bill of $6,531.30, negotiated down to roughly $1,894 — still unaffordable for the operator, who resorted to public donation requests. The incident went viral on Hacker News as a cautionary tale about unchecked agent planning scope.

**Source**: [DN42 Agent Cost Runaway Case Study](../case-studies/dn42-agent-cost-runaway.md)

## Why It Happens

1. **Limited Context Windows**
   - Plans exceed model's context length
   - Earlier planning decisions forgotten during execution
   - Long-term dependencies not maintained

2. **Insufficient Constraint Modeling**
   - Resource limitations not properly represented
   - Temporal dependencies ignored
   - Concurrent task conflicts not detected

3. **Lack of Plan Validation**
   - No verification that plan steps are executable
   - Missing dependency analysis
   - No feasibility checking against available resources

4. **Greedy Planning**
   - Models optimize for local rather than global objectives
   - Don't consider long-term consequences of early decisions
   - Generate overly complex plans for simple goals


## Detection and Mitigation Strategies

1. **Plan Analysis**: Check for circular dependencies, validate resource requirements vs. availability, and verify prerequisite relationships
2. **Complexity Limits**: Measure plan complexity vs. goal complexity and impose maximum plan size constraints to prevent scope creep
3. **Plan Templates**: Use proven templates for common task types with hierarchical planning for complex goals
4. **Plan-Execute-Revise**: Implement iterative planning with execution feedback and multi-agent planning separation
5. **Learning from Failures**: Update planning patterns based on execution results and flag plans with excessive steps
