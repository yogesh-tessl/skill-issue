---
name: skill-issue
description: "Audit and review all installed agent skills. Run on-demand or via cron to get a health report: skill inventory, usage tracking, version checks, dependency health, and actionable recommendations (keep, update, review, remove). Use when asked to review skills, check for skill updates, find unused skills, or audit the skill ecosystem."
---

# Skill Issue — Skill Auditor

Audit all installed skills and produce a markdown report with recommendations.

## Quick Start

```bash
# Basic — scans ./skills and ./memory
node scripts/audit.mjs

# Custom directories
SKILL_DIRS="./skills,/path/to/more/skills" MEMORY_DIR="./memory" node scripts/audit.mjs

# Save report
node scripts/audit.mjs > /tmp/skill-audit-report.md
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `SKILL_DIRS` | `./skills` | Comma-separated skill directories to scan |
| `MEMORY_DIR` | `./memory` | Dated markdown logs for usage tracking |
| `AUDIT_DAYS` | `7` | Days back to scan for usage |

## What It Checks

1. **Inventory** — Finds every subdirectory containing a `SKILL.md` with YAML frontmatter
2. **Usage** — Scans recent memory/log files for skill name mentions
3. **Health** — Verifies required binaries (`requires.bins`) and env vars (`requires.env`)
4. **Versions** — Checks ClawdHub registry if `clawdhub` CLI is available
5. **Recommendations** — keep (active+healthy), update (outdated), review (unused), remove (broken deps)

## Example Output

```markdown
# 🔍 Skill Audit Report

## Summary
- Total skills: 4
- ✅ Keep: 2
- 🔎 Review: 1
- 🗑️ Remove: 1

## Detailed Report
| # | Skill    | Bins     | Usage (7d) | Health      | Rec       |
|---|----------|----------|------------|-------------|-----------|
| 1 | weather  | curl     | 📊 5       | ✅          | ✅ keep   |
| 2 | email    | himalaya | 📊 8       | ✅          | ✅ keep   |
| 3 | notes    | —        | —          | ✅          | 🔎 review |
| 4 | voice    | sag      | —          | ❌ sag      | 🗑️ remove |
```

## Acting on Results

- **✅ keep** — Active and healthy. No action needed.
- **🔄 update** — Hub has a newer version. Run `clawdhub install <skill>` to update.
- **🔎 review** — Unused in the last `AUDIT_DAYS` days. Confirm the skill is still needed or remove it to reduce clutter.
- **🗑️ remove** — Missing required binaries. Install the dependency (`brew install <bin>` / `npm i -g <bin>`) or remove the skill if no longer needed. Verify the binary name is correct before removing.

## Safety

- **Read-only** — Never modifies, installs, or removes anything
- **Advisory only** — Recommendations require manual action
