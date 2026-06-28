---
description: 사용자 제공 자료의 출처·균형·다중 출처를 검증하고 Source Validation Report를 생성합니다
user-invocable: true
---

# /struct-research

주장·수치·인용에 대해 **출처 명시·다중 출처·균형** 체크리스트를 적용하는 스킬. write/solve 전처리 또는 독립 검증.

## Usage

```
/struct-research {주제·자료·주장 목록}
/struct-research {경로}              → 기존 문서 출처 검증
/struct-research write전 {주제} research-first
```

## Instructions

1. `.struct-memory.json`이 존재하면 읽는다
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출:

```
## Task
research 수행

## User Input
{사용자 입력 전체}

## Options
target: {검증 대상 파일 경로 — 선택}
research-first: true | false   # write/solve 전 선행 시
mode: collaborative | autonomous
audience: ...
deliverable-type: ...

## Context (from .struct-memory.json)
{관련 prior·문서 요약}
```

3. Orchestrator 반환 결과를 사용자에게 출력한다

## 연계

- **write/solve 전**: `research-first: true` → research → `## Source Validation` 주입 → write/solve
- **review**: `verification: source-quality` (Phase 6)
- 체크리스트: `reference/source-validation-checklist.md`

## Fallback

Orchestrator 실패 시 **`subagent_type: "research"`** 직접 호출 → `struct-docs/06-researching/` 저장 → `previousResearch` memory 기록