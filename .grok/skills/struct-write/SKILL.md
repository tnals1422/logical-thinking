---
description: 입력을 구조화된 논리 문서(적합한 템플릿 선택)로 만듭니다
user-invocable: true
---

# /struct-write

사용자의 입력을 명확한 논리 구조로 정리한 문서로 생성하는 스킬. (정보 정리 후 내용 특성에 맞는 템플릿 자동/협의 선택)

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다** (primary 금지).  
Parent가 `.grok/agents/orchestrator.md` 의 write Process + `reference/*` 를 인라인 수행한 뒤 **`writing` 만** spawn 한다.  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`

## Instructions

Parent가 아래 순서를 따른다. 상세는 해당 문서 **Read** 후 적용.

### A. 공통

1. Mode: collaborative(기본) | autonomous
2. `.struct-memory.json` 로드 (없으면 기본 구조 생성)
3. Options 파싱 (아래 템플릿)

```
## Options (from user / skill)
template: {논리 패턴 템플릿, 없으면 "default"}
use-prior: true | false
mode: collaborative | autonomous
purpose: {보고 목적}
audience: decision-maker | expert | meeting-participant | reference-only
deliverable-type: policy-planning | coordination | policy-reference | situation | information | meeting-material | meeting-result | event | general
meeting-purpose: info-share | opinion-gathering | decision
event-phase: planning | progress
timing: urgent | normal
requested-action: {수요자 조치 | none}
summary-detail: summary-only | split-1-5 | detail-only
submission-target: true | false
logic-pattern: auto | scqa-pattern | iaej-pattern | incident-causal-pattern | objective-policy-pattern
reuse-brief: true
research-first: true | false
review: true | false
```

### B. 품질 게이트 (Parent 인라인)

| Step | 동작 | 상세 문서 |
|------|------|-----------|
| RV0 | `research-first: true` 등이면 `subagent_type: "research"` 선행 → `## Source Validation` | `reference/source-validation-checklist.md` |
| B0–B2 | **Brief** 수집/추론. Collaborative: purpose/audience/deliverable-type 미확정 시 **writing 스폰 보류** 후 3항목 질문 | `reference/deliverable-brief-schema.md` |
| DS | **Deliverable Spec** (template Read, logic pattern) | schema §2 |
| Prior | `use-prior` / 유사도 → Read → `## Previous Thinking Pyramid` | `orchestrator.md` Thinking Reuse |
| W | **spawn `writing`** only — 아래 프롬프트 골격 | `.grok/agents/writing.md` |
| Save | `struct-docs/02-writing/YYYYMMDD-{kebab-title}.md` | |
| Mem | `previousDocuments` + `briefs` 갱신 | orchestrator.md §5 |
| R | Review Gate 조건 시 spawn `review` → force_rework 시 writing 재호출 | `reference/orchestrator-review-gate.md` |
| EP | Express Package 조건 시 spawn `expression` | `reference/orchestrator-express-package.md` |

**Collaborative Brief 질문 (필요 시):**

```
보고서 운영 Brief를 확인합니다 (미입력 항목):
1. 목적: 이 문서/분석의 목적은?
2. 수요자: 누가 읽나요? (결정권자 / 전문가 / 회의참석자 / 참고용)
3. 유형: 어떤 산출물인가요? (정책기획 / 조정 / 상황 / 정보 / 회의자료 / 일반 등)
```

**Autonomous**: User Input + memory 추론; 실패 시 schema §1.6 폴백 (`audience: expert`, `deliverable-type: general`).

### C. Specialist 프롬프트 골격 (`writing`)

```
## Task
write 수행

## User Input
{사용자 원본 텍스트}

## Mode
{collaborative | autonomous}

## Brief (Report Operations)
{Step B2 블록}

## Deliverable Spec
{Step DS 블록}

## Previous Thinking Pyramid (Prior Analysis)
{해당 시}

## Source Validation
{research 완료 시}

## Review Feedback
{재생성 시}

## Context (from .struct-memory.json)
{요약}

## Output Requirements
- Markdown, Frontmatter (deliverableType, audience, submissionTarget, draftStage, …)
- submissionTarget true: W1→W2→W3→W4 · submission-ready · Working 섹션 없음
- submissionTarget false: W1→W2→W3 · audience-revised
- 개조식 + 표/다이어그램 기본
```

### D. 출력

결과를 사용자에게 출력한다. 저장·memory는 **Parent가 최종 확정**한다 (경로 불일치 방지).

## Emergency

- `writing` (또는 research/review) 스폰 실패 시: 오류 보고. Brief를 건너뛴 채 본문만 쓰지 않는다.
- orchestrator 자식 스폰으로 재시도하지 않는다.

## 참고

- Writing Pipeline: `reference/writing-pipeline.md`, `reference/submission-ready-checklist.md`
- 전체 Process 권위: `.grok/agents/orchestrator.md`
