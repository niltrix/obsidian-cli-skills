# obsidian-skills

Obsidian CLI를 통합하여 여러 vault에 걸쳐 프로젝트 문서, 세션 로그, 지식 베이스를 관리하고, 범용 vault 작업(파일/폴더 CRUD, 태그, 북마크, 플러그인, 템플릿, 데일리 노트 등)을 수행하는 Claude Code plugin입니다. 작업 세션을 추적하고, 프로젝트 컨텍스트를 유지하며, Claude 대화에서 vault 문서를 직접 참조할 수 있습니다.

**버전:** 1.1.0
**작성자:** bjlee
**라이선스:** MIT
**카테고리:** 생산성

## 개요

obsidian-skills는 Obsidian을 Claude Code 워크플로우의 프로젝트 관리 및 문서화 허브로 만들어주는 9가지 강력한 skill을 제공합니다. 구조화된 템플릿으로 프로젝트를 초기화하고, 상세한 로그로 작업 세션을 추적하며, 프로젝트 문서를 업데이트하고, 이전에 작업하던 내용을 이어 시작하고, vault 파일을 프롬프트에 직접 참조할 수 있습니다. 또한 범용 CLI 스킬을 통해 파일 CRUD, 태그, 북마크, 플러그인, 템플릿, 데일리 노트 등 Obsidian vault의 모든 작업을 수행할 수 있습니다.

### 주요 기능

- **범용 Vault 관리** - Obsidian CLI 전체 명령어를 활용한 파일/폴더/태그/플러그인/템플릿 등 관리
- **프로젝트 초기화** - 구조화된 폴더와 템플릿 문서로 새 프로젝트를 설정합니다
- **세션 추적** - 자동 로깅과 컨텍스트 보존으로 작업 세션을 시작하고 종료합니다
- **문서 관리** - 프로젝트 개요, 아키텍처, 결정 사항, TODO, 계획을 업데이트합니다
- **컨텍스트 복원** - vault에서 전체 프로젝트 컨텍스트를 불러와 원활하게 작업을 이어갑니다
- **상태 모니터링** - 프로젝트 개요, 세션 수, TODO 진행 상황을 확인합니다
- **문서 검색** - 필터링 옵션을 사용해 모든 프로젝트 문서를 검색합니다
- **Vault 참조** - `@obsidian:` 문법으로 vault 파일 내용을 프롬프트에 직접 주입합니다
- **프로젝트 삭제** - vault에서 프로젝트 전체를 안전하게 삭제합니다

## 사전 요구사항

obsidian-skills를 사용하기 전에 다음 사항을 확인하세요:

- **Obsidian 데스크탑 앱**이 설치되어 있어야 합니다
- **Obsidian CLI**가 `/Applications/Obsidian.app/Contents/MacOS/obsidian`에 있어야 합니다
- **Claude Code**가 설치 및 설정되어 있어야 합니다

Obsidian CLI 접근 가능 여부를 확인하세요:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian vaults
```

## 설치

```bash
claude plugin marketplace add github:bjlee2024/obsidian-cli-skills
claude plugin install obsidian-skills
```

## Skill 레퍼런스

### 1. obsidian-init

Obsidian vault에 구조화된 폴더와 템플릿 문서로 새 프로젝트를 초기화합니다.

**트리거:** "obsidian init", "init project in obsidian", "create obsidian project"

**사용법:**

```
/obsidian-skills:obsidian-init my-project
```

**수행 작업:**
- vault 선택을 요청합니다
- `AI-Projects/PROJECT_NAME/` 아래에 프로젝트 폴더 구조를 생성합니다
- 다음 템플릿 문서를 생성합니다:
  - `_overview.md` - 프로젝트 메타데이터 및 빠른 링크
  - `_architecture.md` - 시스템 아키텍처 문서
  - `_decisions.md` - 결정 로그
  - `_todo.md` - 작업 추적
  - `sessions/` - 세션 로그 디렉터리
  - `plans/` - 구현 계획 디렉터리

**생성 구조:**

```
AI-Projects/
└── PROJECT_NAME/
    ├── _overview.md        # 프로젝트 메타데이터 및 빠른 링크
    ├── _architecture.md    # 시스템 아키텍처 문서
    ├── _decisions.md       # 결정 로그
    ├── _todo.md            # 작업 추적
    ├── sessions/
    │   └── _index.md       # 세션 로그 인덱스
    └── plans/
        └── _index.md       # 계획 인덱스
