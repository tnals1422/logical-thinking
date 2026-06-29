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
| 제출 목적 | `submission-target: true` | 추론 (기본 `true`) — `false` 시 Working 포함 W3본만 |

**유형 (deliverable-type)**: `policy-planning` · `coordination` · `situation` · `information` · `meeting-material` · `general` 등

상세: `reference/deliverable-brief-schema.md`

## 모드
- **참여형 (기본)**: Brief 3항목 확인 → Prior 확인 → 템플릿 선택 확인 → **W1/W2 단계별 확인(선택)**
- **자율형**: Brief 자동 추론 → prior 자동 소비 → W1→W2→W3→(submissionTarget 시 W4) 연속 실행 → 제출본만 반환

Orchestrator: **Brief → (research-first 시 Source Validation) → Deliverable Spec → Prior** 순 Context 구성.

`research-first: true` — write 전 출처·균형 검증 ([research.md](research.md))

## Writing 4단계 (Phase 3 + W4)

writing agent는 본문을 **W1 → W2 → W3 → W4** 순으로 작성합니다 (`submissionTarget: false` 시 W3까지).

| 단계 | 명칭 | 내용 |
|------|------|------|
| **W1** | 뼈대 (Skeleton) | 유형 템플릿 골격 + prior Level 1 섹션 매핑 |
| **W2** | 초안 (Draft) | 문장·표·도식 구체화 + 논리 패턴 적용 |
| **W3** | 수요자 관점 수정 | `audience` 기준 분량·용어·건의 조율 |
| **W4** | 제출면 정리 | ST1~ST6 · Working 섹션 제거 · Markdown 제출본 |

**W3 체크리스트**: 수요자 요구 충족 · 객관성 · 중복·피동형 제거 · (정책·조정) 조치 명확  
**W4 체크리스트**: `reference/submission-ready-checklist.md` (ST1~ST6)

| submissionTarget | 최종 산출물 | frontmatter |
|------------------|------------|-------------|
| `true` (기본) | 표지·개요·본문·건의만 (Working **없음**) | `submission-ready`, `submissionReady: true` |
| `false` | W3 + Audience Pass · MECE · Pipeline Meta | `audience-revised` |

| 모드 | 중간 확인 |
|------|----------|
| 참여형 | W1·W2·W3 후 확인 가능; W4 제출본 미리보기 |
| 자율형 | 내부만 실행; `submissionTarget: true` 시 제출본만 저장 |

상세: `.claude/agents/writing.md` § Writing Pipeline · `reference/submission-ready-checklist.md`

## 언제 쓰나
- 업무 보고서, 기획서, 제안서를 빠르게 작성할 때
- `/think`로 정리한 생각을 문서로 만들 때
- 다양한 템플릿 (기본 SCQA, IAEJ, 목적-방침, incident-causal (ser/stad), FABE, PREP, Case-Measure 등)이 필요할 때

## 예시
```
/struct-write 자세히 같이 분기 실적 보고서 작성
/struct-write 빠르게 신규 기능 도입 제안서
/struct-write {주제} template:objective-policy-pattern
/struct-write 경쟁사 대비 우위 설명 포함 제안서   # Orchestrator가 내용 신호 분석 → fabe-pattern 제안 가능
```

## 템플릿 선택

**Deliverable 유형** (Brief.deliverable-type) — Orchestrator Deliverable Spec:

| deliverable-type | 템플릿 | 상태 |
|------------------|--------|------|
| `policy-planning` | `deliverables/deliverable-policy-planning.md` | available |
| `coordination` | `deliverables/deliverable-coordination.md` | available |
| `meeting-material` | `deliverables/deliverable-meeting-material.md` | available (`meeting-purpose` 3변형) |
| `meeting-result` | `deliverables/deliverable-meeting-result.md` | available |
| `policy-reference` | `deliverables/deliverable-policy-reference.md` | available (건의 없음) |
| `event` | `deliverables/deliverable-event.md` | available (`event-phase`: planning / progress) |
| `situation` / `information` | `deliverables/deliverable-situation-info.md` | available |
| `general` 등 | `patterns/general/scqa-pattern.md` (fallback) | — |

**디렉터리**: `deliverables/` · `patterns/` · `express/` · `shared/`

**논리 패턴** (`patterns/`) — Primary Logic Core. deliverable의 `(logic: …)` 앵커에 단계 구조 제공.

| 템플릿 파일 | 주요 용도 |
|-------------|----------|
| `patterns/general/scqa-pattern.md` | SCQA · general 폴백 · policy §3 · meeting-result 안건 |
| `patterns/reports/iaej-pattern.md` | IAEJ · policy §2.2 · reference §2.2 lite |
| `patterns/reports/incident-causal-pattern.md` | mode=**ser** 조정·대응 / mode=**stad** 장애·RCA |
| `patterns/proposals/fabe-pattern.md` | 경쟁·대안 우위 비교 (FABE) |
| `patterns/proposals/prep-pattern.md` | 단일 주장·제언 설득 (PREP) |
| `patterns/education/case-measure-pattern.md` | 조건-조치/판단 규칙·지침 (Case-Measure) |
| `patterns/reports/objective-policy-pattern.md` | 목적-방침 · policy §4 · meeting §3.C · event A.3 |

임베드 규칙: `patterns/EMBEDDING-GUIDE.md` + `struct-docs/templates/README.md` (Role Contract)

자세한 선택 기준 및 `useWhen` 은 `struct-docs/templates/README.md` 참조.
Orchestrator는 Brief.deliverableType + pyramid/input 내용 신호(비교→FABE, 단일 제언→PREP, 조건/규칙→case-measure 등)로 logicPattern을 자연스럽게 auto 결정하거나 제안 (상세 휴리스틱: `docs/struct-usage-scenarios.md` S02 "Logic Pattern 선택 가이드").
`struct-docs/templates/{deliverables|patterns|express|shared}/` 에 새 파일을 추가하면 자동으로 인식됩니다.

## 출력
- 선택된 템플릿 구조 따름 (기본 SCQA)
- **제출본** (`submission-target: true`): Markdown 그대로 제출 — Working 섹션 없음
- Frontmatter: `draftStage`, `submissionReady`, `submissionTarget`, `writingPipeline`, `deliverableType`, `audience`
- 저장: `struct-docs/02-writing/YYYYMMDD-{title}.md`

## 관련
- `/struct-think` 먼저 구조화 → Orchestrator가 **Prior Thinking Pyramid** 자동 주입 → writing agent가 Pyramid Consumption 규칙 적용.
- 템플릿 선택 및 목록: `struct-docs/templates/README.md` (categorized: general/, reports/, proposals/, education/)
- 전체 흐름: [workflow.mmd](workflow.mmd)
- 상세 시나리오: [scenarios.md](scenarios.md) (S02, S05, S06)
