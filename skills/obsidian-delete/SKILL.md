---
name: obsidian-delete
description: Delete a project from Obsidian vault. Removes all project files and folders. Use when user says "obsidian delete", "delete project", "remove project from obsidian", "프로젝트 삭제".
version: 1.0.0
---

# Obsidian Project Deletion

Delete an entire project from an Obsidian vault, including all files and empty folders.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Alias for convenience: `OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"`

## Instructions

### Step 1: Determine Project Name

Extract the project name from `$ARGUMENTS`. If not provided, list existing projects and let the user choose:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS folders vault=VAULT_NAME folder="AI-Projects"
```

Use AskUserQuestion to present project options to the user.

### Step 2: Select Vault

If vault is not specified, list available vaults and let the user choose:

```bash
$OBS vaults
```

Use AskUserQuestion to present vault options to the user.

### Step 3: Confirm Deletion

**CRITICAL: Always confirm before deleting.** Show the user:
- Vault name
- Project name and path (`AI-Projects/PROJECT_NAME`)
- List of files that will be deleted

```bash
$OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME"
```

Use AskUserQuestion with options:
- "Delete" - Proceed with deletion
- "Cancel" - Abort

### Step 4: Delete All Project Files

Delete each file in the project using the Obsidian CLI `delete` command:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"

# List all files in the project folder
FILES=$($OBS files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME")

# Delete each file
for FILE in $FILES; do
  $OBS delete vault=VAULT_NAME path="$FILE"
done
```

> **Note**: The `delete` command moves files to Obsidian's trash by default. Add `permanent` flag only if the user explicitly requests permanent deletion.

### Step 5: Remove Empty Folders

After all files are deleted, remove the empty project folder and its subfolders from the filesystem.

First, find the vault path:

```bash
$OBS vault vault=VAULT_NAME info=path
```

Then remove empty directories:

```bash
VAULT_PATH=$($OBS vault vault=VAULT_NAME info=path)
find "$VAULT_PATH/AI-Projects/PROJECT_NAME" -type d -empty -delete
# Remove the project root folder if it's now empty
rmdir "$VAULT_PATH/AI-Projects/PROJECT_NAME" 2>/dev/null
```

### Step 6: Verify Deletion

Confirm the project folder no longer exists:

```bash
$OBS folders vault=VAULT_NAME folder="AI-Projects"
```

## Output

After successful deletion, display:
- Vault name and deleted project path
- Number of files deleted
- Confirmation that folders were cleaned up
- Note: files were moved to Obsidian trash (unless `permanent` was used)
