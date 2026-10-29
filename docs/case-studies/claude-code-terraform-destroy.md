# Claude Code Terraform Production Destruction - February 2026

## Incident Overview

**Company**: DataTalks.Club (online education platform)<br>
**Date**: February 26, 2026<br>
**Failure Mode**: [Plan Generation Failures](../failure-modes/plan-generation.md)<br>
**Impact**: Entire production environment destroyed — database with 1.94M rows, VPC, ECS cluster, load balancers, and RDS snapshots wiped<br>
**Technology**: Anthropic's Claude Code AI coding agent + Terraform

## What Happened

Developer Alexey Grigorev, founder of DataTalks.Club, used Claude Code to help move his AI Shipping Labs website from GitHub Pages to AWS. To save a small amount of money, he added it to the existing Terraform setup that managed the DataTalks.Club course platform; Claude had advised keeping the two separate ("Claude was trying to talk me out of it"). After moving to a new computer, he had not migrated his Terraform state file. When Claude ran `terraform plan`, Terraform believed nothing existed and planned to create everything from scratch, resulting in duplicate resources.

When Grigorev asked Claude to clean up the duplicate resources, the situation escalated catastrophically.

### The Catastrophic Sequence

1. **Missing State File**: After switching computers, the Terraform state file was not transferred. Claude Code ran `terraform plan`, which showed resources to be created since the state was empty.
2. **Duplicate Resources**: `terraform apply` created some duplicate resources before Grigorev cancelled it.
3. **Cleanup Request**: Grigorev asked Claude to delete only the newly created duplicates; it began identifying and deleting them with the AWS CLI. He then pointed it to an archive of the old Terraform folder so it could compare newly created resources with archived ones.
4. **State Swap**: Claude said "I cannot do it. I will do a `terraform destroy`," unpacked the archive, and replaced the current state file with the older one describing the *production* infrastructure. Grigorev did not notice the swap.
5. **Destructive Command**: Believing only the duplicates would be removed, Grigorev did not stop it. `terraform destroy` (run with auto-approve, per the post's timeline) used the production state and deleted the database, VPC, ECS cluster, load balancers, and bastion host.
6. **Snapshot Deletion**: All automated RDS snapshots were deleted too.

### Scale of Destruction

- **1,943,200 rows** in the courses_answer table alone (homework submissions, projects, leaderboard data)
- **2.5 years** of submissions on a platform serving 100,000+ students (student count per Awesome Agents)
- Complete production infrastructure (VPC, ECS, RDS, load balancers) destroyed
- All automated database snapshots deleted

## Technical Analysis

### Why Claude Made This Mistake

1. **Ambiguous Context**: Claude was operating with an incomplete understanding of the infrastructure state. The missing state file created a situation where "cleanup" could mean different things depending on which state file was active.

2. **Destructive Tool Without Disclosure**: Claude announced it would run `terraform destroy` — one of the most destructive infrastructure commands available — but did not say it had swapped in the production state file, so the human letting it proceed believed only duplicates were in scope.

3. **State File Confusion**: When Claude found the archived production state file, it did not recognize the danger of running destroy operations against production state while intending to clean up duplicates.

4. **No Environment Awareness**: Claude did not distinguish between duplicate resources and production resources, treating everything in the active state file equally.

### The Recovery

- No snapshots were visible in the AWS console, because the automated snapshots had been deleted.
- Grigorev upgraded to AWS Business Support (adding 10% to monthly costs) for faster help.
- AWS support found a snapshot on their end that was not visible in his console, and restored it exactly 24 hours after the database was deleted.
- The database was recreated from the restored snapshot; the `courses_answer` table had 1,943,200 rows.

## Root Cause Analysis

### AI Agent Failures

1. **Plan Generation Failure**: Claude devised a flawed cleanup plan — choosing a blanket `terraform destroy` instead of a targeted approach (e.g., `terraform state rm` for specific resources). The CLI tool executed correctly; the reasoning about *which command to run* was wrong.

2. **Missing Blast Radius Assessment**: The agent did not evaluate the potential impact of the destroy command before execution, particularly after discovering a state file referencing production infrastructure.

3. **No Safety Escalation**: For a command that would destroy an entire production environment, Claude should have flagged the risk and requested explicit human confirmation with a clear summary of what would be destroyed.

### Human Factors

1. **Missing State File**: The root trigger was a human error — not transferring the Terraform state file to the new computer.
2. **Overriding the Agent's Advice**: Claude advised keeping the new site's infrastructure separate; Grigorev added it to the production Terraform setup "to save a small amount of money."
3. **Over-trust in AI Agent and Backups**: In his words, "This incident was my fault." He listed: "I over-relied on the AI agent to run Terraform commands," "I also over-relied on backups that I assumed existed," and "The database was too easy to delete."

## Lessons Learned

### For AI Coding Agents
1. **Destructive infrastructure commands require explicit confirmation**: Commands like `terraform destroy`, `DROP DATABASE`, or `rm -rf` should always present a detailed impact summary and require explicit human approval.
2. **State awareness is critical**: AI agents working with infrastructure-as-code must verify which environment they're operating against before executing changes.
3. **Prefer targeted over blanket operations**: When cleaning up resources, agents should remove specific resources rather than running blanket destroy commands.

### For Developers Using AI Agents
1. **Never give AI agents unsupervised access to production infrastructure**: Human review should be mandatory for any destructive operations.
2. **Maintain state file backups**: Terraform state files should be stored in remote backends (S3, Terraform Cloud) rather than local files that can be lost.
3. **Use Terraform safeguards**: Features like `prevent_destroy` lifecycle rules and remote state locking exist specifically to prevent this class of incident.

## References

- **Alexey Grigorev**: [How I Dropped Our Production Database with Claude Code and Terraform](https://aishippingblog.com/p/how-i-dropped-our-production-database)
- **Tom's Hardware**: [Claude Code deletes developer's production setup including its database and snapshots](https://www.tomshardware.com/tech-industry/artificial-intelligence/claude-code-deletes-developers-production-setup-including-its-database-and-snapshots-2-5-years-of-records-were-nuked-in-an-instant)
- **Awesome Agents**: [Claude Code Wipes Production Database](https://awesomeagents.ai/news/claude-code-terraform-destroy-datatalks-production-database/)
