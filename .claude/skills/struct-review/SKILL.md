---
description: 생성된 결과물(write/solve/express)의 Pyramid Consumption 충실도를 검증하고, 필요 시 재생성을 유도합니다
user-invocable: true
---

# /struct-review

write / solve / express (및 think) 결과물에 대한 **독립 검수** 스킬. Fidelity · Compliance · **Deliverable Quality** (Phase 4) 게이트.

## 주요 사용 목적
- prior thinking pyramid 소비 충실도 (Fidelity / writing-consumption)
- Brief·유형·수요자 관점 **Deliverable Quality** — 4대 실패 유형 F1~F4, DT1~DT3
- 문제 발견 시 Regeneration Directives (W1/W2/W3 대상 단계 명시)
- Orchestrator 자동 재생성 루프 지원

## 문법
```
/struct-review
/struct-review [파일경로]
/struct-review last
```

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트와 최근 산출물을 확인한다.
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
review 수행

## User Input
{사용자 추가 지시 또는 빈 값}

## Target
{검토할 대상 파일 경로. 지정되지 않으면 최근 write/solve/express 결과 추론}

## Options
source: {명시적 prior thinking 경로 (선택)}
verification: thinking-compliance | writing-consumption | deliverable-quality | both | fidelity | full | source-quality | deliverable+source
auto-regenerate: true | false   (기본: mode에 따라 Orchestrator 판단)

## Context (from .struct-memory.json)
{최근 thinking / write / solve / express 결과 요약}
```

3. Orchestrator가 반환한 Review 결과를 사용자에게 출력한다.

## 동작 원칙
- **write 기본**: `both` (consumption + deliverable-quality); prior 없으면 `deliverable-quality`
- **solve/express**: `deliverable-quality`
- **think**: `thinking-compliance`
- Deliverable DT1(건의 누락 등) → `force_rework` 우선
- `source-quality` / `deliverable+source` — S1~S5, ST1 무출처 (Phase 6)
- 항상 **Regeneration Directives** + Review Data JSON (`deliverableQuality`, `deliverableTiers`)
- 저장: `struct-docs/05-reviewing/`

## Fallback
Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "review"`**으로 직접 호출한다:
1. 반환된 검토 결과를 사용자에게 출력한다
2. `.struct-memory.json`에 review 결과 요약 기록
3. `struct-docs/05-reviewing/YYYYMMDD-{title}-review.md` 저장

## 예시
```
/struct-review
/struct-review 자세히 같이
/struct-review struct-docs/02-writing/20260620-xxx.md
```