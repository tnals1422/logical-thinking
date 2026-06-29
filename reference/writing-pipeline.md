# Writing Pipeline & Logic Pattern Embedding (Detailed Rules)

> **목적**: writing agent의 W1~W4 파이프라인과 `(logic: …)` 임베드 규칙을 중앙화. 
> **사용**: writing.md는 고수준 역할만 유지하고, 상세 실행은 이 파일을 Read하여 적용.
> **관련**: `reference/submission-ready-checklist.md`, `struct-docs/templates/README.md`, `reference/agent-shared-contract.md`

## Logic Pattern Embedding (필수)

**Phase 3 Unification 원칙**:
- `deliverables/` = 고수준 컨테이너 + `(logic: …)` 앵커만 제공 (thin profile).
- `patterns/` = 논리 단계 구조의 **Primary Source of Truth**.
- `(logic: pattern-name)` 앵커 내부는 **패턴 파일**의 core logic steps를 따름.
- deliverable은 패턴의 내부 단계 제목을 미리 복제하지 않는다.

### 실행 규칙

1. **W1 (Skeleton)**: 
   - deliverable의 상위 섹션 제목·순서·메타 배치.
   - `(logic: …)` 앵커 위치에 **패턴의 핵심 단계**를 주입 (패턴 파일 Read).
   - deliverable은 컨테이너 역할만 (예: "### 2.2 원인분석 (logic: iaej-pattern)").
2. **W2 (Draft)**: 
   - W1에 배치된 패턴 단계 내부에 bullet·표·Mermaid **1개 이상** 채움.
   - `logicTemplate` (패턴) Read 후 내용 구체화. 한 줄 요약 금지.
3. **W3**: Appendix `logicSectionsFilled` 기록 (frontmatter `logicSections` 대조).
4. **W4**: ST1(D7) pass 후 Appendix 제거.
5. **분기**: `subType` / `meetingPurpose` / `eventPhase` / `logicPatternMode`에 따라 해당 분기만 출력.
6. **incident-causal-pattern**: `logicPatternMode: ser|stad` 명시 (기본 추론: 장애·RCA → stad, 조정·대응 → ser).

### deliverable별 임베드 앵커 (고수준)

| deliverable | 필수 앵커 (패턴이 단계 제공) |
|-------------|-----------------------------|
| policy-planning | §2.2 (iaej-pattern) · §3 (scqa-pattern) · §4 (objective-policy-pattern) |
| coordination | §3 (incident-causal-pattern, mode=ser) |
| situation-info | information+인과 시 §인과 (incident-causal-pattern, mode=stad) |
| meeting-material | decision 시 §3.C.4 (objective-policy-pattern) |
| meeting-result | §2.{n} (scqa-pattern) |
| policy-reference | §2.2 (iaej-pattern lite) |
| event | planning A.3 (objective-policy) / progress B.2 (incident-causal ser) |

상세 embed 규칙: `struct-docs/templates/patterns/EMBEDDING-GUIDE.md`

### 실패 조건 (Review D7 — DT1 · force_rework)

- `logicSections`에 선언된 앵커가 본문에 없거나, 해당 패턴의 **핵심 단계**가 누락·미채움 → fail
- 패턴 단계 제목 변경 (L4) 금지 (패턴 파일과 대조)
- placeholder만 → fail
- 재생성: `logic_embed.fix_stage: W2 draft` — **임베드 섹션만** 보강 (별도 패턴 문서 금지)

## Writing Pipeline (Phase 3 + W4)

Brief·Deliverable Spec·템플릿 선택·Pyramid Consumption 규칙 확정 **이후**, 본문 작성은 **W1 → W2 → W3** 순으로 내부 실행한다.  
`Brief.submissionTarget: true`(기본)이면 **W4 External Face**까지 실행 후 제출본만 저장한다.  
`## Deliverable Spec`에 `writingStages`가 있으면 그 순서를 따른다.

### 실행 순서 개요

```
submissionTarget: true (기본)
  W1 → W2 → W3 → MECE + Critique → W4 External Face → 제출본 저장

submissionTarget: false
  W1 → W2 → W3 → MECE + Critique → Working 포함 저장
```

| 단계 | 명칭 | 핵심 작업 | 내부 흔적 |
|------|------|----------|----------|
| **W1** | 뼈대 (Skeleton) | deliverable/logic 템플릿 **섹션 골격** 그대로 적용; prior Level 1 → 섹션 매핑; `{placeholder}` 채움 (내용은 bullet·키워드 수준) | `draftStage: skeleton` |
| **W2** | 초안 (Draft) | W1 골격에 **문장·표·도식** 구체화; `logicTemplate` 섹션 내부 논리 패턴 적용; 개조식 2~3줄 이내; 표·Mermaid·ASCII 적극 사용 | `draftStage: draft` |
| **W3** | 수요자 관점 수정 (Audience Pass) | `Brief.audience`·`audienceRules`·`requestedActionRequired` 기준 최종 조율 | `draftStage: audience-revised` |
| **W4** | 제출면 정리 (External Face) | `submissionTarget: true` 시만. ST1~ST6 점검 · Working 섹션 제거 · 표지 메타 정리 | `draftStage: submission-ready` |

**W1 상세**
- deliverable 최상단부터 **고수준 섹션 제목·순서·메타(표지·개요)** 배치 (thin profile).
- `(logic: …)` 앵커가 있으면 해당 패턴 파일 Read → **패턴의 핵심 단계 제목**을 앵커 아래에 주입.
- Pyramid Consumption §1·§2 매핑으로 coreClaim·Level 1을 해당 섹션에 배치.
- `meetingPurpose` / `eventPhase` / `subType` 분기 변수는 Spec·Brief에 맞게 **해당 변형만** 사용.
- 아직 완성 문장 불필요 — bullet·표 뼈대·`{TBD}` 최소화.

