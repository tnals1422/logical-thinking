---
description: 사용자 제공 자료의 출처·균형·다중 출처를 검증하고 Source Validation Report를 생성합니다
user-invocable: true
---

# /struct-research

주장·수치·인용에 대해 **출처 명시·다중 출처·균형** 체크리스트를 적용하는 스킬. write/solve 전처리 또는 독립 검증.

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다**.  
Parent가 Context를 준비한 뒤 **`research` 만** spawn.  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`  
체크리스트: `reference/source-validation-checklist.md`

## Usage

```
/struct-research {주제·자료·주장 목록}
/struct-research {경로}              → 기존 문서 출처 검증
/struct-research write전 {주제} research-first
```

## Instructions

1. Mode; `.struct-memory.json` 로드
2. Options / Target 파싱 (`target` 경로면 해당 파일 Read)
3. **spawn `research` only**:

```
## Task
research 수행

## User Input
{사용자 입력 전체}

## Options
target: {검증 대상 파일 경로 — 선택}
research-first: true | false
mode: collaborative | autonomous
audience: ...
deliverable-type: ...

## Context (from .struct-memory.json)
{관련 prior·문서 요약}

## Output Requirements
- Source Validation Report (S1~S5)
- Claim–Source catalog + Gaps
- Frontmatter / overall pass|fail
```

4. 저장: `struct-docs/06-researching/YYYYMMDD-{topic}-sources.md` (관례 유지)
5. Memory: `previousResearch` 갱신 (overall, savedTo, claimsCatalogued 등)
6. **write/solve 전처리**로 호출된 경우: Parent가 `## Source Validation` 블록을 후속 specialist Context에 주입 (`source-validation-checklist.md` §4)
7. 사용자 출력

## 연계

- **write/solve 전**: `research-first: true` → 본 스킬 흐름 → Source Validation 주입
- **review**: `verification: source-quality` (Phase 6)

## Emergency

- `research` 스폰 실패 시 오류 보고. orchestrator 자식 스폰 금지.
