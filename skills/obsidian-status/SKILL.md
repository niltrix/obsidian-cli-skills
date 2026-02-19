---
name: obsidian-status
description: Show project status and overview from Obsidian. Lists projects, session counts, TODO status, and recent activity. Use when user says "obsidian status", "project status", "show projects", "what projects do I have", "obsidian overview".
version: 1.0.0
---

# Obsidian Project Status

Display project status, session statistics, and TODO overview from Obsidian vault.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`

## Arguments

Parse `$ARGUMENTS` for:
- **Project name**: (optional - if provided, show detailed status for that project)
- **Vault name**: (optional - if not provided, check all vaults or ask user)

## All Projects Overview

When no specific project is given, show overview of all projects:

### Step 1: List Vaults and Projects

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"

# Check each known vault for AI-Projects folder
for VAULT in Personal medit-public medit; do
  echo "=== $VAULT ==="
  $OBS folders vault=$VAULT folder="AI-Projects" 2>/dev/null || echo "No AI-Projects folder"
done
```

Or let user choose a vault first, then list:

```bash
$OBS folders vault=VAULT_NAME folder="AI-Projects"
```

### Step 2: For Each Project, Gather Stats

For each project found:

```bash
# Get session count
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/sessions"

# Get plan count
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/plans"

# Read overview for status
$OBS property:read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" name="status"

# Read TODO for task counts
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md"
```

Count tasks:
- Count lines matching `- [ ]` → pending tasks
- Count lines matching `- [x]` → completed tasks

### Step 3: Present Overview Table

```
## Obsidian Projects Status

| Vault | Project | Status | Sessions | Plans | TODO (Open/Done) | Last Activity |
|-------|---------|--------|----------|-------|-------------------|---------------|
| Personal | my-app | active | 5 | 2 | 3/7 | 2026-02-19 |
| Personal | api-v2 | paused | 3 | 1 | 1/4 | 2026-02-15 |
| medit | dashboard | active | 8 | 3 | 5/12 | 2026-02-18 |
```

## Single Project Detail

When a specific project is provided:

### Step 1: Load All Project Info

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"

# Overview
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md"

# TODO
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md"

# Sessions list
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/sessions"

# Plans list
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/plans"

# Recent decisions (last few)
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_decisions.md"

# Tags on overview
$OBS property:read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" name="tags"
```

### Step 2: Read Latest Session

```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/LATEST_SESSION.md"
```

### Step 3: Present Detailed Status

```
## Project: PROJECT_NAME

### Basic Info
- **Vault**: VAULT_NAME
- **Status**: STATUS
- **Created**: CREATED_DATE
- **Tags**: TAG_LIST

### Sessions (TOTAL_COUNT total)
| Session | Date | Status | Goals |
|---------|------|--------|-------|
| 2026-02-19-001 | 2026-02-19 | completed | Feature X |
| 2026-02-18-001 | 2026-02-18 | completed | Bug fixes |

### Open TODOs (COUNT)
- [ ] TODO_1
- [ ] TODO_2

### Completed TODOs (COUNT)
- [x] DONE_1
- [x] DONE_2

### Plans (COUNT)
- plan-name-1 (status: draft)
- plan-name-2 (status: approved)

### Recent Decisions
- 2026-02-19: Decision about X
- 2026-02-17: Decision about Y

### Last Session Summary
LAST_SESSION_SUMMARY
```

## Output

Display the formatted status table (all projects) or detailed project view.