```

### 2. obsidian-session

자동 로깅과 컨텍스트 보존으로 작업 세션을 관리합니다. 세션 목표, 성과, 결정 사항, 이월 항목을 추적합니다.

**트리거:** "obsidian session start", "obsidian session end", "start session", "end session"

**사용법:**

```
/obsidian-skills:obsidian-session start my-project
/obsidian-skills:obsidian-session end
```

**세션 시작:**
- 프로젝트를 선택하거나 선택을 요청합니다
- 세션 목표를 입력받습니다
- `YYYY-MM-DD-NNN.md` 형식의 세션 로그를 생성합니다
- 최근 활동으로 프로젝트 개요를 업데이트합니다

**세션 종료:**
- 성과 요약을 입력받습니다
- 완료된 항목을 기록합니다
- 이월 작업을 식별합니다
- 보류 중인 항목으로 프로젝트 TODO를 업데이트합니다
- 세션을 완료로 표시합니다

**세션 로그 형식:**

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

프로젝트 문서를 실시간으로 업데이트합니다. 개요, 아키텍처, 결정 사항, TODO 목록, 구현 계획을 관리합니다.

**트리거:** "obsidian update", "update obsidian docs", "add decision", "update architecture", "update todo"

**사용법:**

```
/obsidian-skills:obsidian-update todo add "Implement authentication"
/obsidian-skills:obsidian-update decisions
/obsidian-skills:obsidian-update architecture
```

**업데이트 유형:**

- **overview** - 프로젝트 메타데이터 및 상태
- **architecture** - 시스템 설계 및 컴포넌트
- **decisions** - 결정 로그 항목
- **todo** - 작업 관리
- **plan** - 구현 계획

**기능:**
- 업데이트 전 현재 문서 상태를 읽습니다
- 새 항목을 추가하거나 기존 속성을 수정합니다
- 타임스탬프를 자동 업데이트합니다
- 세션이 활성화된 경우 세션 작업 로그에 항목을 추가합니다

### 4. obsidian-resume

vault에서 전체 프로젝트 컨텍스트를 불러와 원활하게 작업을 이어갑니다. 개요, 최근 세션, TODO, 결정 사항, 아키텍처를 로드합니다.

**트리거:** "obsidian resume", "resume project", "load project context", "continue project"

**사용법:**

```
/obsidian-skills:obsidian-resume my-project
```

**로드되는 컨텍스트:**
- 프로젝트 개요 및 상태
- 최근 1~3개 세션 요약 (목표 및 성과 포함)
- 미완료 및 완료된 TODO 항목
- 주요 결정 사항 (최근 항목)
- 아키텍처 문서 (선택 사항)
- 마지막 세션에서 이월된 항목
- 작업을 이어가기 위한 다음 단계

**복원 출력 예시:**

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

**선택 기능:**
- 새 세션 시작 프롬프트
- 관련 결정 사항 및 아키텍처 로드

### 5. obsidian-status

세션 수, TODO 진행 상황, 모든 프로젝트의 최근 활동을 보여주는 프로젝트 상태 개요를 표시합니다.

**트리거:** "obsidian status", "project status", "show projects", "obsidian overview"

**사용법:**

```
/obsidian-skills:obsidian-status
/obsidian-skills:obsidian-status my-project
```

**전체 프로젝트 보기:**

모든 vault에 걸쳐 모든 프로젝트를 표로 보여줍니다:

| Vault | 프로젝트 | 상태 | 세션 | 계획 | TODO (미완/완료) | 최근 활동 |
|-------|---------|------|------|------|-----------------|----------|
| Personal | my-app | active | 5 | 2 | 3/7 | 2026-02-19 |
| Personal | api-v2 | paused | 3 | 1 | 1/4 | 2026-02-15 |

**단일 프로젝트 상세 보기:**

종합 통계를 표시합니다:
- 프로젝트 메타데이터 (vault, 상태, 생성 날짜, 태그)
- 날짜, 상태, 목표가 포함된 세션 이력
- 미완료 및 완료된 TODO 분류
- 상태가 포함된 계획 목록
- 최근 결정 사항
- 마지막 세션 요약

### 6. obsidian-search

유연한 필터링으로 프로젝트 문서를 검색합니다. 세션, 계획, 결정 사항, 노트에서 내용을 찾습니다.

**트리거:** "obsidian search", "search in obsidian", "find in obsidian", "search project docs"

**사용법:**

```
/obsidian-skills:obsidian-search "authentication" project=my-project
/obsidian-skills:obsidian-search "bug fix" type=sessions
/obsidian-skills:obsidian-search "database" vault=Personal
```

**검색 옵션:**
- **Query** (필수) - 검색할 텍스트
- **Project** (선택) - 특정 프로젝트로 범위 제한
- **Vault** (선택) - 검색할 특정 vault
- **Type** (선택) - `sessions`, `plans`, `decisions`, `todo`, 또는 `all`

**검색 기능:**
- 일치하는 줄을 보여주는 컨텍스트 인식 결과
- 프로젝트 및 문서 유형별로 그룹화된 결과
- 특정 파일을 읽거나 열 수 있는 옵션
- Obsidian 작업 쿼리 지원

**검색 결과 형식:**

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

`@obsidian:` 문법을 사용해 프롬프트에서 Obsidian vault 문서를 직접 참조합니다. vault 내용을 읽어 대화 컨텍스트에 주입합니다.

**트리거:** 프롬프트에 "@obsidian:" 포함, "obsidian ref", "get from obsidian"

**사용법:**

```
# vault 내용 표시
/obsidian-skills:obsidian-ref @obsidian:Personal/ai/my.md

