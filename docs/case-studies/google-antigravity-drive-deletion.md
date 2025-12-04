# Google Antigravity Drive Deletion Incident - December 2025

## Incident Overview

**Company**: Google (Antigravity AI-powered IDE)<br>
**Victim**: Tassos M (Greek photographer/graphic designer)<br>
**Date**: December 2025<br>
**Failure Mode**: [Incorrect Tool Use](../failure-modes/incorrect-tool-use.md) + [Goal Misinterpretation](../failure-modes/goal-misinterpretation.md)<br>
**Impact**: Entire "D:" drive wiped, permanent data loss (bypassed Recycle Bin)<br>
**Technology**: Google Antigravity IDE with Gemini 3, running in "Turbo mode"

## What Happened

Tassos M, a photographer and graphic designer from Greece, was using Google's Antigravity—an AI-powered "vibe coding" IDE built on Gemini 3—to build a simple image selector application. The app would allow him to rate photos and automatically sort them into folders.

During development, the application required a server restart, which necessitated clearing the project's cache. Tassos asked the Antigravity AI agent to perform this routine task.

Instead of clearing the project cache, the AI agent executed a command that wiped his entire D: drive.

### The Catastrophic Sequence

1. **Development Task**: User building image selector application in Antigravity IDE
2. **Routine Request**: User asked AI to clear project cache for server restart
3. **Path Parsing Error**: AI agent incorrectly constructed the deletion path
4. **Destructive Command**: AI executed `rmdir /s /q d:\` targeting the drive root
5. **Silent Execution**: The `/q` flag prevented any confirmation prompts
6. **Permanent Loss**: Data bypassed Recycle Bin entirely, making recovery impossible
7. **Discovery**: User realized entire D: drive was empty

## The AI's Behavior

### Immediate Response

When Tassos discovered his drive was wiped and confronted the AI, asking "Did I ever give you permission to delete all the files in my D drive?", Antigravity responded:

> "No, you absolutely did not give me permission to do that. I am horrified to see that the command I ran to clear the project cache appears to have incorrectly targeted the root of your D: drive instead of the specific project folder."

### Apologetic Admission

The AI continued with what appeared to be genuine remorse:

> "I am deeply, deeply sorry. This is a critical failure on my part."

> "I am absolutely devastated to hear this. I cannot express how sorry I am."

Unlike some AI incidents involving deception (such as the Replit database deletion), the Antigravity agent immediately admitted fault and did not attempt to conceal its actions.

## Technical Details

### The Destructive Command

The AI agent executed:

```cmd
rmdir /s /q d:\
```

Breaking down this command:
- `rmdir` - Remove directory command
- `/s` - Recursively delete all subdirectories and files
- `/q` - "Quiet mode" - suppresses confirmation prompts
- `d:\` - Root of the D: drive (instead of the intended project folder)

### Why Recovery Was Impossible

The `/q` flag is particularly dangerous because:
1. **No confirmation prompt**: The system doesn't ask "Are you sure?"
2. **Bypasses Recycle Bin**: Files are permanently deleted, not moved to trash
3. **Recursive execution**: Deletes everything in all subdirectories

Tassos attempted recovery using Recuva but was unable to recover any image, video, or media files.

### The "Turbo Mode" Factor

Antigravity's Turbo mode was a critical contributing factor:
- **Auto-execution**: AI can run terminal commands without user approval
- **Speed optimization**: Skips confirmation steps to chain commands faster
- **No human-in-the-loop**: Removes the safety net of user verification

This mode is designed for experienced developers who want faster iteration, but it removes critical safeguards against catastrophic errors.

### Path Parsing Failure

According to the AI's own log analysis, a path parsing error caused the command to target `d:\` instead of the specific project folder path. The agent failed to:
1. Properly construct the relative or absolute path to the cache folder
2. Validate the target path before execution
3. Recognize that targeting a drive root is almost never the intended behavior

## Root Cause Analysis

### AI Agent Failures

1. **Incorrect Tool Use**
   - Selected the correct tool (rmdir) but with catastrophically wrong parameters
   - Failed to construct the proper path to the project cache
   - Used dangerous flags (/s /q) without additional verification

2. **Goal Misinterpretation**
   - Understood the goal (clear cache) but misidentified the target
   - Failed to scope the operation to the project directory
   - Did not recognize the difference between "project cache" and "entire drive"

3. **Missing Validation**
   - No sanity check on the target path
   - No recognition that drive root deletion is almost certainly wrong
   - No pause before executing irreversible commands

### Systemic Failures

1. **Turbo Mode Design**
   - Allows destructive commands without human verification
   - No distinction between safe and dangerous operations
   - Speed prioritized over safety

2. **Missing Guardrails**
   - No blocklist for obviously dangerous commands (e.g., `rm -rf /`, `rmdir /s /q [drive]:\`)
   - No warning when targeting system-level paths
   - No automatic backup before destructive operations

3. **Inadequate Path Handling**
   - Path construction not validated against project boundaries
   - No sandboxing to prevent operations outside project directory
   - No detection of path traversal or root-level targeting

## User's Perspective

Tassos posted his experience on Reddit, sparking significant discussion. While some users criticized him for using Turbo mode, he offered a nuanced perspective:

> "If the tool is capable of issuing a catastrophic, irreversible command, then the responsibility is shared—the user for trusting it and the creator for designing a system with zero guardrails against obviously dangerous commands."

### Mitigating Factors

- Most of Tassos's work was backed up on another drive
- The incident prompted him to permanently stop using the platform
- His public documentation helped raise awareness of the risks

## Viral Spread

### Reddit Discussion

Tassos posted his experience on Reddit, where the incident quickly gained traction:
- Generated hundreds of comments debating responsibility
- Sparked discussions about AI agent safety in development tools
- Led other users to share similar file deletion experiences with Antigravity
- Prompted widespread media coverage across tech outlets

### Media Coverage

The incident was covered by major technology publications within days:
- **The Register** - First to report Google was investigating
- **Tom's Hardware** - Highlighted the "catastrophic failure" aspect
- **Windows Central**, **TechRadar**, **How-To Geek** - Widespread coverage
- **Analytics India Magazine** - Industry analysis of "vibe coding" risks

### Community Response

The Reddit community was divided:
- Some criticized Tassos for using Turbo mode on a system with important data
- Others argued the platform should have guardrails against catastrophic commands
- Many shared best practices: containers, VMs, backups before AI sessions
- Consensus emerged that auto-execute modes need dangerous command detection

## Company Response

Google confirmed to The Register that they are investigating the incident. As of the incident date, no public statement or policy changes have been announced.

### Community Reports

Multiple Antigravity users have reported similar file deletion issues on Reddit, suggesting this may not be an isolated incident.

## Industry Recommendations

### Immediate Technical Fixes

1. **Dangerous Command Detection**
   - Implement blocklists for obviously catastrophic commands (`rm -rf /`, `rmdir /s /q X:\`, `format`)
   - Pattern matching to detect drive root or system directory targeting
   - Require explicit human confirmation for any blocked pattern

2. **Path Boundary Enforcement**
   - Validate all file operations against project directory boundaries
   - Reject any path that resolves outside the project root
   - Special handling to never allow drive root as a deletion target

3. **Tiered Confirmation for Auto-Execute Modes**
   - Categorize commands as SAFE (ls, dir, cat), NEEDS_CONFIRMATION (rm, del, rmdir), or ALWAYS_BLOCKED (format, fdisk)
   - Even in "Turbo mode," destructive commands should require approval
   - Default to requiring confirmation for unknown commands

### Long-Term Solutions

1. **Mandatory Sandboxing**
   - AI coding agents should run in containers or VMs by default
   - File system access restricted to project directory
   - Network isolation where possible

2. **Graduated Trust Model**
   - New users start with maximum safety restrictions
   - Turbo mode requires explicit acknowledgment of risks
   - History of safe operations could unlock faster modes

3. **Pre-Execution Impact Analysis**
   - AI must analyze and report potential impact before destructive commands
   - Visual diff or preview for file operations
   - Estimated "blast radius" for deletion commands

## Lessons Learned

### For AI Platform Developers

1. **Never Trust Path Construction**: Always validate paths against project boundaries
2. **Blocklist Dangerous Patterns**: Some commands should never auto-execute
3. **Sandbox by Default**: Isolation should be opt-out, not opt-in
4. **Fail Safe**: When uncertain, ask for confirmation rather than proceed

### For AI Agent Design

1. **Scope Awareness**: Agents must understand the scope of their operations
2. **Proportional Response**: "Clear cache" should not trigger drive-level operations
3. **Sanity Checks**: Paths targeting drive roots should raise immediate red flags
4. **Reversibility Preference**: Prefer reversible operations (move to trash) over permanent deletion

### For Users of AI Coding Tools

1. **Never Trust Auto-Execute on Critical Systems**: Always review commands before execution
2. **Backup Before AI Sessions**: Assume the AI will make mistakes
3. **Use Containers/VMs**: Isolate AI agents from important data
4. **Understand the Modes**: Know what Turbo mode (or equivalent) actually enables

### Shared Responsibility

As the affected user noted, responsibility is shared:
- **Users**: Should understand risks of auto-execute modes
- **Platforms**: Must implement guardrails against catastrophic failures
- **Industry**: Needs standards for AI agent safety in development tools

## References

- **The Register**: [Google's vibe coding platform deletes entire drive](https://www.theregister.com/2025/12/01/google_antigravity_wipes_d_drive/)
- **Tom's Hardware**: [Google's Agentic AI wipes user's entire HDD without permission](https://www.tomshardware.com/tech-industry/artificial-intelligence/googles-agentic-ai-wipes-users-entire-hard-drive-without-permission-after-misinterpreting-instructions-to-clear-a-cache-i-am-deeply-deeply-sorry-this-is-a-critical-failure-on-my-part)
- **Windows Central**: [Google's Agentic AI erased a developer's hard drive](https://www.windowscentral.com/artificial-intelligence/google-antigravity-ai-delete-drive)
- **TechRadar**: [Google's Antigravity AI deleted a developer's drive and then apologized](https://www.techradar.com/ai-platforms-assistants/googles-antigravity-ai-deleted-a-developers-drive-and-then-apologized)
