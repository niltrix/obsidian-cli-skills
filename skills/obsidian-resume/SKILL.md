---
name: obsidian-resume
description: Resume a project by loading context from Obsidian. Reads project overview, recent sessions, TODO, and decisions to restore working context. Use when user says "obsidian resume", "resume project", "load project context", "continue project", "pick up where I left off".
---

# Obsidian Project Resume

Restore project context from Obsidian vault for seamless work continuation.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Project must be initialized via `obsidian-init`

## Arguments

Parse `$ARGUMENTS` for:
- **Project name**: (if not provided, list projects and ask user)
- **Vault name**: (if not provided, ask user)

## Instructions

### Step 1: Select Project

If project name not provided, list available projects:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS folders vault=VAULT_NAME folder="AI-Projects"
```

Use AskUserQuestion to let user choose a project.

### Step 2: Load Project Overview

```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md"
```

Extract:
- Project status
- Description
- Recent activity

### Step 3: Load TODO List

```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md"
```

Extract:
- In-progress tasks
- Pending tasks
- Count of completed tasks

### Step 4: Load Recent Sessions

List sessions and read the most recent 1-3:

```bash
# List all sessions
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/sessions"

# Read most recent session
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/LATEST_SESSION.md"
```

Extract from the most recent session:
- Goals and what was accomplished
- Carry-forward items
- Next steps
- Any unresolved issues

### Step 5: Load Key Decisions (Optional)

If the last session references decisions, read the decisions log:

```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_decisions.md"
```

### Step 6: Load Architecture (Optional)

If the project has architecture documentation:

```bash
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_architecture.md"
```

### Step 7: Present Resume Summary

Present a structured summary to the user:

```
## Project Resume: PROJECT_NAME

### Status: STATUS

### Project Overview
OVERVIEW_SUMMARY

### Last Session (SESSION_ID)
- **Date**: YYYY-MM-DD
- **Goals**: GOALS
- **Accomplished**: ACCOMPLISHMENTS
- **Carry Forward**: ITEMS

### Open TODOs
- [ ] TODO_1
- [ ] TODO_2

### Next Steps (from last session)
1. NEXT_STEP_1
2. NEXT_STEP_2

### Key Decisions
- LATEST_DECISION_1
- LATEST_DECISION_2
```

### Step 8: Auto-Start Session (Optional)

Ask the user if they want to start a new session:

Use AskUserQuestion:
- "Start a new session now?" → Yes / No

If yes, invoke the obsidian-session start flow.

## Output

Display the complete resume summary with all context needed to continue work. This should give the user (and Claude) full context to pick up exactly where they left off.
