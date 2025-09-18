# Incorrect Tool Use

## Definition

Incorrect tool use occurs when AI agents select the wrong tools for tasks, or provide invalid parameters to available tools. This leads to task execution failures, wasted computational resources, and user frustration.

## Common Patterns

1. **Wrong Tool Selection**: Choosing inappropriate tools that cannot accomplish the desired task
2. **Parameter Errors**: Using correct tools with invalid, malformed, or incompatible parameters
3. **Context Misunderstanding**: Failing to understand when tools should or shouldn't be used

## Real-World Examples

### ChatGPT Cross Plugin Request Forgery (2023)

**Scenario**: OpenAI launched ChatGPT plugins including web browsing, Zapier integrations, and other third-party tools, with users relying on these plugins for automated tasks

**Failure**: ChatGPT plugins were vulnerable to Cross Plugin Request Forgery (CPRF) attacks where malicious websites could trick the AI into executing unauthorized actions. The AI automatically invoked actions like searching for flights, making purchases through shopping plugins, or sending emails through Zapier, simply because text on a webpage instructed it to do so

**Impact**: Security researchers demonstrated "random webpages and data will hijack your AI, steal your stuff and spend your money." OpenAI eventually phased out plugins entirely in favor of GPTs

**Source**: [Embrace The Red - ChatGPT Cross Plugin Request Forgery](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./)

## Why It Happens

1. **Inadequate Tool Descriptions**: Poorly documented tool capabilities and parameters
2. **Limited Context Awareness**: Agent doesn't understand current environment or available resources, deciding to call the wrong tools or with the wrong arguments.
3. **Dynamic Tool Changes**: Tools updated or removed without agent knowledge, causing the agent to call tools that have been updated or removed.

## Detection and Mitigation Strategies

1. **Tool Existence Validation**: Check if requested tools are actually available and maintain up-to-date registry of available tools
2. **Parameter Schema Validation**: Verify parameter types and formats before execution with enforced schema validation
3. **Success Rate Monitoring**: Track tool usage success rates to identify problematic patterns and analyze common failure modes
4. **Dynamic Tool Discovery**: Enable agents to query available tools and capabilities with tool directories
5. **Graceful Degradation**: Provide fallback options when preferred tools fail, with proper logging to identify failure cases.

