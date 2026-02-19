---
name: obsidian-ref
description: Reference Obsidian vault documents in prompts. Read and inject vault file content into conversation context. Use when user mentions "@obsidian:", "obsidian ref", "get from obsidian", "obsidian 에서 가져와", or references vault files with @obsidian:path syntax.
---

# Obsidian Document Reference

Read Obsidian vault files and inject their content into the conversation context. Supports inline `@obsidian:` references and action phrases like "가져와서 설정해" (fetch and configure).

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Alias for convenience: `OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"`

## Reference Syntax

Two formats are supported:

| Format | Example | Meaning |
|--------|---------|---------|
| `@obsidian:VAULT/PATH` | `@obsidian:Personal/ai/my.md` | Explicit vault name |
| `@obsidian:/PATH` | `@obsidian:/ai/my.md` | Default vault (resolved at runtime) |

## Arguments

Parse `$ARGUMENTS` for:
- **@obsidian: references**: one or more `@obsidian:` patterns (required)
- **Target file**: local file path that the content should be written to (optional, detected from action phrases)
- **Action**: read/display only, or fetch-and-apply to a target (optional)

## Instructions

### Step 1: Parse References

Scan `$ARGUMENTS` for all `@obsidian:` patterns using this logic:

```
For each token matching @obsidian:(...):
  remainder = token after "@obsidian:"
  if remainder starts with "/" :
    vault = None  (default vault, resolve in Step 2)
    path  = remainder[1:]   # strip leading slash
  else:
    first_segment = remainder up to first "/"
    vault = first_segment
    path  = remainder after first "/"
```

Collect all `(vault, path)` pairs. If none found, inform the user that no `@obsidian:` reference was detected and stop.

Also extract any **target file** from the remaining text. Look for:
- A local file path adjacent to action phrases such as: "가져와서 설정해", "로 설정해", "에 저장해", "write to", "save to", "apply to"
- Example: `CLAUDE.md를 @obsidian:Personal/ai/my.md 가져와서 설정해` → target = `./CLAUDE.md`

### Step 2: Resolve Vault

For each reference where vault is `None`:

1. Check if there is an active Obsidian session context (e.g., from `obsidian-session` skill state) that records a default vault.
2. If a default vault is known, use it silently.
3. If no default vault is available, ask the user:

```
List available vaults first:
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS vaults
```

Then use AskUserQuestion to present the vault list and ask which vault to use for the unresolved reference(s).

### Step 3: Read Content

For each `(vault, path)` pair, read the file:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS read vault=VAULT_NAME path="FILE_PATH"
```

Store the content together with its source reference for presentation.

If a file is not found, report the missing path clearly and continue with remaining references.

### Step 4: Detect Action

Examine `$ARGUMENTS` for action intent:

| Pattern | Action |
|---------|--------|
| No action phrase, no target file | Display content in conversation |
| Action phrase + target file present | Write Obsidian content to target file |
| "가져와서 설정해", "로 설정해", "write to", "apply to", "save to" | Write to target |
| "보여줘", "show", "read", "display" | Display only |

If intent is ambiguous and a target file is mentioned, prefer write action.

### Step 5: Execute Action

**Display only** — present each file's content with clear attribution:

```
## @obsidian:VAULT/PATH

[file content here]

---
```

For multiple references, display them in order with a separator between each.

**Write to target file** — apply the Obsidian content to the local target:

1. If the target file already exists, read it first so you understand its current content.
2. Write the Obsidian content to the target path. If the user's phrasing implies full replacement (e.g., "설정해", "configure"), overwrite the file entirely. If it implies appending, append instead.
3. Confirm the write:

```
Read from : @obsidian:VAULT/PATH
Written to: TARGET_FILE_PATH
```

For multiple references written to the same target, concatenate the content in order before writing.

### Step 6: Log to Session Work Log (if active)

If an active Obsidian session is detected (`.omc/state/obsidian-session-state.json` exists or similar), append a brief entry to the session work log:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS append vault=VAULT_NAME path="AI-Projects/PROJECT/sessions/SESSION_FILE.md" content="
### obsidian-ref
- Read: VAULT/PATH
- Action: [displayed | written to TARGET]
- Time: TIMESTAMP
"
```

Skip silently if no active session is found.

## Examples

### Simple read — display content

```
/obsidian-ref @obsidian:Personal/ai/my.md
```

Reads `ai/my.md` from the `Personal` vault and displays its content in the conversation.

### Fetch and configure a local file

```
CLAUDE.md를 @obsidian:Personal/ai/my.md 가져와서 설정해
```

Reads `ai/my.md` from the `Personal` vault and writes the content to `./CLAUDE.md` in the current working directory.

### Multiple references — display all

```
/obsidian-ref @obsidian:/templates/a.md @obsidian:/templates/b.md
```

Resolves the default vault (from session context or user prompt), reads both files, and displays them sequentially.

### Default vault — ask user

```
/obsidian-ref @obsidian:/ai/my.md
```

No vault is specified. Lists available vaults and asks the user which one to use, then reads `ai/my.md` from the chosen vault.

### Write multiple refs to one target

```
rules.md에 @obsidian:Work/rules/coding.md @obsidian:Work/rules/git.md 가져와서 설정해
```

Reads both vault files and writes their combined content to `./rules.md`.

## Output

- For display actions: formatted content blocks with source attribution.
- For write actions: concise confirmation showing source reference and target path.
- For errors (file not found, vault not available): clear error message per reference, continue processing remaining references.