# 로컬 파일에 가져와서 적용
CLAUDE.md를 @obsidian:Personal/ai/my.md 가져와서 설정해

# 여러 참조
/obsidian-skills:obsidian-ref @obsidian:/templates/a.md @obsidian:/templates/b.md
```

**참조 문법:**

| 형식 | 예시 | 의미 |
|------|------|------|
| `@obsidian:VAULT/PATH` | `@obsidian:Personal/ai/my.md` | vault와 경로를 명시적으로 지정 |
| `@obsidian:/PATH` | `@obsidian:/ai/my.md` | 기본 vault 사용 (세션 컨텍스트에서 가져오거나 요청) |

**동작:**
- **표시만** - 대화에서 vault 내용을 보여줍니다
- **파일에 쓰기** - vault 내용을 로컬 대상 파일에 적용합니다
- **추가** - 기존 파일에 vault 내용을 덧붙입니다

**예시:**

```
# 단순 읽기
@obsidian:Personal/ai/my.md
대화에 파일 내용을 표시합니다

# 가져와서 설정
rules.md에 @obsidian:Work/rules/coding.md 가져와서 설정해
Work vault에서 coding.md를 읽어 ./rules.md에 씁니다

# 여러 파일
@obsidian:/templates/config.md @obsidian:/templates/setup.md
기본 vault에서 두 파일을 모두 읽어 표시합니다
```

### 8. obsidian-cli

Obsidian CLI 전체 명령어를 활용할 수 있는 범용 스킬입니다. 사용자의 자연어 요청을 적절한 CLI 명령어로 변환하여 실행합니다.

**트리거:** "obsidian cli", "obsidian 에서", "vault 에서", "노트 만들어", "노트 읽어", "파일 이동", "태그 목록", "obsidian 명령"

**사용법:**

```
/obsidian-skills:obsidian-cli 노트 만들어줘 "회의록/2026-02-19.md"
/obsidian-skills:obsidian-cli 태그 목록 보여줘
/obsidian-skills:obsidian-cli 데일리 노트에 추가해줘 "오늘 할 일: 코드 리뷰"
```

**지원 카테고리:**

| 카테고리 | CLI 명령어 | 설명 |
|---------|-----------|------|
| **Vault 관리** | `vaults`, `vault`, `reload` | 볼트 목록, 정보, 리로드 |
| **파일 CRUD** | `create`, `read`, `append`, `prepend`, `delete`, `move`, `rename` | 파일 생성/읽기/수정/삭제/이동/이름변경 |
| **폴더** | `folders`, `folder`, `files` | 폴더 목록, 정보, 파일 목록 |
| **검색** | `search`, `search:context`, `search:open` | 텍스트 검색 |
| **속성** | `properties`, `property:read`, `property:set`, `property:remove` | frontmatter 관리 |
| **태그** | `tags`, `tag` | 태그 목록, 태그 정보 |
| **링크** | `links`, `backlinks`, `orphans`, `deadends`, `unresolved` | 링크 분석 |
| **작업(Tasks)** | `tasks`, `task` | 작업 목록, 상태 변경 |
| **북마크** | `bookmark`, `bookmarks` | 북마크 관리 |
| **데일리 노트** | `daily`, `daily:read`, `daily:append`, `daily:prepend`, `daily:path` | 데일리 노트 |
| **템플릿** | `templates`, `template:read`, `template:insert` | 템플릿 |
| **플러그인** | `plugins`, `plugin:enable`, `plugin:disable`, `plugin:install`, `plugin:uninstall` | 플러그인 관리 |
| **테마** | `themes`, `theme`, `theme:set`, `theme:install`, `theme:uninstall` | 테마 관리 |
| **히스토리** | `history`, `history:read`, `history:restore` | 파일 버전 이력 |
| **워크스페이스** | `workspaces`, `workspace:load`, `workspace:save`, `workspace:delete` | 워크스페이스 |
| **기타** | `aliases`, `outline`, `wordcount`, `open`, `random` | 유틸리티 |

> **참고**: 파괴적 작업(delete, overwrite, permanent, uninstall, restore)은 실행 전 반드시 사용자 확인을 거칩니다.

### 9. obsidian-delete

Obsidian vault에서 프로젝트 전체를 안전하게 삭제합니다. AI-Projects 폴더 내의 프로젝트 파일과 빈 폴더를 모두 제거합니다.

**트리거:** "obsidian delete", "delete project", "remove project from obsidian", "프로젝트 삭제"

**사용법:**

```
/obsidian-skills:obsidian-delete my-project
```

**수행 작업:**
- 프로젝트 내 모든 파일 목록을 보여주고 삭제 확인을 요청합니다
- 기본적으로 Obsidian 휴지통으로 이동합니다 (permanent 플래그로 영구 삭제 가능)
- 파일 삭제 후 빈 폴더를 정리합니다
- 삭제 완료 후 결과를 확인합니다

## 일반적인 워크플로우

obsidian-skills의 이점을 최대한 활용하려면 다음 워크플로우를 따르세요:

### 1일차: 프로젝트 초기화

```
/obsidian-skills:obsidian-init my-app
```

필요한 모든 문서와 폴더로 프로젝트를 설정합니다.

### 작업 시작: 세션 시작

```
/obsidian-skills:obsidian-session start my-app
```

이번 작업 세션의 목표를 지정합니다. 진행 상황을 추적하는 세션 로그가 생성됩니다.

### 작업 중: 문서 업데이트

작업하면서 프로젝트 문서를 업데이트합니다:

```
/obsidian-skills:obsidian-update todo add "Set up authentication"
/obsidian-skills:obsidian-update decisions
```

### 작업 종료: 세션 종료

```
/obsidian-skills:obsidian-session end
```

완료한 작업을 요약하고, 완료된 작업을 표시하고, 이월할 항목을 기록합니다.

### 다음날: 작업 재개

```
/obsidian-skills:obsidian-resume my-app
```

vault에서 전체 컨텍스트를 불러옵니다. 다음 내용을 즉시 확인할 수 있습니다:
- 마지막 세션에서 작업한 내용
- 완료한 작업
- 남아 있는 작업
- 작업을 이어가기 위한 다음 단계

### 언제든지 상태 확인

```
/obsidian-skills:obsidian-status
```

모든 프로젝트 개요, 최근 활동, TODO 진행 상황을 확인합니다.

### 필요할 때 검색

```
/obsidian-skills:obsidian-search "error handling" project=my-app
```

관련 결정 사항, 세션 노트, 계획을 찾습니다.

### Vault 관리 작업

범용 CLI 스킬로 다양한 vault 작업을 수행합니다:

```
/obsidian-skills:obsidian-cli 태그 목록 보여줘
/obsidian-skills:obsidian-cli 데일리 노트에 "회의 메모: API 설계 확정" 추가해줘
/obsidian-skills:obsidian-cli "회의록/2026-02-19.md" 노트 만들어줘
/obsidian-skills:obsidian-cli 고아 노트 찾아줘
```

파일 생성, 태그 조회, 링크 분석, 플러그인 관리 등 Obsidian CLI가 지원하는 모든 작업을 자연어로 요청할 수 있습니다.

### Vault 내용 참조

Claude 대화 어디서든 vault 문서를 주입합니다:

```
@obsidian:Personal/ai/my.md
```

파일을 프롬프트 컨텍스트에 직접 로드합니다.

## 프로젝트 구조 레퍼런스

obsidian-init으로 생성된 프로젝트는 다음 구조를 따릅니다:

```
AI-Projects/
└── PROJECT_NAME/
    ├── _overview.md
    │   └── 프로젝트 메타데이터, 상태, 빠른 링크
    │       마지막 업데이트 타임스탬프 및 활동 로그
    │
    ├── _architecture.md
    │   └── 시스템 설계, 컴포넌트, 기술 스택
    │       설계 결정 사항 및 근거
    │
    ├── _decisions.md
    │   └── 컨텍스트, 대안, 결과가 포함된 결정 로그
    │       추적 가능성을 위한 타임스탬프 항목
    │
    ├── _todo.md
    │   └── 섹션이 있는 작업 추적:
    │       진행 중, 보류 중, 완료됨
    │
    ├── sessions/
    │   ├── _index.md
    │   ├── 2026-02-19-001.md
    │   ├── 2026-02-19-002.md
    │   └── 2026-02-18-001.md
    │       └── 각 세션에는 다음이 포함됩니다:
    │           목표, 작업 로그, 결정된 사항,
    │           발생한 이슈, 요약
    │
    └── plans/
        ├── _index.md
        ├── feature-auth.md
        └── performance-optimization.md
            └── 다음이 포함된 구현 계획:
                목표, 접근법, 단계, 위험, 일정
