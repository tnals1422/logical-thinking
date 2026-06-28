---
description: 문제를 구조화된 논리로 분석하고 해결안을 도출합니다
user-invocable: true
---

# /struct-solve

문제를 명확한 논리 구조(피라미드)로 분해하고 가설 기반 해결안을 도출하는 스킬.

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
solve 수행

## User Input
{사용자가 입력한 문제 설명 전체}

## Options
use-prior: true | false
mode: collaborative | autonomous
purpose: {분석 목적}
audience: decision-maker | expert | meeting-participant | reference-only
deliverable-type: policy-planning | coordination | general | ...
requested-action: {수요자 조치 | none}
reuse-brief: true
research-first: true | false

## Context (from .struct-memory.json)
{이전 분석 결과가 있으면 포함, 없으면 "이전 컨텍스트 없음"}

**참고**:
- Orchestrator는 solve 시 Brief를 수집·주입한다. `research-first: true` 시 research 선행 (Phase 6).
- problem-solving agent는 Brief·Source Validation을 Problem Framing에 반영한다.
- `use-prior: true` 시 prior를 framing 품질 기준으로 활용 (직접 주장 이식 금지).
```

3. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "problem-solving"`**으로 직접 호출한다:

1. 반환된 분석 결과를 사용자에게 출력한다
2. `.struct-memory.json`의 `previousAnalysis` 배열에 추가한다
3. `struct-docs/03-solving/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
