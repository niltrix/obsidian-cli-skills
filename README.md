# obsidian-skills

A Claude Code plugin that integrates Obsidian CLI for managing project documents, session logs, and knowledge bases across multiple vaults. Seamlessly track your work sessions, maintain project context, and reference vault documents directly in Claude conversations.

**Version:** 1.0.0  
**Author:** bjlee  
**License:** MIT  
**Category:** Productivity

## Overview

obsidian-skills provides seven powerful skills that turn Obsidian into a project management and documentation hub for your Claude Code workflows. Initialize projects with structured templates, track work sessions with detailed logs, update project documents, resume from where you left off, and reference vault files directly in your prompts.

### Key Features

- **Project Initialization** - Set up new projects with structured folders and template documents
- **Session Tracking** - Start and end work sessions with automatic logging and context preservation
- **Document Management** - Update project overviews, architecture, decisions, TODOs, and plans
- **Context Resume** - Restore full project context from vault for seamless continuation
- **Status Monitoring** - View project overview, session counts, and TODO progress
- **Document Search** - Search across all project documents with filtering options
- **Vault References** - Inject vault file content directly into prompts with `@obsidian:` syntax

## Prerequisites

Before using obsidian-skills, ensure:

- **Obsidian Desktop App** is installed
- **Obsidian CLI** is available at `/Applications/Obsidian.app/Contents/MacOS/obsidian`
- **Claude Code** is installed and configured

Verify the Obsidian CLI is accessible:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian vaults
```

## Installation

Choose one of three installation methods:

### Method 1: Local Marketplace (Recommended for Development)

```bash
claude plugin marketplace add /path/to/obsidian-skills
claude plugin install obsidian-skills
```

### Method 2: Session-Only Loading

Load the plugin for the current session without permanent installation:

```bash
claude --plugin-dir /path/to/obsidian-skills
```

### Method 3: GitHub Installation (For Sharing)

```bash
claude plugin marketplace add github:bjlee/obsidian-skills
claude plugin install obsidian-skills
```

## Skills Reference

### 1. obsidian-init

Initialize a new project in your Obsidian vault with structured folders and template documents.

**Triggers:** "obsidian init", "init project in obsidian", "create obsidian project"

**Usage:**

```
/obsidian-skills:obsidian-init my-project
```

**What it does:**
- Prompts you to select a vault
- Creates project folder structure under `AI-Projects/PROJECT_NAME/`
- Generates template documents:
  - `_overview.md` - Project metadata and quick links
  - `_architecture.md` - System architecture documentation
  - `_decisions.md` - Decision log
  - `_todo.md` - Task tracking
  - `sessions/` - Directory for session logs
  - `plans/` - Directory for implementation plans

**Created Structure:**

```
AI-Projects/
└── PROJECT_NAME/
    ├── _overview.md        # Project metadata and quick links
    ├── _architecture.md    # System architecture documentation
    ├── _decisions.md       # Decision log
    ├── _todo.md            # Task tracking
    ├── sessions/
    │   └── _index.md       # Session log index
    └── plans/
        └── _index.md       # Plans index
```

### 2. obsidian-session

Manage work sessions with automatic logging and context preservation. Track session goals, accomplishments, decisions, and carry-forward items.

**Triggers:** "obsidian session start", "obsidian session end", "start session", "end session"

**Usage:**

```
/obsidian-skills:obsidian-session start my-project
/obsidian-skills:obsidian-session end
```

**Start Session:**
- Selects or prompts for project
- Asks for session goals
- Creates session log: `YYYY-MM-DD-NNN.md`
- Updates project overview with recent activity

**End Session:**
- Prompts for accomplishments summary
- Records completed items
- Identifies carry-forward tasks
- Updates project TODO with pending items
- Marks session as complete

**Session Log Format:**

```
---
project: PROJECT_NAME
type: session
session: 2026-02-19-001
started: 2026-02-19T10:30:00
status: completed
ended: 2026-02-19T12:00:00
goals:
  - Implement authentication
  - Write unit tests
---

# Session 2026-02-19-001

## Goals
- Implement authentication
- Write unit tests

## Work Log
- Created auth middleware
- Added tests for login flow