```

## 문서 레퍼런스

### _overview.md

프로젝트 메타데이터 및 빠른 참조. 최근 활동 로그와 함께 업데이트됩니다.

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

시스템 설계 및 기술적 결정 사항. 상세한 컨텍스트를 위해 결정 로그에서 링크합니다.

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

컨텍스트와 근거가 포함된 타임스탬프 결정 로그.

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

완료 상태가 포함된 작업 추적.

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

## 문제 해결

### Obsidian CLI를 찾을 수 없는 경우

"obsidian command not found" 오류가 표시되면 경로를 확인하세요:

```bash
ls -la /Applications/Obsidian.app/Contents/MacOS/obsidian
```

경로가 다른 경우 plugin 설정을 업데이트하거나 별칭을 생성하세요:

```bash
alias obsidian="/Applications/Obsidian.app/Contents/MacOS/obsidian"
```

### Vault가 목록에 없는 경우

Obsidian이 실행 중인지 확인하세요:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian vaults
```

vault가 표시되지 않으면 먼저 Obsidian 데스크탑 앱을 열어주세요.

### 세션 컨텍스트를 찾을 수 없는 경우

작업을 재개할 때 plugin은 가장 최근 세션을 찾습니다. 세션이 없으면 새 프로젝트를 시작하거나 기존 프로젝트를 선택하라는 메시지가 표시됩니다.

