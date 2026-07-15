---
description: 구조화된 내용을 슬라이드·메모·스토리로 변환합니다
user-invocable: true
---

# /struct-express

구조화된 논리를 발표 슬라이드, 메모, 스토리텔링으로 변환하는 스킬.

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다**.  
Parent가 Brief/Spec/Package 컨텍스트를 준비한 뒤 **`expression` 만** spawn.  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`

## Usage

```
/struct-express {텍스트}                 → 슬라이드 (기본값)
/struct-express slide {텍스트}           → Marp 호환 슬라이드
/struct-express memo {텍스트}            → 1페이지 메모
/struct-express story {텍스트}           → 스토리텔링
/struct-express brief {Full Report 경로} → Executive Brief 1:5 (Phase 5)
/struct-express package {경로}           → Brief + Attachments (+ meeting slide)
```

**write 후 자동 트리거**: `summary-detail: split-1-5` + Review pass → Parent가 express package 자동(Autonomous) 또는 확인(Collaborative).  
상세: `reference/orchestrator-express-package.md`

## Instructions

1. Mode 감지; `.struct-memory.json` 로드
2. format / packageMode 파싱:
   - `brief` / `package` / `slide-deck` → packageMode (Phase 5)
   - `slide` / `memo` / `story` → Format
   - 없으면 기본 `"slide"`
3. Parent 인라인:
   - express용 Brief · Deliverable Spec (필요 시)
   - package/brief 시 `## Full Report Source` (경로 Read)
   - `## Express Package` 옵션 (`reference/orchestrator-express-package.md`)
   - Prior 해당 시 주입
4. **spawn `expression` only** — 프롬프트:

```
## Task
express 수행

## User Input
{format 키워드 제외 텍스트}

## Format
{slide | memo | story | brief}

## Mode
{collaborative | autonomous}

## Options
package: true | false
package-mode: brief | package | slide-deck | attachments
full-report: {write 산출물 경로}
use-prior: true | false
audience: ...
deliverable-type: ...
summary-detail: summary-only | split-1-5 | detail-only
reuse-brief: true

## Brief (Report Operations)
{해당 시}

## Deliverable Spec
{해당 시}

## Full Report Source
{package/brief 시}

## Express Package
{Phase 5 블록}

## Context (from .struct-memory.json)
{...}

## Output Requirements
- Markdown / Marp 등 Format 준수
- Frontmatter, 개조식·표 기본
```

5. 저장: `struct-docs/04-expressing/YYYYMMDD-{kebab-title}.md`
6. Memory: `previousDocuments` 갱신
7. 사용자 출력

## Emergency

- `expression` 스폰 실패 시 오류 보고. orchestrator 자식 스폰 금지.

## 참고

- 템플릿: `struct-docs/templates/express/`
- `.grok/agents/expression.md`
