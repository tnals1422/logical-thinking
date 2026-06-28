# Submission-Ready Checklist (제출 가능 Full Report)

> **목적**: `struct-docs/02-writing/` 산출물이 review pass 후 **수요자에게 Markdown 그대로 제출** 가능한지 판정.  
> **형식**: Markdown (docx/PDF는 추후 Wave 5).  
> **적용**: writing **W4 External Face** 자체 점검 + review **DT-Submission** (Wave 2).

---

## 1. 제출 가능 정의

**submissionReady: true** = 아래 ST1~ST6 **전부 pass** + W4 정리 완료.

| 구분 | 설명 |
|------|------|
| **Working** | W1~W3 내부 흔적 (Audience Pass, MECE, Pipeline Meta, Appendix 품질) — **제출본에 없음** |
| **Submission** | 표지·개요·본문·건의·참고·출처만 — `02-writing/` 저장본 |

---

## 2. Submission Tier (ST1~ST6)

| # | 코드 | 항목 | fail 조건 |
|---|------|------|----------|
| ST1 | logic | `logicSectionsFilled: pass` (D7) | 임베드 단계 누락·placeholder만 |
| ST2 | facts | 핵심·건의에 `TBD`·`출처 미확인`·`{placeholder}` | 건의·핵심 수치·조치 문장에 잔존 |
| ST3 | clean | 파이프라인 흔적·내부 링크 | Audience Pass, MECE, Pipeline Meta, Appendix 품질, `struct-docs/05-reviewing` 등 |
| ST4 | meta | 표지 메타 | 작성일·작성자·보고 목적 누락; 테스트용 작성자 그대로 |
| ST5 | action | 수요자 조치 | `requestedActionRequired` 시 건의가 누가·무엇·언제 수준 아님 |
| ST6 | tone | audience 톤 | 아래 §3 미충족 |

**W4 실패 시**: `submissionReady: false` · `draftStage: audience-revised` 유지 · Working 섹션은 W3 상태 보존(재실행용).  
**W4 pass 시**: Working 섹션 **제거 후** 저장 · `draftStage: submission-ready`.

---

## 3. audience별 Definition of Done

| audience | 제출본에 반드시 |
|----------|----------------|
| `decision-maker` | 개요에 결론·건의 요지 · §건의 구체적 · 두괄식·평이한 용어 |
| `expert` | 기술 근거·출처 · 용어 정의(필요 시) · 인과·STAD/IAEJ 단계 채움 |
| `meeting-participant` | 회의 목적·안건·기대효과 · meetingPurpose 분기만 |
| `reference-only` | 건의·조치 요청 **없음** · 사례·시사점·한계 |

---

## 4. W4 External Face — 제거·정리 대상

제출본에서 **삭제** (frontmatter에만 상태 기록):

- `## Audience Pass`
- `## MECE 검증 결과`
- `## Writing Pipeline Meta`
- `## Appendix: 품질 점검` (및 logicSectionsFilled 블록)
- HTML 주석 `<!-- … -->`
- `재생성:` / `Review Feedback` / `struct-docs/05-reviewing/` 안내 문장
- `사용 템플릿:` / `prior thinking 기반` (에이전트 메타)

**유지** (수요자용):

- 표지 메타 표 · 개요 · 본문 전 섹션 · 건의 · 참고·첨부 · `## 출처·가정` (해당 시)

**작성자**: `Struct Agent Team (테스트)` 등 → Brief·User Input에서 추론한 실제 소속/이름으로 교체. 불명 시 `작성자: (미기입 — 제출 전 확인)` 1줄만 표지에.

---

## 5. Brief · Spec 연동

```markdown
submissionTarget: true | false   # 기본 true (write 제출 목적 시)
```

| submissionTarget | 동작 |
|------------------|------|
| `true` | W1→W2→W3→**W4** → `02-writing/` 제출본 저장 |
| `false` | W3까지 · Working 섹션 포함 · `draftStage: audience-revised` |

Deliverable Spec `writingStages` (submissionTarget=true):

```
skeleton → draft → audience-revised → submission-ready
```

---

## 6. frontmatter (제출본)

```yaml
draftStage: submission-ready
submissionReady: true
submissionTarget: true
writingPipeline: w1-w2-w3-w4
deliverableType: ...
audience: ...
```

`submissionReady: false`이면 제출 금지 — W3 재실행 또는 사용자 확인.

---

## 7. 에이전트 Read 시점

| 에이전트 | 시점 |
|----------|------|
| writing | W4 시작 전 `Read` — ST1~ST6 + §4 제거 목록 |
| review | DT-Submission — 본 체크리스트 재검 · Review Data `submissionReady` |

체크리스트: `reference/submission-ready-checklist.md`