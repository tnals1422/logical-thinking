---
description: 구조화된 내용을 슬라이드·메모·스토리로 변환합니다
user-invocable: true
---

# /struct-express

구조화된 논리를 발표 슬라이드, 메모, 스토리텔링으로 변환하는 스킬.

## Usage

```
/struct-express {텍스트}                 → 슬라이드 (기본값)
/struct-express slide {텍스트}           → Marp 호환 슬라이드
/struct-express memo {텍스트}            → 1페이지 메모
/struct-express story {텍스트}           → 스토리텔링
/struct-express brief {Full Report 경로} → Executive Brief 1:5 (Phase 5)
/struct-express package {경로}           → Brief + Attachments (+ meeting slide)
```

**자동 트리거**: write 시 `summary-detail: split-1-5` + Review pass → Orchestrator가 express package 자동 호출 (Autonomous) 또는 확인 (Collaborative).

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. 사용자 입력에서 format/package를 파싱한다:
   - `brief` / `package` / `slide-deck` → packageMode (Phase 5)
   - `slide` / `memo` / `story` → Format
   - 없으면 기본값 "slide" (Brief `split-1-5` 시 Orchestrator가 brief/package 결정)
3. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
express 수행

## User Input
{사용자가 입력한 텍스트 (format 키워드 제외)}

## Format
{slide | memo | story | brief}

## Options
package: true | false          # packageMode: package
package-mode: brief | package | slide-deck | attachments
full-report: {write 산출물 경로}
use-prior: true | false
mode: collaborative | autonomous
audience: decision-maker | expert | meeting-participant | reference-only
deliverable-type: meeting-material | general | ...
summary-detail: summary-only | split-1-5 | detail-only
reuse-brief: true

## Context (from .struct-memory.json)
{이전 결과가 있으면 포함, 없으면 "이전 컨텍스트 없음"}

**참고**:
- Orchestrator는 express 시 Brief·Deliverable Spec·`## Full Report Source`·`## Express Package` 주입 (Phase 5).
- `split-1-5` / `brief` / `package` → `express-executive-brief.md` 등 템플릿 Read.
- write 후 자동 package: Review pass 직후 Orchestrator Step EP.
```

4. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "expression"`**으로 직접 호출한다:

1. 반환된 결과를 사용자에게 출력한다
2. `.struct-memory.json`의 `previousDocuments` 배열에 추가한다
3. `struct-docs/04-expressing/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
