---
name: obsidian-update
description: Update project documents in Obsidian. Manage overview, architecture, decisions, todo, and plans. Use when user says "obsidian update", "update obsidian docs", "update project docs", "add decision", "update architecture", "update todo", "add plan".
---

# Obsidian Document Update

Update various project documents in an Obsidian vault.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Project must be initialized via `obsidian-init`

## Arguments

Parse `$ARGUMENTS` for:
- **Document type**: `overview`, `architecture`, `decisions`, `todo`, `plan` (required)
- **Project name**: (if not provided, ask user)
- **Vault name**: (if not provided, ask user or use active session context)

## Document Types

### overview

Update the project overview document.

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"

# Read current overview
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md"

# Append new content
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" content="NEW_CONTENT"

# Or update a property
$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" name="status" value="NEW_STATUS" type=text
```

### architecture

Update architecture documentation.

```bash
# Read current architecture
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_architecture.md"

# Append architecture details
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_architecture.md" content="
## New Section
ARCHITECTURE_CONTENT
"

# Update the updated date
$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_architecture.md" name="updated" value="YYYY-MM-DD" type=text
```

### decisions

Add a new decision to the decision log.

Ask the user for (use AskUserQuestion if not provided):
- Decision title
- Context (why)
- Decision (what)
- Alternatives considered
- Expected consequences

```bash
# Append new decision entry
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_decisions.md" content="
### YYYY-MM-DD: DECISION_TITLE
- **Context**: WHY_THIS_DECISION
- **Decision**: WHAT_WAS_DECIDED
- **Alternatives**: ALTERNATIVES_CONSIDERED
- **Consequences**: EXPECTED_IMPACT
"

$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_decisions.md" name="updated" value="YYYY-MM-DD" type=text
```

### todo

Update project TODO list.

Parse the action from arguments: `add`, `complete`, `list`

**Add a task:**
```bash
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md" content="
- [ ] TASK_DESCRIPTION"

$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md" name="updated" value="YYYY-MM-DD" type=text
```

**List tasks:**
```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md"
```

**Complete a task** (read, modify, rewrite with `create overwrite`):
```bash
# Read current content
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md"

# Rewrite the full file with the task marked as done
# NOTE: There is no "update" CLI command. Use "create overwrite" to replace file contents.
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md" overwrite content="FULL_UPDATED_CONTENT_WITH_TASK_MARKED_DONE"
```

> **Important**: The Obsidian CLI has no `update` command. To modify existing file content beyond appending/prepending, read the file first, modify the content, then use `create` with the `overwrite` flag to replace it.

### plan

Create or update an implementation plan.

Ask the user for:
- Plan name/title
- Plan content (or generate from conversation context)

**Create a new plan:**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/plans/PLAN_NAME.md" content="---
project: PROJECT_NAME
type: plan
title: PLAN_TITLE
created: YYYY-MM-DD
status: draft
---

# PLAN_TITLE

## Objective
PLAN_OBJECTIVE

## Approach
PLAN_APPROACH

## Steps
1. STEP_1
2. STEP_2

## Risks
- RISK_1

## Timeline
- Phase 1: DESCRIPTION
"
```

**Update existing plan:**
```bash
# List plans
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/plans"

# Read plan
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/plans/PLAN_NAME.md"

# Append updates
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/plans/PLAN_NAME.md" content="UPDATED_CONTENT"
```

## Update Active Session Log

If a session is active, also append an entry to the current session's Work Log section:

```bash
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/ACTIVE_SESSION.md" content="
- Updated TYPE_NAME: BRIEF_DESCRIPTION"
```

## Output

After updating, display:
- What was updated
- Current state of the document (brief summary)
- If session is active, note that session log was updated
