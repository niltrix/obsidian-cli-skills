---
name: obsidian-init
description: Initialize a new project in Obsidian vault. Use when starting a new project, setting up project documentation, or when user says "obsidian init", "init project in obsidian", "create obsidian project".
version: 1.0.0
---

# Obsidian Project Initialization

Initialize a new project workspace in an Obsidian vault with structured folders and template documents.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Alias for convenience: `OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"`

## Instructions

### Step 1: Determine Project Name

Extract the project name from `$ARGUMENTS`. If not provided, ask the user using AskUserQuestion.

### Step 2: Select Vault

List available vaults and let the user choose:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian vaults
```

Use AskUserQuestion to present vault options to the user.

### Step 3: Create Project Folder Structure

Create the project root folder and all subfolders by creating placeholder files:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"

# Create _overview.md with project metadata
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_overview.md" content="---
project: PROJECT_NAME
created: YYYY-MM-DD
status: active
vault: VAULT_NAME
tags:
  - ai-project
  - active
---

# PROJECT_NAME

## Overview
<!-- Project description goes here -->

## Quick Links
- [[_architecture|Architecture]]
- [[_decisions|Decisions Log]]
- [[_todo|TODO]]

## Recent Activity
<!-- Auto-updated by obsidian-session skill -->
"
```

### Step 4: Create Template Documents

Create each template document:

**_architecture.md:**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_architecture.md" content="---
project: PROJECT_NAME
type: architecture
updated: YYYY-MM-DD
---

# Architecture - PROJECT_NAME

## System Overview
<!-- High-level architecture description -->

## Key Components
<!-- Component descriptions -->

## Technology Stack
<!-- Technologies used -->

## Design Decisions
<!-- Link to _decisions.md for detailed log -->
"
```

**_decisions.md:**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_decisions.md" content="---
project: PROJECT_NAME
type: decisions
updated: YYYY-MM-DD
---

# Decision Log - PROJECT_NAME

## Decisions

<!-- Format for each decision:
### YYYY-MM-DD: Decision Title
- **Context**: Why this decision was needed
- **Decision**: What was decided
- **Alternatives**: What else was considered
- **Consequences**: Expected impact
-->
"
```

**_todo.md:**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/_todo.md" content="---
project: PROJECT_NAME
type: todo
updated: YYYY-MM-DD
---

# TODO - PROJECT_NAME

## In Progress
<!-- - [ ] Task description -->

## Pending
<!-- - [ ] Task description -->

## Completed
<!-- - [x] Task description -->
"
```

**sessions/.gitkeep (placeholder for sessions folder):**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/sessions/_index.md" content="---
project: PROJECT_NAME
type: session-index
---

# Sessions - PROJECT_NAME

<!-- Session logs are automatically created by obsidian-session skill -->
<!-- Format: YYYY-MM-DD-NNN.md -->
"
```

**plans/.gitkeep (placeholder for plans folder):**
```bash
$OBS create vault=VAULT_NAME path="AI-Projects/PROJECT_NAME/plans/_index.md" content="---
project: PROJECT_NAME
type: plans-index
---

# Plans - PROJECT_NAME

<!-- Implementation plans are stored here -->
"
```

### Step 5: Confirm Completion

After all files are created, verify by listing the folder:

```bash
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME"
```

Report the created structure to the user.

## Output

After successful initialization, display:
- Vault name and project path
- List of created documents
- How to start a session: `/obsidian-skills:obsidian-session start`
- How to resume later: `/obsidian-skills:obsidian-resume PROJECT_NAME`