### 파일을 찾을 수 없는 오류

프로젝트가 초기화되었는지 확인하세요:

```bash
/Applications/Obsidian.app/Contents/MacOS/obsidian files vault=VAULT_NAME folder="AI-Projects/PROJECT_NAME"
```

파일이 없으면 obsidian-init을 다시 실행하세요.

### 참조 문법 문제

`@obsidian:` 참조가 올바른 형식을 사용하는지 확인하세요:
- vault 포함: `@obsidian:VaultName/path/to/file.md`
- 기본 vault: `@obsidian:/path/to/file.md`

Obsidian에서 경로는 대소문자를 구분합니다.

### CLI 명령어 참고

Obsidian CLI에는 `update` 명령이 없습니다. 파일 내용을 수정하려면 다음 명령을 사용하세요:

| 작업 | CLI 명령어 |
|------|-----------|
| 파일 끝에 내용 추가 | `obsidian append vault=NAME path="PATH" content="TEXT"` |
| 파일 앞에 내용 추가 | `obsidian prepend vault=NAME path="PATH" content="TEXT"` |
| 프론트매터 속성 수정 | `obsidian property:set vault=NAME path="PATH" name="KEY" value="VALUE" type=text` |
| 전체 내용 교체 | `obsidian create vault=NAME path="PATH" overwrite content="NEW_CONTENT"` |
| 텍스트 검색 | `obsidian search vault=NAME query="TEXT" path="FOLDER"` (검색 시 `path=` 사용, `folder=` 아님) |

