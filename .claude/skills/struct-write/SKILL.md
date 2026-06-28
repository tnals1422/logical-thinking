---
description: 입력을 구조화된 논리 문서(적합한 템플릿 선택)로 만듭니다
user-invocable: true
---

# /struct-write

사용자의 입력을 명확한 논리 구조로 정리한 문서로 생성하는 스킬. (정보 정리 후 내용 특성에 맞는 템플릿 자동/협의 선택)

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
write 수행

## User Input
{사용자가 입력한 텍스트 전체}

## Options
template: {논리 패턴 템플릿, 없으면 "default"}
use-prior: true | false
mode: collaborative | autonomous
# Brief (Phase 1 — 미지정 시 Orchestrator가 수집/추론)
purpose: {보고 목적}
audience: decision-maker | expert | meeting-participant | reference-only
deliverable-type: policy-planning | coordination | policy-reference | situation | information | meeting-material | meeting-result | event | general
meeting-purpose: info-share | opinion-gathering | decision   # meeting-material 시
event-phase: planning | progress   # event 시
timing: urgent | normal
requested-action: {수요자 조치 | none}
summary-detail: summary-only | split-1-5 | detail-only
logic-pattern: auto | report-default | iaej-pattern | ...
reuse-brief: true
research-first: true | false

## Context (from .struct-memory.json)
{이전 생각 정리 요약이 있으면 포함, 없으면 "이전 컨텍스트 없음"}

**참고**:
- Orchestrator는 write 시 **Brief → Deliverable Spec → Prior Thinking** 순으로 Context를 구성한다 (`reference/deliverable-brief-schema.md`).
- Collaborative(기본): purpose/audience/deliverable-type 미지정 시 Orchestrator가 사용자에게 Brief 3항목 질문.
- Autonomous: Brief 자동 추론 + 폴백 (`audience: expert`, `deliverable-type: general`).
- `research-first: true` 시 Orchestrator가 research 선행 → `## Source Validation` 주입 (Phase 6).
- `use-prior: true` 시 prior thinking 강제 주입. writing agent는 Brief·Deliverable Spec 후 **W1→W2→W3** Writing Pipeline + Pyramid Consumption 적용 (Phase 3).
```

3. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "writing"`**으로 직접 호출한다:

1. 반환된 문서를 사용자에게 출력한다
2. `.struct-memory.json`의 `previousDocuments` 배열에 추가한다
3. `struct-docs/02-writing/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
