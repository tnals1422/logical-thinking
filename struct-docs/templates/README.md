# Struct Write Templates

`struct-docs/templates/` — writing·expression agent용 문서 템플릿. **3축 디렉터리**로 분리.

```
templates/
├── README.md           # 본 파일 (source of truth)
├── deliverables/       # 산출물 유형 골격 (writing)
├── patterns/           # 논리 전개 (writing — standalone · 임베드)
├── express/            # 1:5 요약·슬라이드·첨부 (expression)
└── shared/             # 공통 메타·품질 규칙 (참조 전용)
```

**핵심 원칙**: 논리 패턴은 `patterns/`를 Read하되, 출력은 `deliverables/` skeleton의 `(logic: …)` 섹션 **제목·순서를 그대로** 따른다.

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

| 파일 | pattern | useWhen |
|------|---------|---------|
| `scqa-pattern.md` | `scqa-pattern` | general 폴백, policy §3, meeting-result 안건 |
| `iaej-pattern.md` | `iaej-pattern` | policy §2.2, reference §2.2 lite |
| `incident-causal-pattern.md` | `incident-causal-pattern` | mode=**ser** 조정·대응 / **stad** 장애·RCA |
| `objective-policy-pattern.md` | `objective-policy-pattern` | 추진·회의·행사 프로그램 |

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
| pattern | `struct-docs/templates/patterns/*.md` |
| express | `struct-docs/templates/express/*.md` |
| shared | `struct-docs/templates/shared/*.md` |

1. Deliverable Spec → `deliverables/` Read → skeleton
2. `logicSections` → `(logic: …)` 섹션 필수 전개
3. `patterns/` Read → W2 임베드 보강
4. `logicSectionsFilled` pass/fail — Review D7
5. Brief 없음 / `general` → `patterns/scqa-pattern.md`

### 2차원 매트릭스 (임베드)

| deliverableType | iaej | scqa | incident-causal | objective-policy |
|-----------------|:----:|:----:|:---------------:|:----------------:|
| policy-planning | §2.2 ◎ | §3 ◎ | · | §4 ◎ |
| coordination | · | · | §3 ser ◎ | §5 ○ |
| situation | · | · | · | · |
| information | · | · | §인과 stad ◎* | · |
| meeting-material | · | · | §3.C.5 ser ○ | §3.C.4 ◎† |
| meeting-result | · | §2 ◎ | §2 ser ○ | · |
| policy-reference | §2.2 lite ○ | · | · | · |
| event | · | · | B.2 ser ◎‡ | A.3 ◎‡ |
| general | ○ | **◎** | ○ | ○ |

---

## 파일 형식 · 신규 추가

- 파일명: 영문 kebab-case
- deliverable → `deliverables/deliverable-{type}.md`
- pattern → `patterns/{name}-pattern.md`
- express → `express/express-{role}.md`
- 본 README 표에 한 줄 추가 + `deliverable-brief-schema.md` §2.2 동기화

## 디렉터리·파일 마이그레이션 (2026-06)

| 구 경로 | 신 경로 |
|---------|---------|
| `templates/deliverable-*.md` | `templates/deliverables/` |
| `templates/*-pattern.md` | `templates/patterns/` |
| `templates/express-*.md` | `templates/express/` |
| `templates/deliverable-common-meta.md` | `templates/shared/` |
| `report-default.md` | `patterns/scqa-pattern.md` |
| SER + STAD | `patterns/incident-causal-pattern.md` |