> **주의**: `search` 명령에서 폴더 범위를 지정할 때는 `path=` 파라미터를 사용합니다. `folder=`는 `files`, `folders` 명령에서만 사용됩니다.

## 설정

이 plugin은 Obsidian의 기본 CLI와 함께 동작합니다. 주요 설정은 다음 위치에 저장됩니다:

```
.claude-plugin/
├── plugin.json          # Plugin 메타데이터 및 매니페스트
└── marketplace.json     # Marketplace 목록 설정
```

Obsidian이 설치되어 있으면 별도의 추가 설정이 필요하지 않습니다.

## 고급 사용법

### 여러 Skill 조합

프로젝트 컨텍스트를 복원한 후 관련 결정 사항을 검색합니다:

```
1. /obsidian-skills:obsidian-resume my-app
2. /obsidian-skills:obsidian-search "error handling" project=my-app
```

### 개발 시 참조 활용

프로젝트 아키텍처를 프롬프트 컨텍스트에 로드합니다:

```
@obsidian:Personal/ai/my-app/_architecture.md
Implement the API layer according to this architecture
```

### 세션 업데이트 자동화

작업 중 수동으로 세션 로그를 업데이트합니다:

```
/obsidian-skills:obsidian-update todo add "Implemented user authentication"
```

TODO 목록과 활성 세션 로그 모두에 내용이 추가됩니다.

### 크로스 Vault 프로젝트

vault 매개변수를 사용해 다른 vault의 프로젝트에 접근합니다:

```
/obsidian-skills:obsidian-status project=api-v2 vault=Work
```

## 기여

기여를 환영합니다. 기여하려면:

1. 저장소를 fork합니다
2. 기능 브랜치를 생성합니다
3. 변경 사항을 적용합니다
4. `claude --plugin-dir ./`로 테스트합니다
5. pull request를 제출합니다

## 라이선스

MIT 라이선스. 자세한 내용은 LICENSE 파일을 참고하세요.

## 지원

이슈, 질문, 기능 요청은 GitHub 저장소에서 이슈를 열어주세요.

## 변경 이력

### v1.1.0

범용 vault 관리 스킬 추가:
- obsidian-cli - Obsidian CLI 전체 명령어를 활용한 범용 vault 관리
- obsidian-delete - 프로젝트 안전 삭제

### v1.0.0

7가지 핵심 skill이 포함된 초기 릴리스:
- obsidian-init - 프로젝트 초기화
- obsidian-session - 세션 관리
- obsidian-update - 문서 업데이트
- obsidian-resume - 컨텍스트 복원
- obsidian-status - 프로젝트 개요
- obsidian-search - 문서 검색
- obsidian-ref - Vault 참조

---

**obsidian-skills** - 원활한 프로젝트 문서화와 세션 추적을 위해 Obsidian을 Claude Code 워크플로우에 통합합니다.
