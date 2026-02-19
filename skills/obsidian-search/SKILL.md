---
name: obsidian-search
description: Search project documents in Obsidian vault. Find content across sessions, plans, decisions, and notes. Use when user says "obsidian search", "search in obsidian", "find in obsidian", "search project docs", "search notes".
---

# Obsidian Document Search

Search across project documents in Obsidian vaults using the CLI search capabilities.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`

## Arguments

Parse `$ARGUMENTS` for:
- **Query**: search text (required)
- **Project name**: (optional - limit search to a specific project)
- **Vault name**: (optional - if not provided, search active vault or ask user)
- **Type**: (optional - `sessions`, `plans`, `decisions`, `todo`, `all`)

## Instructions

### Step 1: Determine Search Scope

If vault not specified, ask user which vault to search. If project specified, limit search scope.

### Step 2: Execute Search

Use the CLI's native `path=` parameter for folder-level filtering.

**Search all AI-Projects:**
```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS search vault=VAULT_NAME query="SEARCH_QUERY" path="AI-Projects"
```

**Search within a specific project:**
```bash
$OBS search vault=VAULT_NAME query="SEARCH_QUERY" path="AI-Projects/PROJECT_NAME"
```

**JSON output for structured parsing:**
```bash
$OBS search vault=VAULT_NAME query="SEARCH_QUERY" path="AI-Projects" format=json
```

**Read matched files for context** (preferred over `search:context` which may return empty):
```bash
# First search for matching files
$OBS search vault=VAULT_NAME query="SEARCH_QUERY" path="AI-Projects/PROJECT_NAME"

# Then read each matched file for context
$OBS read vault=VAULT_NAME path="MATCHED_FILE_PATH"
```

> **Note**: `search:context` exists but may not return results reliably. Prefer using `search` to find matching files, then `read` each file for full context.

### Step 3: Filter by Type (if type specified)

Use `path=` parameter to narrow to specific document types:
- `sessions` → `path="AI-Projects/PROJECT_NAME/sessions"`
- `plans` → `path="AI-Projects/PROJECT_NAME/plans"`
- `decisions` → search then filter to `_decisions.md`
- `todo` → search then filter to `_todo.md`
- `all` → `path="AI-Projects/PROJECT_NAME"` (default)

### Step 5: Present Results

Format results grouped by project and document type:

```
## Search Results for "QUERY"

### Project: PROJECT_NAME_1

#### Sessions
- **2026-02-19-001.md**: "...matching line context..."
- **2026-02-18-001.md**: "...matching line context..."

#### Plans
- **feature-x.md**: "...matching line context..."

#### Decisions
- **_decisions.md**: "...matching line context..."

### Project: PROJECT_NAME_2
...
```

### Step 6: Offer to Open or Read

After showing results, ask the user if they want to:
- Read a specific file
- Open a file in Obsidian

```bash
# Read a result
$OBS read vault=VAULT_NAME path="AI-Projects/PROJECT/FILE_PATH"

# Open in Obsidian
$OBS open vault=VAULT_NAME path="AI-Projects/PROJECT/FILE_PATH"
```

## Advanced Search

### Search by Tags

```bash
$OBS tags vault=VAULT_NAME
$OBS search vault=VAULT_NAME query="tag:#TAG_NAME"
```

### Search by Properties

```bash
# Find files with specific property values
$OBS search vault=VAULT_NAME query="status: active"
$OBS search vault=VAULT_NAME query="project: PROJECT_NAME"
```

### Search Tasks

```bash
# List all tasks in vault
$OBS tasks vault=VAULT_NAME

# Filter tasks by status (bare flags, not filter=)
$OBS tasks vault=VAULT_NAME todo
$OBS tasks vault=VAULT_NAME done

# Search for tasks within a project folder (tasks command has no folder= parameter)
$OBS search vault=VAULT_NAME query="- [ ]" path="AI-Projects/PROJECT_NAME"
$OBS search vault=VAULT_NAME query="- [x]" path="AI-Projects/PROJECT_NAME"
```

## Output

Display formatted search results with context, grouped by project and type. Offer options to read or open files.
