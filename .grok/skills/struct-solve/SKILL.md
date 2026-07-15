---
description: 문제를 구조화된 논리로 분석하고 해결안을 도출합니다
user-invocable: true
---

# /struct-solve

문제를 명확한 논리 구조(피라미드)로 분해하고 가설 기반 해결안을 도출하는 스킬.

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다**.  
Parent가 orchestrator.md 의 solve Process를 인라인 수행 후 **`problem-solving` 만** spawn.  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`

## Instructions

### A. 공통

1. Mode: collaborative(기본) | autonomous
2. `.struct-memory.json` 로드
3. Options 파싱:

```
## Options
use-prior: true | false
mode: collaborative | autonomous
purpose: {분석 목적}
audience: decision-maker | expert | meeting-participant | reference-only
deliverable-type: policy-planning | coordination | general | ...
requested-action: {수요자 조치 | none}
reuse-brief: true
research-first: true | false
```

### B. Parent 인라인 게이트

| Step | 동작 |
|------|------|
| RV0 | `research-first` → spawn `research` → Source Validation |
| Brief / Spec | write와 동일 계열 (`reference/deliverable-brief-schema.md`). Collaborative 3항 미확정 시 specialist 보류 |
| Prior | use-prior / 유사도 → Previous Thinking Pyramid (framing 기준; 직접 주장 이식 금지) |
| S | **spawn `problem-solving` only** |
| Save | `struct-docs/03-solving/YYYYMMDD-{kebab-title}.md` |
| Mem | `previousAnalysis` 갱신 |
| R | Review Gate 조건 시 spawn `review` (`reference/orchestrator-review-gate.md`) |

### C. Specialist 프롬프트 골격 (`problem-solving`)

```
## Task
solve 수행

## User Input
{문제 설명 전체}

## Mode
{collaborative | autonomous}

## Brief (Report Operations)
{...}

## Deliverable Spec
{...}

## Previous Thinking Pyramid (Prior Analysis)
{해당 시 — framing 품질 기준}

## Source Validation
{해당 시}

## Context (from .struct-memory.json)
{이전 분석 요약 또는 없음}

## Output Requirements
- Markdown, Frontmatter
- 개조식 + 표/다이어그램 기본
```

### D. 출력

분석 결과를 사용자에게 출력. 저장·memory는 Parent 확정.

## Emergency

- specialist 스폰 실패 시 오류 보고. orchestrator 자식 스폰 금지.
- Brief 없이 autonomous 폴백만으로 진행할 때는 schema 폴백을 명시한다.

## 참고

- `.grok/agents/orchestrator.md`, `.grok/agents/problem-solving.md`