**W2 상세**
- W1 골격을 유지한 채 narrative·개조식 문장 완성.
- `logicTemplate` (패턴 파일) Read 후 **앵커 내부 패턴 단계**에 내용 채움.
- Default Output Style: bullet·표·다이어그램 우선.
- `requestedActionRequired: true` → 건의/결론 섹션에 **구체적 조치** 초안 작성.
- W2 완료 시 MECE·Critique **1차** 내부 점검.

**W3 상세 — 수요자 관점 수정 체크리스트**

각 항목을 점검하고 `## Audience Pass` 섹션에 pass/조치 요약 기록:

| # | 점검 항목 | audience별 조절 |
|---|----------|----------------|
| 1 | 수요자가 요구·궁금한 것을 해소했는가? | `reportPurpose`·`requestedAction` 대조 |
| 2 | 객관적·설득력 있게 작성했는가? | 근거·출처 표기; 주장 없는 견해 나열 제거 |
| 3 | 불명확한 표현·중복·피동형은 없는가? | 능동형·두괄식; 동일 내용 반복 삭제 |
| 4 | (policy-planning·coordination) 수요자 조치가 명확한가? | `requestedActionRequired` 시 건의 섹션 **한눈에** |

**audience별 W3 조절** (`Brief.audience` / schema §1.3):

| audience | W3 조절 |
|----------|---------|
| `decision-maker` | 분량 축소, Executive Summary·건의 강화, 전문용어 평이화 |
| `expert` | 기술 상세 유지, 정의 병기, 건의는 선택 |
| `meeting-participant` | 안건·쟁점·회의 목적(`meetingPurpose`) 중심 |
| `reference-only` | 사례·시사점 풍부, 건의·조치 요구 없음, 중립 톤 |

**W4 상세 — External Face (submissionTarget: true)**

W4 시작 전 **`Read reference/submission-ready-checklist.md`** — ST1~ST6 + §4 제거 목록 적용.

| # | 점검 | W4 조치 |
|---|------|---------|
| ST1 | `logicSectionsFilled: pass` (Appendix 또는 W3 기록) | fail 시 W4 중단 · `submissionReady: false` · W2 재보강 필요 |
| ST2 | TBD·출처 미확인·placeholder 잔존 | 건의·핵심 수치에서 제거 또는 `가정:` 명시 |
| ST3 | Working·내부 링크 | §4 제거 목록 전부 삭제 (`shared/submission-vs-working.md` 참고) |
| ST4 | 표지 메타 | 작성일·작성자·보고 목적; 테스트용 작성자 → User Input/Brief 기반 교체 |
| ST5 | 수요자 조치 | `requestedActionRequired` 시 건의가 누가·무엇·언제 수준 |
| ST6 | audience 톤 | checklist §3 Definition of Done |

**W4 pass**: Working 섹션 삭제 후 저장 · frontmatter `submissionReady: true`, `draftStage: submission-ready`, `writingPipeline: w1-w2-w3-w4`  
**W4 fail**: Working 섹션 W3 상태 보존 · `submissionReady: false` · `draftStage: audience-revised` 유지 · 저장은 허용(재실행용)하되 제출 금지

`submissionTarget: false` → W4 **건너뜀** · W3 완료본(Working 포함) 저장

### 모드별 W1~W4 행동

**Collaborative**
- W1 완료 후: 섹션 골격·Level 1 매핑 요약 제시 → "이 뼈대로 초안(W2) 작성할까요?" 확인 (사용자가 "한 번에" 요청 시 생략 가능)
- W2 완료 후: 초안 핵심·MECE 1차 요약 → "수요자 관점 수정(W3) 및 저장할까요?" 확인
- W3 후: Audience Pass 체크리스트 공개 → `submissionTarget: true`이면 "W4 제출면 정리 후 저장할까요?" 확인
- W4·최종: 제출본 미리보기(Working 제거 후) → "이 문서로 저장할까요?" 최종 승인
- 사용자 요청 시 W1 또는 W2 산출물을 메시지에 노출

**Autonomous**
- W1→W2→W3→(submissionTarget 시 W4) **연속 내부 실행**; 중간 단계 본문은 사용자에게 노출하지 않음
- `submissionTarget: true`: **제출본만** 반환·저장 (Working 섹션 없음); stage는 **frontmatter**만
- `submissionTarget: false`: W3 완료본 + Working 섹션; frontmatter + 하단 Pipeline Meta
- 변경 요약 3~5 bullet (prior 소비·audience 조절·건의·submissionReady 여부)

### Review Feedback과의 관계

- `## Review Feedback`이 있으면 **W1 시작 전** 지침 반영
- `logic_embed.fix_stage: W2 draft` (D7만) → W1 골격 유지, **W2에서 임베드 섹션** 보강 후 W3
- 건의·audience 등 T1 위반 → W3 우선; skeleton 누락 → W1부터 재실행

## Fallback Skeletons (기본 템플릿 없을 때)

기본 템플릿 (`patterns/general/scqa-pattern.md`)이 없으면 아래 구조 사용:

(기본 SCQA report + 간결 memo 버전 — templates/README와 patterns/ 파일을 우선 Read할 것)

## Output Requirements (요약)

- `submissionTarget: true` 시 W4 후 Working 섹션 완전 제거 + frontmatter `submissionReady: true`
- 항상 MECE 검증 + Critique Pass 결과 포함 (prior 사용 여부 무관)
- 선택된 템플릿 구조 준수

자세한 frontmatter 규칙과 제출본/Working 구분은 writing.md Output Requirements와 `reference/submission-ready-checklist.md` 참조.
