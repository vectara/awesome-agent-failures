# Claude Code Terraform Production Destruction - February 2026

## Incident Overview

**Company**: DataTalks.Club (online education platform)<br>
**Date**: February 26, 2026<br>
**Failure Mode**: [Plan Generation Failures](../failure-modes/plan-generation.md)<br>
**Impact**: Entire production environment destroyed — database with 1.94M rows, VPC, ECS cluster, load balancers, and RDS snapshots wiped<br>
**Technology**: Anthropic's Claude Code AI coding agent + Terraform

## What Happened

Developer Alexey Grigorev used Claude Code to help migrate the DataTalks.Club website from GitHub Pages to AWS. After moving to a new computer, he forgot to transfer his Terraform state file. When Claude ran `terraform plan`, it saw no existing infrastructure and assumed everything needed to be created from scratch, resulting in duplicate resources.

When Grigorev asked Claude to clean up the duplicate resources, the situation escalated catastrophically.

### The Catastrophic Sequence

1. **Missing State File**: After switching computers, the Terraform state file was not transferred. Claude Code ran `terraform plan` which showed all infrastructure as "new" since the state was empty.
2. **Duplicate Resources**: Claude created duplicate AWS resources (VPC, ECS, etc.) alongside the existing production infrastructure.
3. **Cleanup Request**: Grigorev asked Claude to clean up the duplicates.
4. **State File Discovery**: Claude unpacked an archived Terraform state file that contained references to the *production* infrastructure.
5. **Destructive Command**: Claude executed `terraform destroy`, which — using the production state file — deleted the entire production environment: the RDS database, VPC, ECS cluster, load balancers, and bastion host.
6. **Snapshot Deletion**: Automated RDS snapshots were also destroyed as part of the cascade.

### Scale of Destruction

- **1,943,200 rows** in the courses_answer table alone (homework submissions, projects, leaderboard data)
- **2.5 years** of student data for 100,000+ students
- Complete production infrastructure (VPC, ECS, RDS, load balancers) destroyed
- All automated database snapshots deleted

## Technical Analysis

### Why Claude Made This Mistake

1. **Ambiguous Context**: Claude was operating with an incomplete understanding of the infrastructure state. The missing state file created a situation where "cleanup" could mean different things depending on which state file was active.

2. **Destructive Tool Without Confirmation**: Claude executed `terraform destroy` — one of the most destructive infrastructure commands available — without adequate consideration of the blast radius or requesting explicit confirmation of *which* resources to destroy.

3. **State File Confusion**: When Claude found the archived production state file, it did not recognize the danger of running destroy operations against production state while intending to clean up duplicates.

4. **No Environment Awareness**: Claude lacked the ability to distinguish between development/duplicate resources and production resources, treating all Terraform-managed infrastructure equally.

### The Recovery

- Initial recovery attempts failed because automated snapshots were also destroyed.
- Grigorev upgraded to AWS Business Support (adding 10% to monthly costs).
- After 24 hours, AWS support located a surviving backup snapshot.
- The complete database was restored from this snapshot.

## Root Cause Analysis

### AI Agent Failures

1. **Plan Generation Failure**: Claude devised a flawed cleanup plan — choosing a blanket `terraform destroy` instead of a targeted approach (e.g., `terraform state rm` for specific resources). The CLI tool executed correctly; the reasoning about *which command to run* was wrong.

2. **Missing Blast Radius Assessment**: The agent did not evaluate the potential impact of the destroy command before execution, particularly after discovering a state file referencing production infrastructure.

3. **No Safety Escalation**: For a command that would destroy an entire production environment, Claude should have flagged the risk and requested explicit human confirmation with a clear summary of what would be destroyed.

### Human Factors

1. **Missing State File**: The root trigger was a human error — not transferring the Terraform state file to the new computer.
2. **Over-trust in AI Agent**: The developer allowed Claude to execute infrastructure commands without sufficient oversight of each step.

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

- **Alexey's Substack**: [How I Dropped Our Production Database with Claude Code and Terraform](https://alexeyondata.substack.com/p/how-i-dropped-our-production-database)
- **Tom's Hardware**: [Claude Code deletes developer's production setup including its database and snapshots](https://www.tomshardware.com/tech-industry/artificial-intelligence/claude-code-deletes-developers-production-setup-including-its-database-and-snapshots-2-5-years-of-records-were-nuked-in-an-instant)
- **Awesome Agents**: [Claude Code Wipes Production Database](https://awesomeagents.ai/news/claude-code-terraform-destroy-datatalks-production-database/)
