---
tags: [struct, usage, write]
created: 2026-06-17
type: cheatsheet
---
# /struct-write — 문서/보고서 생성

## 문법
```
/struct-write {문서 주제 또는 지시}
/struct-write {주제} template:{논리패턴명}
/struct-write 결정권자용 정책기획 보고서 {주제}
```

## Brief (Phase 1)

Orchestrator가 write 전 **보고서 운영 Brief**를 수집합니다.

| 필드 | 옵션 예시 | Collaborative 미지정 시 |
|------|----------|------------------------|
| 목적 | `purpose: ...` | 질문 |
| 수요자 | `audience: decision-maker` | 질문 |
| 유형 | `deliverable-type: policy-planning` | 질문 |
| 분량 | `summary-detail: split-1-5` | 추론 → Review pass 후 **Executive Brief** 자동/확인 (Phase 5) |

**유형 (deliverable-type)**: `policy-planning` · `coordination` · `situation` · `information` · `meeting-material` · `general` 등

상세: `reference/deliverable-brief-schema.md`

## 모드
- **참여형 (기본)**: Brief 3항목 확인 → Prior 확인 → 템플릿 선택 확인 → **W1/W2 단계별 확인(선택)**
- **자율형**: Brief 자동 추론 → prior 자동 소비 → W1→W2→W3 연속 실행 → 변경 요약만

Orchestrator: **Brief → (research-first 시 Source Validation) → Deliverable Spec → Prior** 순 Context 구성.

`research-first: true` — write 전 출처·균형 검증 ([research.md](research.md))

## Writing 3단계 (Phase 3)

writing agent는 본문을 **W1 → W2 → W3** 순으로 작성합니다 (대통령 보고서 가이드 3단계 대응).

| 단계 | 명칭 | 내용 |
|------|------|------|
| **W1** | 뼈대 (Skeleton) | 유형 템플릿 골격 + prior Level 1 섹션 매핑 |
| **W2** | 초안 (Draft) | 문장·표·도식 구체화 + 논리 패턴 적용 |
| **W3** | 수요자 관점 수정 | `audience` 기준 분량·용어·건의 조율 |

**W3 체크리스트**: 수요자 요구 충족 · 객관성 · 중복·피동형 제거 · (정책·조정) 조치 명확

| 모드 | 중간 확인 | 최종 산출물 |
|------|----------|------------|
| 참여형 | W1·W2 후 확인 가능 | `## Audience Pass` + MECE 포함 |
| 자율형 | 내부만 실행 | frontmatter `draftStage: audience-revised` |

상세: `.claude/agents/writing.md` § Writing Pipeline (Phase 3)

## 언제 쓰나
- 업무 보고서, 기획서, 제안서를 빠르게 작성할 때
- `/think`로 정리한 생각을 문서로 만들 때
- 다양한 템플릿 (기본 SCQA, I-A-E-J, 목적-방침, 구조-사건-대처, 상태-트리거-사고-손해 등)이 필요할 때

## 예시
```
/struct-write 자세히 같이 분기 실적 보고서 작성
/struct-write 빠르게 신규 기능 도입 제안서
/struct-write {주제} template:objective-policy-pattern
```

## 템플릿 선택

**Deliverable 유형** (Brief.deliverable-type) — Orchestrator Deliverable Spec:

| deliverable-type | 템플릿 | 상태 |
|------------------|--------|------|
| `policy-planning` | `deliverable-policy-planning.md` | available |
| `coordination` | `deliverable-coordination.md` | available |
| `meeting-material` | `deliverable-meeting-material.md` | available (`meeting-purpose` 3변형) |
| `meeting-result` | `deliverable-meeting-result.md` | available |
| `policy-reference` | `deliverable-policy-reference.md` | available (건의 없음) |
| `event` | `deliverable-event.md` | available (`event-phase`: planning / progress) |
| `situation` / `information` | `deliverable-situation-info.md` | available |
| `general` 등 | `report-default.md` (fallback) | — |

**논리 패턴** (섹션 내부) — pyramid 분석 또는 Spec.logicPattern:

| 템플릿 파일                            | 주요 용도                     |
|----------------------------------------|-------------------------------|
| report-default.md                      | 기본 SCQA 보고서             |
| iaej-pattern.md                        | 기반구조-행동-사건-판단      |
| structure-event-response-pattern.md    | 구조-사건-대처               |
| state-trigger-accident-damage-pattern.md | 상태-트리거-사고-손해       |
| objective-policy-pattern.md            | 목적-방침                    |

자세한 선택 기준 및 `useWhen` 은 `struct-docs/templates/README.md` 참조.
`struct-docs/templates/` 에 새 파일을 추가하면 자동으로 인식됩니다.

## 출력
- 선택된 템플릿 구조 따름 (기본 SCQA)
- W3 완료본: Executive Summary + 근거 중심 (개조식 + 표 우선)
- 하단: `## Audience Pass` · `## MECE 검증 결과` · `## Writing Pipeline Meta`
- Frontmatter: `draftStage`, `writingPipeline`, `deliverableType`, `audience`
- 저장: `struct-docs/02-writing/YYYYMMDD-{title}.md`

## 관련
- `/struct-think` 먼저 구조화 → Orchestrator가 **Prior Thinking Pyramid** 자동 주입 → writing agent가 Pyramid Consumption 규칙 적용.
- 템플릿 선택 및 목록: `struct-docs/templates/README.md` (영문 파일명 사용: report-default.md, iaej-pattern.md, objective-policy-pattern.md 등)
- 전체 흐름: [workflow.mmd](workflow.mmd)
- 상세 시나리오: [scenarios.md](scenarios.md) (S02, S05, S06)