## Decisions Made
- Using JWT for session tokens

## Summary
Completed authentication implementation...
```

### 3. obsidian-update

Update project documents in real-time. Manage overview, architecture, decisions, TODO lists, and implementation plans.

**Triggers:** "obsidian update", "update obsidian docs", "add decision", "update architecture", "update todo"

**Usage:**

```
/obsidian-skills:obsidian-update todo add "Implement authentication"
/obsidian-skills:obsidian-update decisions
/obsidian-skills:obsidian-update architecture
```

**Update Types:**

- **overview** - Project metadata and status
- **architecture** - System design and components
- **decisions** - Decision log entries
- **todo** - Task management
- **plan** - Implementation plans

**Features:**
- Read current document state before updates
- Append new entries or modify existing properties
- Auto-update timestamps
- If a session is active, appends entry to session work log

### 4. obsidian-resume

Restore complete project context from vault for seamless work continuation. Loads overview, recent sessions, TODOs, decisions, and architecture.

**Triggers:** "obsidian resume", "resume project", "load project context", "continue project"

**Usage:**

```
/obsidian-skills:obsidian-resume my-project
```

**Loaded Context:**
- Project overview and status
- Last 1-3 session summaries with goals and accomplishments
- Open and completed TODO items
- Key decisions (recent entries)
- Architecture documentation (optional)
- Carry-forward items from last session
- Next steps to continue work

**Resume Output Example:**

```
## Project Resume: my-project

### Status: active

### Project Overview
Full project description and metadata...

### Last Session (2026-02-19-001)
- Date: 2026-02-19
- Goals: Implement auth, write tests
- Accomplished: Auth middleware complete
- Carry Forward: Unit test coverage

### Open TODOs (3)
- [ ] Add password reset flow
- [ ] Set up 2FA
- [ ] Write integration tests

### Next Steps (from last session)
1. Implement password reset
2. Add 2FA verification
3. Complete test coverage
```

**Optional Features:**
- Auto-start new session prompt
- Load related decisions and architecture

### 5. obsidian-status

Display project status overview showing session counts, TODO progress, and recent activity across all projects.

**Triggers:** "obsidian status", "project status", "show projects", "obsidian overview"

**Usage:**

```
/obsidian-skills:obsidian-status
/obsidian-skills:obsidian-status my-project
```

**All Projects View:**

Shows a table of all projects across all vaults:

| Vault | Project | Status | Sessions | Plans | TODO (Open/Done) | Last Activity |
|-------|---------|--------|----------|-------|-------------------|---------------|
| Personal | my-app | active | 5 | 2 | 3/7 | 2026-02-19 |
| Personal | api-v2 | paused | 3 | 1 | 1/4 | 2026-02-15 |

**Single Project Detail:**

Shows comprehensive stats:
- Project metadata (vault, status, created date, tags)
- Session history with dates, status, and goals
- Open and completed TODO breakdown
- Plans list with statuses
- Recent decisions
- Last session summary

### 6. obsidian-search

Search across project documents with flexible filtering. Find content in sessions, plans, decisions, and notes.

**Triggers:** "obsidian search", "search in obsidian", "find in obsidian", "search project docs"

**Usage:**

```
/obsidian-skills:obsidian-search "authentication" project=my-project
/obsidian-skills:obsidian-search "bug fix" type=sessions
/obsidian-skills:obsidian-search "database" vault=Personal
```

**Search Options:**
- **Query** (required) - Search text
- **Project** (optional) - Limit to specific project
- **Vault** (optional) - Specific vault to search
- **Type** (optional) - `sessions`, `plans`, `decisions`, `todo`, or `all`

**Search Features:**
- Context-aware results showing matching lines
- Results grouped by project and document type
- Option to read or open specific files
- Support for Obsidian task queries

**Search Results Format:**

```
## Search Results for "authentication"

### Project: my-project

#### Sessions
- **2026-02-19-001.md**: "...implemented OAuth authentication..."
- **2026-02-18-001.md**: "...discussed auth strategy..."

#### Plans
- **auth-implementation.md**: "...authentication layer..."

