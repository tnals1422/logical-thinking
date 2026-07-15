---
description: 생성된 결과물(write/solve/express)의 Pyramid Consumption 충실도를 검증하고, 필요 시 재생성을 유도합니다
user-invocable: true
---

# /struct-review

write / solve / express (및 think) 결과물에 대한 **독립 검수** 스킬. Fidelity · Compliance · **Deliverable Quality** (Phase 4) 게이트.

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다**.  
Parent가 Target·Brief·Spec·Prior를 준비한 뒤 **`review` 만** spawn. 재생성 루프는 **Parent만** 제어.  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`  
상세 Gate: `reference/orchestrator-review-gate.md`

## 주요 사용 목적

- prior thinking pyramid 소비 충실도 (Fidelity / writing-consumption)
- Brief·유형·수요자 관점 **Deliverable Quality** — 4대 실패 유형 F1~F4, DT1~DT3
- 문제 발견 시 Regeneration Directives (W1/W2/W3/W4 대상 단계 명시)
- Parent 자동 재생성 루프 지원

## 문법

```
/struct-review
/struct-review [파일경로]
/struct-review last
```

## Instructions

1. Mode; `.struct-memory.json` 로드 (최근 산출물·briefs)
2. **Target** 결정: 명시 경로 > `last` / 최근 write·solve·express `savedTo`
3. Parent R1 준비 (`reference/orchestrator-review-gate.md`):
   - 대상 파일 Read
   - Brief 복원 (`briefs` linkedDocument 또는 frontmatter)
   - Prior / Deliverable Spec / logic template / submission checklist (해당 시)
4. **spawn `review` only**:

```
## Task
review 수행

## User Input
{추가 지시 또는 빈 값}

## Target
{경로 및/또는 본문}

## Verification Type
{ thinking-compliance | writing-consumption | deliverable-quality | both | fidelity | full | source-quality | deliverable+source }

## Options
source: {prior thinking 경로}
auto-regenerate: true | false

## Brief (Report Operations)
{...}

## Deliverable Spec
{...}

## Previous Thinking Pyramid
{필요 시}

## Mode
{collaborative | autonomous}

## Output Requirements
검증 Report + Review Data JSON + Regeneration Directives
```

5. 저장: `struct-docs/05-reviewing/YYYYMMDD-{title}-review.md`
6. Memory: `previousReviews` 갱신
7. **force_rework**: Parent만 해당 specialist(`writing` / `problem-solving` / `expression`) 재호출 — review agent가 재생성 spawn 금지
8. 사용자 출력

## 동작 원칙

- **write 기본**: `both` (consumption + deliverable-quality); prior 없으면 `deliverable-quality`
- **solve/express**: `deliverable-quality`
- **think**: `thinking-compliance`
- DT1 / D7 / DT-Submission / source-quality 규칙은 기존과 동일 (본 스킬 상단·review-gate 문서)
- 항상 **Regeneration Directives** + Review Data JSON

## Emergency

- `review` 스폰 실패 시 오류 보고. orchestrator 자식 스폰 금지.

## 예시

```
/struct-review
/struct-review 자세히 같이
/struct-review struct-docs/02-writing/20260620-xxx.md
```
