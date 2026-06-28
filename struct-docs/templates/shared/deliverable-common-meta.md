---
tags: [struct, template, deliverable, shared]
pattern: deliverable-common-meta
name: "Deliverable 공통 메타 (Wave 0)"
useWhen: "모든 deliverable-* 작성 시 상단·하단 공통 블록 참조. 단독 산출물 아님."
outputStyle: "표지 메타 + 개요 + Appendix 품질 점검"
---

## 공통 표지 메타 (deliverable 상단)

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| 보고 목적 | {Brief.reportPurpose 한 줄} |

## 공통 개요 블록

## 개요

> {coreClaim — 1문장}

- {핵심 요지 2~3 bullet}
- {수요자 조치 — requestedActionRequired 시}

---

## 공통 Appendix (deliverable 하단 — **Working**, W4에서 제거)

> `submissionTarget: true` 제출본에는 **포함하지 않음**. W3에서만 기록 후 W4가 삭제.  
> 상태는 frontmatter `submissionReady` · Review D7는 본문 `(logic: …)` 직접 대조 · DT-Submission ST1~ST6 (`review.md`).

## Appendix: 품질 점검

- 검증 결과: {passed / partial / failed}
- deliverableType: { }
- logicPattern: {확정 패턴 — 임베드 섹션 포함}
- logicSectionsFilled: {임베드된 논리 섹션 전부 채움 — pass/fail}
- prior thinking 기반: {yes / no}

제출본 구분: `shared/submission-vs-working.md` · `reference/submission-ready-checklist.md`

---

## 패턴 가이드 (출력 제외)

### logicSectionsFilled 규칙

deliverable frontmatter `logicSections`에 나열된 `(logic: …)` 섹션마다:

1. 패턴 **단계 제목**이 본문에 존재
2. 각 단계에 bullet·표·도식 **1개 이상**
3. 빈 placeholder·`{TBD}` 잔존 시 **fail**
4. Review D7: `logicSectionsFilled: fail` → DT1 · `force_rework` · 재생성 `fix_stage: W2 draft` (`review.md` § Logic Pattern Embedding)

### 제목 규칙 (T1)

- 20자 이내 권장, 동작성 단어로 종결
- 본문에 없는 내용 금지