#### Decisions
- **_decisions.md**: "...decided on JWT tokens..."
```

### 7. obsidian-ref

Reference Obsidian vault documents directly in prompts using `@obsidian:` syntax. Read and inject vault content into conversation context.

**Triggers:** "@obsidian:" in prompt, "obsidian ref", "get from obsidian"

**Usage:**

```
# Display vault content
/obsidian-skills:obsidian-ref @obsidian:Personal/ai/my.md

# Fetch and apply to local file
CLAUDE.md를 @obsidian:Personal/ai/my.md 가져와서 설정해

# Multiple references
/obsidian-skills:obsidian-ref @obsidian:/templates/a.md @obsidian:/templates/b.md
```

**Reference Syntax:**

| Format | Example | Meaning |
|--------|---------|---------|
| `@obsidian:VAULT/PATH` | `@obsidian:Personal/ai/my.md` | Explicit vault and path |
| `@obsidian:/PATH` | `@obsidian:/ai/my.md` | Default vault (from session context or prompted) |

**Actions:**
- **Display only** - Show vault content in conversation
- **Write to file** - Apply vault content to local target file
- **Append** - Add vault content to existing file

**Examples:**

```
# Simple read
@obsidian:Personal/ai/my.md
Displays the file content in conversation

# Fetch and configure
rules.md에 @obsidian:Work/rules/coding.md 가져와서 설정해
Reads coding.md from Work vault, writes to ./rules.md

# Multiple files
@obsidian:/templates/config.md @obsidian:/templates/setup.md
Reads both files from default vault, displays them
```

## Typical Workflow

Follow this workflow to maximize the benefits of obsidian-skills:

### Day 1: Initialize Project

```
/obsidian-skills:obsidian-init my-app
```

Set up your project with all necessary documents and folders.

### Start Work: Begin Session

```
/obsidian-skills:obsidian-session start my-app
```

Specify goals for this work session. This creates a session log that tracks your progress.

### During Work: Update Documents

As you work, update project documents:

```
/obsidian-skills:obsidian-update todo add "Set up authentication"
/obsidian-skills:obsidian-update decisions
```

### End Work: End Session

```
/obsidian-skills:obsidian-session end
```

Summarize what you accomplished, mark completed tasks, and note items to carry forward.

### Next Day: Resume Work

```
/obsidian-skills:obsidian-resume my-app
```

Load full context from vault - you instantly see:
- What you worked on last session
- What you accomplished
- What tasks remain open
- Next steps to continue

### Check Status Anytime

```
/obsidian-skills:obsidian-status
```

See overview of all projects, recent activity, and TODO progress.

### Search When Needed

```
/obsidian-skills:obsidian-search "error handling" project=my-app
```

Find relevant decisions, session notes, or plans.

### Reference Vault Content

In any Claude conversation, inject vault documents:

```
@obsidian:Personal/ai/my.md
```

This loads the file directly into your prompt context.

## Project Structure Reference

Projects created by obsidian-init follow this structure:

```
AI-Projects/
└── PROJECT_NAME/
    ├── _overview.md
    │   └── Project metadata, status, quick links
    │       Last updated timestamps and activity log
    │
    ├── _architecture.md
    │   └── System design, components, tech stack
    │       Design decisions and rationale
    │
    ├── _decisions.md
    │   └── Decision log with context, alternatives, consequences
    │       Timestamped entries for traceability
    │
    ├── _todo.md
    │   └── Task tracking with sections:
    │       In Progress, Pending, Completed
    │
    ├── sessions/
    │   ├── _index.md
    │   ├── 2026-02-19-001.md
    │   ├── 2026-02-19-002.md
    │   └── 2026-02-18-001.md
    │       └── Each session contains:
    │           Goals, Work Log, Decisions Made,
    │           Issues Encountered, Summary
    │
    └── plans/
        ├── _index.md
        ├── feature-auth.md
        └── performance-optimization.md
            └── Implementation plans with:
                Objective, Approach, Steps, Risks, Timeline
```

## Document Reference

### _overview.md

Project metadata and quick reference. Updated with recent activity logs.

```markdown
---
project: PROJECT_NAME
created: 2026-02-19
status: active
vault: VAULT_NAME
tags:
  - ai-project
  - active
