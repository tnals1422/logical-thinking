---
tags: [struct, template, deliverable, policy]
pattern: deliverable-policy-planning
deliverableType: policy-planning
name: "정책기획보고서"
useWhen: "Brief.deliverableType이 policy-planning일 때. 신규·변경 정책·전략·과제 제안 등 의사결정권자에게 건의하는 보고서."
outputStyle: "서술형 개조식 + 표 + 시각자료. 제목 20자 이내. decision-maker 대상 시 건의 필수."
defaultLogicPattern: iaej-pattern
logicSections:
  - "§2.2 iaej-pattern (필수)"
  - "§3.0~3.3 scqa-pattern (필수)"
  - "§4 objective-policy-pattern (필수)"
reference: "reference/president/대통령 보고서.md Ch.2 정책기획"
---

# {제목 — 20자 이내, 핵심 주제·동작성 단어로 종결}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| 보고 목적 | {Brief.reportPurpose 한 줄} |

## 개요

> {coreClaim — 정책 의제화 핵심 주장 1문장. 가능하면 결론 포함}

- {핵심 요지 bullet 2~3개}
- {수요자 조치 요약 1줄 — requestedActionRequired 시}

---

## 1. 보고 개요

### 1.1 보고의 목적과 필요성

- **이슈 심각성·시급성**: {왜 지금 정책 검토가 필요한가}
- **상위 목표 연관성**: {상위 정책·전략·목표와의 관계}
- **정책 검토 필요성**: {정책으로 다룰 이유}

### 1.2 보고서의 작성경위

- {추진·협의·토론 경과}
- {관계 부서 협의 및 논란·해결 과정 — 해당 시}
- {정책 추진 이력 요약 — 해당 시}

## 2. 현황과 문제점

### 2.1 현황과 실태

- {객관적 사실·통계·추세 — 개조식}
- {과거→현재 변화, 속도}

| 지표 | 현재 | 추세 | 출처 |
|------|------|------|------|
| {항목} | {값} | {↑↓} | {출처} |

### 2.2 원인분석 (logic: iaej-pattern — **필수 전개**)

> iaej-pattern의 4단계(기반구조 → 행동 → 사건 → 판단 + '이런 까닭에 →' 제언)를 전개하라.
> 패턴 단계 제목과 구조는 `patterns/reports/iaej-pattern.md`를 따른다. (상세: EMBEDDING-GUIDE.md)

### 2.3 기존 대응 사례

| 정책·조치 | 시기 | 담당 | 기대 효과 | 실제 결과 |
|----------|------|------|----------|----------|
| {조치명} | {연도} | {주체} | {기대} | {성공/실패·요약} |

### 2.4 국내외 유사 사례

- **국내**: {사례·시사점}
- **해외**: {사례·시사점}

## 3. 정책수단과 대안 (logic: scqa-pattern — **필수 전개**)

> scqa-pattern의 구조(Situation·Complication·Question·Answer + 근거)를 적용하되, 정책대안 비교·선정에 맞게 변형.
> 상세 단계는 `patterns/general/scqa-pattern.md` 참조. (EMBEDDING-GUIDE.md)

### 3.0 핵심 질문 (Question)

> {§2 문제를 해결하기 위한 1문장 질문}

### 3.1 정책대상과 자원

| 구분 | 대상·규모 | 영향 (혜택/불이익) |
|------|----------|-------------------|
| 수혜 집단 | {누구} | {효과} |
| 불이익 집단 | {누구} | {리스크·완화} |

**소요 자원**: 조직 { } · 인력 { } · 예산 { } · 기간 { } · 조달 가능성 { }

### 3.2 정책대안 (Answer)

| 대안 | 목표 적합 | 근본 해결 | 실천 가능성 | 권장 |
|------|:--------:|:--------:|:----------:|:----:|
| {대안 A} | {○/△/×} | {○/△/×} | {○/△/×} | {채택/검토} |
| {대안 B} | | | | |

**권장 대안**: {대안명} — {선정 이유 1~2문장}

#### 근거 1: {제목}

- {포인트 · 데이터 · 함의}

#### 근거 2: {제목}

- {포인트 · 데이터 · 함의}

### 3.3 예상 효과

- **직접 효과**: {정량·정성}
- **간접·파급 효과**: {부작용·리스크 포함}
- **현재 대비 변화**: {before → after}

## 4. 추진계획 (logic: objective-policy-pattern — **필수 전개**)

> objective-policy-pattern의 (n) **목적 / 방침** 쌍 형식을 사용.
> 패턴 상세: `patterns/reports/objective-policy-pattern.md`

(1) **목적**: {1단계 달성 상태}

    **방침**: {실행 방식·담당·제약}

(2) **목적**: {2단계 달성 상태}

    **방침**: {실행 방식}

(3) **목적**: {3단계 달성 상태}

    **방침**: {실행 방식}

### 4.1 추진 일정

| 단계 | 내용 | 기한 | 담당 |
|------|------|------|------|
| 1 | { } | { } | { } |
| 2 | { } | { } | { } |

### 4.2 홍보·품질관리 (해당 시)

- **홍보**: {방법}
- **점검·평가**: {KPI·평가 주기}

## 5. 건의와 제안

> {수요자에게 요청하는 핵심 사항 — Brief.requestedAction 반영}

- [ ] {건의 1 — 구체적·실행 가능}
- [ ] {건의 2}
- [ ] {건의 3}

---

## 참고·첨부

- {첨부 1 제목}
- {첨부 2 제목}

---

## Appendix: 품질 점검

- 검증 결과: {passed / partial / failed}
- logicSectionsFilled: {§2.2 IAEJ 4단계 · §3 SCQA · §4 목적-방침 — pass/fail}
- 사용 템플릿: deliverable-policy-planning.md
- logicPattern: iaej-pattern + scqa-pattern + objective-policy-pattern
- prior thinking 기반: {yes / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### Pyramid Consumption

| Prior | 매핑 위치 |
|-------|----------|
| coreClaim | 개요 + §5 건의 |
| Level 1 #1~2 | §2.1·§2.2 IAEJ |
| Level 1 #3~4 | §3.2 대안·§3.3 효과 |
| GPS 비교표 | §3.2 대안 표 |

### Review T1

- [ ] §2.2 (logic: iaej-pattern) 4단계 모두 채움 + '이런 까닭에' 최소 2개 (패턴 단계 기준)
- [ ] §3 (logic: scqa-pattern) Question 1문장 + Answer 구조·근거
- [ ] §4 (logic: objective-policy-pattern) 목적-방침 쌍 **최소 2개**
- [ ] §5 건의 — decision-maker 시 필수

### Gold reference

`reference/president/정책기획보고서1_청년실업 원인분석 보고.pdf`, `정책기획보고서2_OO시스템실용성제고방안.pdf`