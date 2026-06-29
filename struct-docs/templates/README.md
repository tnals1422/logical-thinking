# Struct Write Templates

`struct-docs/templates/` — writing·expression agent용 문서 템플릿. **4축 디렉터리**로 분리.

```
templates/
├── README.md           # 본 파일 (source of truth)
├── deliverables/       # 산출물 유형 프로파일 (thin packaging + composition 선언)
├── patterns/           # 논리 전개 (Primary Logic Core — standalone · 임베드)
│   ├── education/      # 조건·지침 등 교육용 패턴 (Case-Measure)
│   ├── proposals/      # 기획·제안용 패턴 (FABE, PREP)
│   ├── reports/        # 보고서용 패턴 (IAEJ, objective-policy, incident-causal)
│   └── general/        # 범용 fallback (SCQA)
├── express/            # 1:5 요약·슬라이드·첨부 (expression)
└── shared/             # 공통 메타·품질 규칙 (참조 전용)
```

**patterns/ 구조**: desktop '생각정리, 문서 작성법 구축' 자료와 유사하게 **사용 목적별 하위 폴더**로 그룹화하여 육안 파악 용이. 에이전트 glob은 `patterns/**/*.md` (recursive).

## Role Contract (2026-06 Unification)

**핵심 원칙**:
- `patterns/` = **Primary Logic Source of Truth**. 논리 전개 방식(단계 제목·개발 방법·시각화)은 patterns가 주도한다.
- `deliverables/` = **Thin Report Type Profile**. 고수준 문서 구조, 타입별 보강(특정 표·분기·건의 규칙), composition 선언(`logicSections`), 수요자/제출 운영 규칙만 담당.
- `(logic: xxx-pattern)` 앵커가 있으면, 해당 패턴의 **core logic steps**를 권위 있게 따르고, deliverable은 컨테이너 역할만 한다.
- 패턴 개선(단계 추가, 예시 강화 등)이 임베드된 모든 산출물에 자동 반영되도록 유지한다.

기존 "deliverables skeleton이 제목·순서를 그대로 따른다"는 규칙은 patterns 중심으로 전환되었다. 상세: `docs/struct-template-role-unification-plan.md`.

**patterns/ 구조**: desktop '생각정리, 문서 작성법 구축' 자료와 유사하게 **사용 목적별 하위 폴더**로 그룹화.

---

## deliverables/ — 산출물 골격

| 파일 | deliverableType | logicSections (임베드) |
|------|-----------------|------------------------|
| `deliverable-policy-planning.md` | `policy-planning` | §2.2 IAEJ · §3 SCQA · §4 목적-방침 |
| `deliverable-coordination.md` | `coordination` | §3 SER |
| `deliverable-situation-info.md` | `situation`, `information` | §인과 STAD (information+인과 시) |
| `deliverable-meeting-material.md` | `meeting-material` | §3.C.4 목적-방침 (decision) |
| `deliverable-meeting-result.md` | `meeting-result` | §2 안건별 SCQA |
| `deliverable-policy-reference.md` | `policy-reference` | §2.2 IAEJ lite |
| `deliverable-event.md` | `event` | A.3 / B.2 SER |

---

## patterns/ — 논리 전개

(디렉토리 구조는 사용 목적별로 시각적으로 그룹화 — desktop '생각정리, 문서 작성법 구축' 과 유사. 상세: `patterns/README.md`)

| 하위 폴더 | 파일 | pattern | useWhen |
|-----------|------|---------|---------|
| general/ | `scqa-pattern.md` | `scqa-pattern` | general 폴백, policy §3, meeting-result 안건 |
| reports/ | `iaej-pattern.md` | `iaej-pattern` | policy §2.2, reference §2.2 lite |
| reports/ | `incident-causal-pattern.md` | `incident-causal-pattern` | mode=**ser** 조정·대응 / **stad** 장애·RCA |
| reports/ | `objective-policy-pattern.md` | `objective-policy-pattern` | 추진·회의·행사 프로그램 |
| proposals/ | `fabe-pattern.md` | `fabe-pattern` | 경쟁·대안 우위 비교·설득 (제안·선택) |
| proposals/ | `prep-pattern.md` | `prep-pattern` | 단일 주장·제언 설득 (Answer 내부 micro 또는 standalone) |
| education/ | `case-measure-pattern.md` | `case-measure-pattern` | 규칙·지침·적용 조건·SOP (조건 → 조치/판단) |

