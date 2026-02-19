---
name: obsidian-session
description: Manage work sessions in Obsidian. Start or end a session to track work progress. Use when user says "obsidian session start", "obsidian session end", "start session", "end session", "log session", or wants to track a work session in Obsidian.
version: 1.0.0
---

# Obsidian Session Management

Track work sessions with structured logs for project context preservation and resume capability.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Project must be initialized via `obsidian-init`

## Arguments

Parse `$ARGUMENTS` for:
- **Action**: `start` or `end` (required)
- **Project name**: name of the project (if not provided, ask user)
- **Vault name**: vault to use (if not provided, ask user)

## Session Start

### Step 1: Identify Project

If project name is not in arguments, list existing projects:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS folders vault=VAULT_NAME folder="AI-Projects"
```

Use AskUserQuestion to let user select a project.

### Step 2: Determine Session Number

List existing sessions to determine the next number:

```bash
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/sessions"
```

Calculate next session number: `YYYY-MM-DD-NNN` where NNN is zero-padded (001, 002, etc.) incremented from the last session of today.

### Step 3: Ask Session Goals

Use AskUserQuestion to ask the user:
- What are the goals for this session?

### Step 4: Create Session Log

```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/YYYY-MM-DD-NNN.md" content="---
project: PROJECT_NAME
type: session
session: YYYY-MM-DD-NNN
started: YYYY-MM-DDTHH:MM:SS
status: active
goals:
  - GOAL_1
  - GOAL_2
tags:
  - session
  - active
---

# Session YYYY-MM-DD-NNN

## Goals
- GOAL_1
- GOAL_2

## Work Log
<!-- Automatically updated during session -->

## Decisions Made
<!-- Key decisions during this session -->

## Issues Encountered
<!-- Problems and how they were resolved -->

## Next Steps
<!-- To be filled at session end -->
"
```

### Step 5: Update Overview

Append recent activity to the project overview:

```bash
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" content="
- **YYYY-MM-DD**: Session YYYY-MM-DD-NNN started - GOAL_SUMMARY"
```

### Step 6: Store Session Context

Save the active session info for later use. Use the notepad or remember mechanism:
- Active vault: VAULT_NAME
- Active project: PROJECT_NAME
- Active session: YYYY-MM-DD-NNN

Report to user that session has started.

## Session End

### Step 1: Identify Active Session

Read the most recent active session file. If no active session context is available, list sessions and ask user:

```bash
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME/sessions"
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/LATEST_SESSION.md"
```

### Step 2: Gather Session Summary

Use AskUserQuestion to ask:
- Brief summary of what was accomplished
- Any unfinished tasks to carry forward
- Key decisions made (if any)

Or automatically summarize from the conversation context if available.

### Step 3: Update Session Log

Append the summary and update frontmatter:

```bash
$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/SESSION_FILE.md" name="status" value="completed" type=text
$OBS property:set vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/SESSION_FILE.md" name="ended" value="YYYY-MM-DDTHH:MM:SS" type=text

$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/SESSION_FILE.md" content="
## Summary
SUMMARY_TEXT

## Completed
- COMPLETED_ITEM_1
- COMPLETED_ITEM_2

## Carry Forward
- CARRY_FORWARD_ITEM_1

## Next Steps
- NEXT_STEP_1
- NEXT_STEP_2
"
```

### Step 4: Update TODO

If there are carry-forward items, update the project TODO:

```bash
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md" content="
### From Session SESSION_ID
- [ ] CARRY_FORWARD_ITEM
"
```

### Step 5: Update Overview

```bash
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" content="
- **YYYY-MM-DD**: Session SESSION_ID completed - SUMMARY_BRIEF"
```

## Output

- **Start**: Display session ID, goals, and how to end the session
- **End**: Display summary, carry-forward items, and next steps
