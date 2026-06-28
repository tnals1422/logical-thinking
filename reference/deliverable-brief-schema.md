# Deliverable Brief & Spec Schema

Struct Deliverable System Phase 1 기준. Orchestrator가 write/solve/express 호출 시 수집·주입한다.

> 상세 설계: `docs/struct-deliverable-system.design.md`  
> 유형 우선순위: `docs/struct-deliverable-template-priority.md`

---

## 1. Brief (Report Operations)

### 1.1 필드 정의

| 필드 | 필수 | 값 (enum) | 설명 |
|------|:----:|-----------|------|
| `reportPurpose` | **T1** | 자유 텍스트 (1~2문장) | 왜 지금 이 보고/분석/표현을 하는가 |
| `audience` | **T1** | `decision-maker` \| `expert` \| `meeting-participant` \| `reference-only` | 수요자 유형 |
| `deliverableType` | **T1** | 아래 §1.2 | 산출물 유형 |
| `timing` | T2 | `urgent` \| `normal` | 보고 시급성 (기본: `normal`) |
| `requestedAction` | T2 | 자유 텍스트 \| `none` \| `reference-only` | 수요자가 해야 할 일 |
| `summaryDetailRatio` | T2 | `summary-only` \| `split-1-5` \| `detail-only` | 분량·요약/상세 (기본: `detail-only`) |
| `logicPattern` | T2 | `auto` \| 논리 패턴 파일명 | 섹션 내부 논리 전개 (기본: `auto`) |

**T1** = Collaborative에서 미지정 시 사용자에게 질문. Autonomous에서 추론 실패 시 폴백 적용.

### 1.2 deliverableType enum

| 값 | 한글 | Brief 미지정 시 힌트 키워드 |
|----|------|---------------------------|
| `policy-planning` | 정책기획 | 정책, 제안, 기획, 의사결정, 건의 |
| `coordination` | 조정과제 | 조정, 쟁점, 이해관계, 갈등 |
| `policy-reference` | 정책참고 | 참고, 사례, 시사점 (결정 불요) |
| `situation` | 상황 | 상황, 사건, 긴급, 속보 |
| `information` | 정보 | 동향, 분석, 이슈, 정보 |
| `meeting-material` | 회의자료 | 회의, 안건, 자료 |
| `meeting-result` | 회의결과 | 회의 결과, 논의 요약 |
| `event` | 행사 | 행사, 기획, 진행 |
| `general` | 일반 | 위에 해당 없음 — **폴백** |

**event 전용** — Options `event-phase`:

| event-phase | 한글 |
|-------------|------|
| `planning` | 행사기획 |
| `progress` | 행사진행 |

**meeting-material 전용** — Brief 또는 Options에 `meeting-purpose` 추가:

| meeting-purpose | 한글 |
|-----------------|------|
| `info-share` | 정보공유 |
| `opinion-gathering` | 의견수렴 |
| `decision` | 의사결정 |

### 1.3 audience별 조절 (Specialist에 전달)

| audience | 분량 | 깊이 | requestedAction | 용어 |
|----------|------|------|-----------------|------|
| `decision-maker` | 짧음 | 핵심 | **필수** (policy/coordination) | 평이 |
| `expert` | 중~장 | 기술 상세 | 선택 | 전문용어 OK (정의 병기) |
| `meeting-participant` | 중 | 안건·쟁점 | 회의 목적 따름 | 평이 |
| `reference-only` | 장 | 사례·시사점 | `none` | 균형·중립 |

### 1.4 Context 주입 형식

```markdown
## Brief (Report Operations)
reportPurpose: {text}
audience: {enum}
deliverableType: {enum}
timing: {urgent | normal}
requestedAction: {text | none | reference-only}
summaryDetailRatio: {summary-only | split-1-5 | detail-only}
logicPattern: {auto | report-default | iaej-pattern | ...}
meetingPurpose: {info-share | opinion-gathering | decision}   # meeting-material 시
eventPhase: {planning | progress}   # event 시
briefSource: {user-explicit | inferred | memory-reuse | fallback}
```

