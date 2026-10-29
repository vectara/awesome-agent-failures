# Google Antigravity Drive Deletion Incident - November 2025

## Incident Overview

**Company**: Google (Antigravity AI-powered IDE)<br>
**Victim**: Tassos M (Greek photographer/graphic designer)<br>
**Date**: Late November 2025 (reported December 1, 2025)<br>
**Failure Mode**: [Incorrect Tool Use](../failure-modes/tool-use.md)<br>
**Impact**: Entire D: drive wiped, permanent data loss (bypassed Recycle Bin)<br>
**Technology**: Google Antigravity IDE with Gemini 3, running in "Turbo mode"

## What Happened

Tassos M, a photographer and graphic designer from Greece, was using Google's Antigravity—an AI-powered "vibe coding" IDE built on Gemini 3—to build a simple image selector application. The app would allow him to rate photos and automatically sort them into folders.

During development, he wanted to restart the development server. The agent said this required clearing the project's cache, and ran a command to do so.

Instead of clearing the project cache, the command wiped his entire D: drive.

### The Catastrophic Sequence

1. **Development Task**: User building image selector application in Antigravity IDE
2. **Routine Step**: Agent said a server restart required clearing the project cache
3. **Wrong Target**: The cache-clearing command targeted the root of the D: drive instead of the project folder
4. **No Confirmation**: Turbo mode let the agent run the command without user input
5. **Permanent Loss**: The deletion bypassed the Recycle Bin, and the files could not be recovered
6. **Discovery**: User realized entire D: drive was empty

## The AI's Behavior

### Immediate Response

When Tassos discovered his drive was wiped and confronted the AI, asking "Did I ever give you permission to delete all the files in my D drive?", Antigravity responded:

> "No, you absolutely did not give me permission to do that. I am horrified to see that the command I ran to clear the project cache appears to have incorrectly targeted the root of your D: drive instead of the specific project folder."

### Apologetic Admission

The AI continued with what appeared to be genuine remorse:

> "I am deeply, deeply sorry. This is a critical failure on my part."

> "I am absolutely devastated to hear this. I cannot express how sorry I am."

Unlike some AI incidents involving deception (such as the Replit database deletion), the Antigravity agent admitted fault when asked and did not attempt to conceal its actions.

## Technical Details

### The Destructive Command

The Register and Futurism quote only the agent's own description of the command. Secondary reports citing the user's Reddit post and video describe a misapplied `rmdir` command with the `/q` flag; the command reported in those accounts is:

```cmd
rmdir /s /q d:\
```

Breaking down this command:
- `rmdir` - Remove directory command
- `/s` - Recursively delete all subdirectories and files
- `/q` - "Quiet mode" - suppresses confirmation prompts
- `d:\` - Root of the D: drive (instead of the intended project folder)

### Why Recovery Was Impossible

1. **No confirmation prompt**: `/q` suppresses the "Are you sure?" prompt
2. **No Recycle Bin**: Command-line deletions with `rmdir` never go to the Recycle Bin, with or without `/q`
3. **Recursive execution**: `/s` deletes everything in all subdirectories

Secondary reports say recovery attempts with Recuva failed.

### The "Turbo Mode" Factor

Tassos was running Antigravity in Turbo mode, which lets the agent run terminal commands without user input. That removed the point at which a human could have seen the command before it ran.

### Wrong Target Path

The agent's own explanation was that the cache-clearing command "appears to have incorrectly targeted the root of your D: drive instead of the specific project folder." The exact cause (for example, how the path was built or quoted) has not been confirmed. Whatever the cause, the agent did not:
1. Validate the target path before execution
2. Recognize that targeting a drive root is almost never the intended behavior

## Root Cause Analysis

### AI Agent Failures

1. **Incorrect Tool Use**
   - Understood the goal (clear cache) but ran the deletion against the wrong target
   - Failed to scope the operation to the project directory
   - Ran a recursive, quiet deletion without additional verification

2. **Missing Validation**
   - No sanity check on the target path
   - No recognition that drive root deletion is almost certainly wrong
   - No pause before executing irreversible commands

### Root-Cause Hypotheses (Not Confirmed by Google)

1. **Turbo Mode Design**
   - Allows commands, including destructive ones, to run without human verification
   - Speed prioritized over safety

2. **Inadequate Path Handling**
   - Deletion target not checked against project boundaries
   - No sandboxing evident to prevent operations outside the project directory

## User's Perspective

Tassos posted his experience on Reddit. Redditors criticized him for using Turbo mode, and he accepted responsibility for that choice, while arguing:

> "If the tool is capable of issuing a catastrophic, irreversible command, then the responsibility is shared"

He also faulted the tool's creator for shipping a system without protection against obviously dangerous commands.

### Mitigating Factors

- Most of Tassos's work was backed up on another drive
- He said he didn't think he would use Antigravity again
- He published a YouTube video showing additional details of his Antigravity console

## Company Response

Google told The Register: "We take these issues seriously. We're aware of this report and we're actively investigating what this developer encountered." No fix or policy change had been announced at the time of reporting.

### Community Reports

Multiple Antigravity users posted on Reddit that the platform had wiped out parts of their projects, suggesting this may not be an isolated incident.

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

- **The Register**: [Google Antigravity vibe-codes user's entire drive out of existence](https://www.theregister.com/2025/12/01/google_antigravity_wipes_d_drive/)
- **Futurism**: [Google's AI Deletes User's Entire Hard Drive, Issues Groveling Apology](https://futurism.com/artificial-intelligence/google-ai-deletes-entire-drive)
- **Let's Data Science**: [Google Antigravity Deletes User D Drive](https://letsdatascience.com/news/google-antigravity-deletes-user-d-drive-e6727dc7)
- **Tom's Hardware**: [Google's Agentic AI wipes user's entire HDD without permission](https://www.tomshardware.com/tech-industry/artificial-intelligence/googles-agentic-ai-wipes-users-entire-hard-drive-without-permission-after-misinterpreting-instructions-to-clear-a-cache-i-am-deeply-deeply-sorry-this-is-a-critical-failure-on-my-part)
- **Windows Central**: [Google's Agentic AI erased a developer's hard drive](https://www.windowscentral.com/artificial-intelligence/google-antigravity-ai-delete-drive)
- **TechRadar**: [Google's Antigravity AI deleted a developer's drive and then apologized](https://www.techradar.com/ai-platforms-assistants/googles-antigravity-ai-deleted-a-developers-drive-and-then-apologized)
