---
name: obsidian-cli
description: General-purpose Obsidian CLI skill for vault management. Use when user says "obsidian cli", "obsidian 에서", "vault 에서", "노트 만들어", "노트 읽어", "파일 이동", "태그 목록", "obsidian 명령", "북마크", "플러그인 관리", "데일리 노트", "템플릿".
version: 1.0.0
---

# Obsidian CLI - General Purpose Vault Management

A general-purpose skill that translates natural language requests into Obsidian CLI commands. Covers the full range of vault operations: file CRUD, folders, search, properties, tags, links, tasks, bookmarks, daily notes, templates, plugins, themes, history, workspaces, and more.

## Prerequisites

- Obsidian must be running
- CLI binary: `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- Alias for convenience: `OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"`

## Instructions

### Step 1: Parse User Intent

Analyze `$ARGUMENTS` and the user's natural language request to determine:
1. **Action category** (file CRUD, search, tags, plugins, etc.)
2. **Target** (file path, folder, tag name, plugin name, etc.)
3. **Vault** (explicit or needs selection)
4. **Parameters** (content, properties, options)

### Step 2: Select Vault

If the vault is not specified in the request, list available vaults and let the user choose:

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS vaults
```

Use AskUserQuestion to present vault options to the user.

### Step 3: Execute CLI Command

Map the user's intent to the appropriate CLI command(s) from the reference below. For compound operations, chain multiple commands.

### Step 4: Confirm Destructive Actions

**CRITICAL: Always confirm before executing destructive operations.**

The following actions require user confirmation via AskUserQuestion before execution:
- `delete` - File deletion (trash or permanent)
- `create` with `overwrite` - Overwriting existing files
- `plugin:uninstall` - Removing plugins
- `theme:uninstall` - Removing themes
- `workspace:delete` - Deleting workspaces
- `history:restore` - Restoring file versions (overwrites current)

### Step 5: Format and Display Results

Present CLI output in a readable format:
- Tables for list outputs (files, tags, plugins, etc.)
- Markdown for file content
- Confirmation messages for mutations
- Error messages with suggested fixes

## CLI Command Reference

### Vault Management

| Command | Description | Example |
|---------|-------------|---------|
| `vaults` | List all vaults | `$OBS vaults` |
| `vault` | Vault info | `$OBS vault vault=NAME info=path` |
| `reload` | Reload vault | `$OBS reload vault=NAME` |

### File CRUD

| Command | Description | Example |
|---------|-------------|---------|
| `create` | Create a note | `$OBS create vault=NAME path="folder/note.md" content="# Title"` |
| `create` (overwrite) | Overwrite a note | `$OBS create vault=NAME path="note.md" overwrite content="new content"` |
| `read` | Read a note | `$OBS read vault=NAME path="note.md"` |
| `append` | Append to note | `$OBS append vault=NAME path="note.md" content="added text"` |
| `prepend` | Prepend to note | `$OBS prepend vault=NAME path="note.md" content="top text"` |
| `delete` | Delete a note (trash) | `$OBS delete vault=NAME path="note.md"` |
| `delete` (permanent) | Delete permanently | `$OBS delete vault=NAME path="note.md" permanent` |
| `move` | Move a note | `$OBS move vault=NAME path="old/note.md" to="new/note.md"` |
| `rename` | Rename a note | `$OBS rename vault=NAME path="note.md" to="new-name.md"` |

### Folders

| Command | Description | Example |
|---------|-------------|---------|
| `folders` | List folders | `$OBS folders vault=NAME` |
| `folders` (subfolder) | List subfolders | `$OBS folders vault=NAME folder="parent"` |
| `folder` | Folder info | `$OBS folder vault=NAME folder="path"` |
| `files` | List files in folder | `$OBS files vault=NAME folder="path"` |

### Search

| Command | Description | Example |
|---------|-------------|---------|
| `search` | Text search | `$OBS search vault=NAME query="keyword"` |
| `search` (scoped) | Search in folder | `$OBS search vault=NAME query="keyword" path="folder"` |
| `search:context` | Search with context | `$OBS search:context vault=NAME query="keyword"` |
| `search:open` | Open search in Obsidian | `$OBS search:open vault=NAME query="keyword"` |

> **Note**: Use `path=` (not `folder=`) to scope searches to a specific folder.

### Properties (Frontmatter)

| Command | Description | Example |
|---------|-------------|---------|
| `properties` | List all properties | `$OBS properties vault=NAME path="note.md"` |
| `property:read` | Read a property | `$OBS property:read vault=NAME path="note.md" name="status"` |
| `property:set` | Set a property | `$OBS property:set vault=NAME path="note.md" name="status" value="done" type=text` |
| `property:remove` | Remove a property | `$OBS property:remove vault=NAME path="note.md" name="status"` |

Property types: `text`, `number`, `checkbox`, `date`, `datetime`, `list`, `tags`

### Tags

| Command | Description | Example |
|---------|-------------|---------|
| `tags` | List all tags | `$OBS tags vault=NAME` |
| `tag` | Tag info (files with tag) | `$OBS tag vault=NAME tag="tagname"` |

### Links

| Command | Description | Example |
|---------|-------------|---------|
| `links` | Outgoing links | `$OBS links vault=NAME path="note.md"` |
| `backlinks` | Incoming links | `$OBS backlinks vault=NAME path="note.md"` |
| `orphans` | Notes with no links | `$OBS orphans vault=NAME` |
| `deadends` | Notes linked to nothing | `$OBS deadends vault=NAME` |
| `unresolved` | Broken links | `$OBS unresolved vault=NAME` |

### Tasks

| Command | Description | Example |
|---------|-------------|---------|
| `tasks` | List tasks | `$OBS tasks vault=NAME` |
| `tasks` (filtered) | Filter by status | `$OBS tasks vault=NAME status=open` |
| `task` | Toggle task status | `$OBS task vault=NAME path="note.md" line=5` |

### Bookmarks

| Command | Description | Example |
|---------|-------------|---------|
| `bookmarks` | List bookmarks | `$OBS bookmarks vault=NAME` |
| `bookmark` | Add bookmark | `$OBS bookmark vault=NAME path="note.md"` |

### Daily Notes

| Command | Description | Example |
|---------|-------------|---------|
| `daily` | Open today's daily note | `$OBS daily vault=NAME` |
| `daily:read` | Read daily note | `$OBS daily:read vault=NAME` |
| `daily:read` (date) | Read specific date | `$OBS daily:read vault=NAME date=2026-02-19` |
| `daily:append` | Append to daily note | `$OBS daily:append vault=NAME content="log entry"` |
| `daily:prepend` | Prepend to daily note | `$OBS daily:prepend vault=NAME content="top entry"` |
| `daily:path` | Get daily note path | `$OBS daily:path vault=NAME` |

### Templates

| Command | Description | Example |
|---------|-------------|---------|
| `templates` | List templates | `$OBS templates vault=NAME` |
| `template:read` | Read a template | `$OBS template:read vault=NAME template="name"` |
| `template:insert` | Insert template into note | `$OBS template:insert vault=NAME path="note.md" template="name"` |

### Plugins

| Command | Description | Example |
|---------|-------------|---------|
| `plugins` | List plugins | `$OBS plugins vault=NAME` |
| `plugin:enable` | Enable a plugin | `$OBS plugin:enable vault=NAME plugin="plugin-id"` |
| `plugin:disable` | Disable a plugin | `$OBS plugin:disable vault=NAME plugin="plugin-id"` |
| `plugin:install` | Install a plugin | `$OBS plugin:install vault=NAME plugin="plugin-id"` |
| `plugin:uninstall` | Uninstall a plugin | `$OBS plugin:uninstall vault=NAME plugin="plugin-id"` |

### Themes

| Command | Description | Example |
|---------|-------------|---------|
| `themes` | List themes | `$OBS themes vault=NAME` |
| `theme` | Current theme info | `$OBS theme vault=NAME` |
| `theme:set` | Set theme | `$OBS theme:set vault=NAME theme="theme-name"` |
| `theme:install` | Install a theme | `$OBS theme:install vault=NAME theme="theme-name"` |
| `theme:uninstall` | Uninstall a theme | `$OBS theme:uninstall vault=NAME theme="theme-name"` |

### History

| Command | Description | Example |
|---------|-------------|---------|
| `history` | File version history | `$OBS history vault=NAME path="note.md"` |
| `history:read` | Read a version | `$OBS history:read vault=NAME path="note.md" version=ID` |
| `history:restore` | Restore a version | `$OBS history:restore vault=NAME path="note.md" version=ID` |

### Workspaces

| Command | Description | Example |
|---------|-------------|---------|
| `workspaces` | List workspaces | `$OBS workspaces vault=NAME` |
| `workspace:load` | Load a workspace | `$OBS workspace:load vault=NAME workspace="name"` |
| `workspace:save` | Save workspace | `$OBS workspace:save vault=NAME workspace="name"` |
| `workspace:delete` | Delete workspace | `$OBS workspace:delete vault=NAME workspace="name"` |

### Utilities

| Command | Description | Example |
|---------|-------------|---------|
| `aliases` | List note aliases | `$OBS aliases vault=NAME path="note.md"` |
| `outline` | Note heading outline | `$OBS outline vault=NAME path="note.md"` |
| `wordcount` | Word count | `$OBS wordcount vault=NAME path="note.md"` |
| `open` | Open note in Obsidian | `$OBS open vault=NAME path="note.md"` |
| `random` | Open random note | `$OBS random vault=NAME` |

## Common Workflows

### Create a note with properties

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS create vault=NAME path="notes/my-note.md" content="# My Note

Some content here."
$OBS property:set vault=NAME path="notes/my-note.md" name="tags" value="idea,draft" type=tags
$OBS property:set vault=NAME path="notes/my-note.md" name="status" value="draft" type=text
```

### Find orphaned notes and broken links

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS orphans vault=NAME
$OBS unresolved vault=NAME
```

### Daily note journaling

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS daily:append vault=NAME content="## 14:30 - Meeting Notes
- Discussed project timeline
- Agreed on MVP scope"
```

### Bulk tag inspection

```bash
OBS="/Applications/Obsidian.app/Contents/MacOS/obsidian"
$OBS tags vault=NAME
$OBS tag vault=NAME tag="project-x"
```

## Output

After executing commands, display:
- Command result in readable format
- For mutations: confirmation of what changed
- For queries: formatted table or list
- For errors: error message with suggested alternative command