### 1.5 Collaborative 수집 (필수 3항목)

다음 중 하나라도 확정 불가 시 **Specialist 호출 전** 사용자에게 질문:

1. `reportPurpose` — "이 보고/문서의 목적은 무엇인가요?"
2. `audience` — "누가 읽나요? (결정권자 / 전문가 / 회의참석자 / 참고용)"
3. `deliverableType` — "어떤 유형인가요? (정책기획 / 상황 / 회의자료 / 일반 등)"

나머지 필드는 입력·맥락에서 추론하고 Brief 블록에 `briefSource: inferred` 표기.

### 1.6 Autonomous 추론 및 폴백

| 필드 | 추론 실패 시 폴백 |
|------|------------------|
| `reportPurpose` | User Input 첫 문장 요약 |
| `audience` | `expert` |
| `deliverableType` | `general` |
| `timing` | `normal` |
| `requestedAction` | `none` (general/reference) / 입력에서 추출 |
| `summaryDetailRatio` | `detail-only` |
| `logicPattern` | `auto` |

---

## 2. Deliverable Spec

Orchestrator가 Brief 확정 후 Specialist 호출 직전에 생성.

### 2.1 Context 주입 형식

```markdown
## Deliverable Spec
deliverableType: {Brief와 동일}
deliverableTemplate: {struct-docs/templates/...}
logicPattern: {확정된 논리 패턴}
logicTemplate: {struct-docs/templates/{pattern}.md}
fallbackTemplate: {deliverable 미구현 시 사용}
summaryDetailRatio: {Brief와 동일}
requestedActionRequired: {true | false}
audienceRules: {audience별 조절 한 줄 요약}
deliverableTemplateStatus: {available | pending-phase2}
writingStages: skeleton → draft → audience-revised   # write 전용 (Phase 3)
```

**writingStages** (write 명령만): Specialist가 W1 뼈대 → W2 초안 → W3 수요자 관점 수정 순으로 실행. 상세: `writing.md` § Writing Pipeline (Phase 3).

### 2.2 deliverableType → deliverableTemplate 매핑

| deliverableType | deliverableTemplate (Phase 2+) |
|-----------------|-------------------------------|
| `policy-planning` | `struct-docs/templates/deliverable-policy-planning.md` |
| `coordination` | `struct-docs/templates/deliverable-coordination.md` |
| `situation` \| `information` | `struct-docs/templates/deliverable-situation-info.md` |
| `meeting-material` | `struct-docs/templates/deliverable-meeting-material.md` |
| `meeting-result` | `struct-docs/templates/deliverable-meeting-result.md` |
| `policy-reference` | `struct-docs/templates/deliverable-policy-reference.md` |
| `event` | `struct-docs/templates/deliverable-event.md` |
| `general` | — (logicTemplate만 사용) |

**Phase 2 available (구현 완료)**:
- `policy-planning` → `deliverable-policy-planning.md`
- `coordination` → `deliverable-coordination.md`
- `meeting-material` → `deliverable-meeting-material.md`
- `meeting-result` → `deliverable-meeting-result.md`
- `policy-reference` → `deliverable-policy-reference.md`
- `event` → `deliverable-event.md`
- `situation` \| `information` → `deliverable-situation-info.md`

**미구현 유형**: 없음 (2026-06 Sprint 2f 완료). `general`만 logicTemplate fallback.

### 2.3 logicPattern 확정 (auto일 때)

| deliverableType | auto 시 1차 권장 logicPattern |
|-----------------|------------------------------|
| `policy-planning` | `iaej-pattern` |
| `coordination` | `structure-event-response-pattern` |
| `situation` \| `information` | (전용 — logicTemplate 없음, Brief 양식 우선) |
| `meeting-material` | `objective-policy-pattern` |
| `general` | `report-default` |