---

# PROJECT_NAME

## Overview
Project description

## Quick Links
- [[_architecture|Architecture]]
- [[_decisions|Decisions Log]]
- [[_todo|TODO]]

## Recent Activity
- 2026-02-19: Session 001 started - Setup and initialization
```

### _architecture.md

System design and technical decisions. Link from decisions log for detailed context.

```markdown
---
project: PROJECT_NAME
type: architecture
updated: 2026-02-19
---

# Architecture - PROJECT_NAME

## System Overview
High-level architecture description

## Key Components
Component descriptions and responsibilities

## Technology Stack
Technologies, frameworks, libraries used

## Design Decisions
Link to _decisions.md for detailed log
```

### _decisions.md

Timestamped decision log with context and rationale.

```markdown
---
project: PROJECT_NAME
type: decisions
updated: 2026-02-19
---

# Decision Log - PROJECT_NAME

### 2026-02-19: Use JWT for Authentication
- **Context**: Need secure session management
- **Decision**: Implement JWT-based authentication
- **Alternatives**: Session cookies, OAuth delegation
- **Consequences**: Stateless sessions, simplified scaling
```

### _todo.md

Task tracking with completion status.

```markdown
---
project: PROJECT_NAME
type: todo
updated: 2026-02-19
---

# TODO - PROJECT_NAME

## In Progress
- [ ] Implement API authentication

## Pending
- [ ] Add password reset flow

## Completed
- [x] Project initialization
- [x] Set up development environment
```

## Troubleshooting

### Obsidian CLI Not Found

If you see "obsidian command not found", verify the path:

```bash
ls -la /Applications/Obsidian.app/Contents/MacOS/obsidian
```

If the path differs, update the plugin configuration or create an alias:

```bash
alias obsidian="/Applications/Obsidian.app/Contents/MacOS/obsidian"
```

### Vault Not Listed

Ensure Obsidian is running:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian vaults
```

If no vaults appear, open Obsidian desktop app first.

### Session Context Not Found

When resuming work, the plugin looks for the most recent session. If none exist, you'll be prompted to start a new project or select an existing one.

### File Not Found Errors

Verify the project was initialized:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME"
```

If files are missing, run obsidian-init again.

### Reference Syntax Issues

Ensure `@obsidian:` references use correct format:
- With vault: `@obsidian:VaultName/path/to/file.md`
- Default vault: `@obsidian:/path/to/file.md`

Paths are case-sensitive in Obsidian.

## Configuration

The plugin works with Obsidian's default CLI. Key configuration is stored in:

```
.claude-plugin/
├── plugin.json          # Plugin metadata and manifest
└── marketplace.json     # Marketplace listing configuration
```

No additional setup required beyond having Obsidian installed.

## Advanced Usage

### Combining Multiple Skills

Resume project context, then search for related decisions:

```
1. /obsidian-skills:obsidian-resume my-app
2. /obsidian-skills:obsidian-search "error handling" project=my-app
```

### Using References in Development

Load project architecture into prompt context:

```
@obsidian:Personal/ai/my-app/_architecture.md
Implement the API layer according to this architecture
```

### Automating Session Updates

Manually update session logs during work:

```
/obsidian-skills:obsidian-update todo add "Implemented user authentication"
```

This appends to both the TODO list and the active session log.

### Cross-Vault Projects

Use the vault parameter to access projects in different vaults:

```
/obsidian-skills:obsidian-status project=api-v2 vault=Work
```

## Contributing

Contributions are welcome. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with `claude --plugin-dir ./`
5. Submit a pull request

## License

MIT License. See LICENSE file for details.

## Support

For issues, questions, or feature requests, please open an issue on the GitHub repository.

## Changelog

### v1.0.0

Initial release with 7 core skills:
- obsidian-init - Project initialization
- obsidian-session - Session management
- obsidian-update - Document updates
- obsidian-resume - Context restoration
- obsidian-status - Project overview
- obsidian-search - Document search
- obsidian-ref - Vault references

---

**obsidian-skills** - Integrate Obsidian into your Claude Code workflow for seamless project documentation and session tracking.