---

## express/ — 패키지 산출

| 파일 | packageRole |
|------|-------------|
| `express-executive-brief.md` | executive-brief |
| `express-meeting-slide-deck.md` | meeting-slide-deck |
| `express-attachments-index.md` | attachments-index |

---

## shared/ — 공통 참조

| 파일 | 용도 |
|------|------|
| `deliverable-common-meta.md` | 표지·Appendix·`logicSectionsFilled` · Review D7 규칙 |

---

## Agent Glob·선택

| 축 | Glob |
|----|------|
| deliverable | `struct-docs/templates/deliverables/*.md` |
| pattern | `struct-docs/templates/patterns/**/*.md` (recursive, categorized subdirs) |
| express | `struct-docs/templates/express/*.md` |
| shared | `struct-docs/templates/shared/*.md` |

1. Deliverable Spec → `deliverables/` Read → skeleton
2. `logicSections` → `(logic: …)` 섹션 필수 전개
3. `patterns/` Read → W2 임베드 보강
4. `logicSectionsFilled` pass/fail — Review D7
5. Brief 없음 / `general` → `patterns/general/scqa-pattern.md`

### 2차원 매트릭스 (임베드)

| deliverableType | iaej | scqa | incident-causal | objective-policy | fabe | prep | case-measure |
|-----------------|:----:|:----:|:---------------:|:----------------:|:----:|:----:|:------------:|
| policy-planning | §2.2 ◎ | §3 ◎ | · | §4 ◎ | 대안비교 ○ | §3 내부 ○ | 적용조건 ○ |
| coordination | · | · | §3 ser ◎ | §5 ○ | · | 포인트 ○ | 조건지침 ○ |
| situation | · | · | · | · | · | · | · |
| information | · | · | §인과 stad ◎* | · | · | · | · |
| meeting-material | · | · | §3.C.5 ser ○ | §3.C.4 ◎† | 옵션평가 ○ | 안건포인트 ○ | · |
| meeting-result | · | §2 ◎ | §2 ser ○ | · | · | 결정포인트 ○ | · |
| policy-reference | §2.2 lite ○ | · | · | · | 사례비교 ○ | · | 참고지침 ○ |
| event | · | · | B.2 ser ◎‡ | A.3 ◎‡ | · | · | 실행조건 ○ |
| general | ○ | **◎** | ○ | ○ | ○ | **◎** | ○ |

---

## 파일 형식 · 신규 추가

- 파일명: 영문 kebab-case
- deliverable → `deliverables/deliverable-{type}.md`
- pattern → `patterns/{category}/{name}-pattern.md` (category: education | proposals | reports | general 등 desktop 구조 반영)
- express → `express/express-{role}.md`
- 본 README 표에 한 줄 추가 + `deliverable-brief-schema.md` §2.2 동기화

**2026-06 desktop 자료 검토 후 추가된 패턴** (C:\Users\박수민\Desktop\생각정리, 문서 작성법 구축 에서 추출·정제)
- `proposals/fabe-pattern.md` — FABE
- `proposals/prep-pattern.md` — PREP
- `education/case-measure-pattern.md` — Case-Measure (교육 자료 기반 통합)

## 디렉터리·파일 마이그레이션 (2026-06)

| 구 경로 | 신 경로 |
|---------|---------|
| `templates/deliverable-*.md` | `templates/deliverables/` |
| `templates/*-pattern.md` | `templates/patterns/` |
| `templates/express-*.md` | `templates/express/` |
| `templates/deliverable-common-meta.md` | `templates/shared/` |
| `report-default.md` | `patterns/general/scqa-pattern.md` |
| SER + STAD | `patterns/reports/incident-causal-pattern.md` |