`logicPattern: auto`이면 위 표를 기본값으로 Spec에 명시. writing agent가 pyramid 분석 후 변경 가능 (이유 기록).

### 2.4 requestedActionRequired

| deliverableType | audience=decision-maker |
|-----------------|-------------------------|
| `policy-planning`, `coordination` | `true` |
| 기타 | `false` (audience가 decision-maker면 권장) |

---

## 3. Memory (`.struct-memory.json`)

### 3.1 briefs 배열 항목

```json
{
  "id": "{timestamp}",
  "timestamp": "{ISO 8601}",
  "reportPurpose": "...",
  "audience": "expert",
  "deliverableType": "general",
  "logicPattern": "report-default",
  "requestedAction": "none",
  "summaryDetailRatio": "detail-only",
  "command": "write",
  "linkedDocument": "struct-docs/02-writing/..."
}
```

### 3.2 Brief 재사용

- 동일 주제 후속 요청 시 `briefs` 최근 항목 또는 `linkedDocument` 연계 prior 검색
- 사용자 옵션 `reuse-brief: true` 또는 "이전과 같은 Brief" → 최근 해당 command brief 복사, `briefSource: memory-reuse`

### 3.3 기본 memory 구조 (신규 생성 시)

```json
{
  "previousThoughts": [],
  "previousAnalysis": [],
  "previousDocuments": [],
  "previousReviews": [],
  "briefs": [],
  "maxItemsPerArray": 20,
  "userPreferences": {
    "reportTemplate": "report-default",
    "preferredFormat": "markdown",
    "defaultAudience": "expert"
  }
}
```

---

## 4. Skill Options (스킬 → Orchestrator 전달)

```
## Options
audience: decision-maker
deliverable-type: policy-planning
purpose: {reportPurpose}
timing: urgent
requested-action: 예산 승인 건의
summary-detail: split-1-5
logic-pattern: auto
reuse-brief: true
use-prior: true | false
mode: collaborative | autonomous
```

`deliverable-type`와 `deliverableType` 동일 취급.

---

## 5. Review 연계 (Phase 4)

Review Gate 시 Orchestrator가 대상 산출물과 함께 Brief·Deliverable Spec을 review agent에 주입한다.

| 대상 command | 기본 Verification Type |
|--------------|------------------------|
| think | `thinking-compliance` |
| write (prior 있음) | `both` |
| write (prior 없음) | `deliverable-quality` |
| solve / express | `deliverable-quality` |

**Deliverable DT1** (강제 rework): `requestedActionRequired` + 건의 누락 · 유형 skeleton 핵심 섹션 누락 · `draftStage` 미적용(write).

Review Data: `deliverableQuality`, `deliverableTiers.dt1_violations` — 상세: `.claude/agents/review.md` § Deliverable Quality Model.

---

## 6. Express Package (Phase 5)

| summaryDetailRatio | write 후 express |
|--------------------|------------------|
| `split-1-5` | Executive Brief (~1/5) + Attachments Index; meeting-material 시 Slide Deck |
| `summary-only` | Executive Brief 1페이지 |
| `detail-only` | express 자동 트리거 없음 (명시 `brief`/`package`만) |

**Context 주입** (`## Express Package`):
```markdown
packageMode: brief | package | slide-deck | attachments
fullReportSource: struct-docs/02-writing/...
trigger: post-write-auto | user-explicit
```

템플릿: `struct-docs/templates/express-executive-brief.md`, `express-meeting-slide-deck.md`, `express-attachments-index.md`

---

## 7. Source Validation (Phase 6)

| 옵션 | 동작 |
|------|------|
| `research-first: true` | write/solve 전 `/struct-research` 선행 |
| `verification: source-quality` | review 시 S1~S5 점검 |
| `verification: deliverable+source` | Deliverable + Source 통합 |

체크리스트: `reference/source-validation-checklist.md`  
저장: `struct-docs/06-researching/` · memory: `previousResearch[]`