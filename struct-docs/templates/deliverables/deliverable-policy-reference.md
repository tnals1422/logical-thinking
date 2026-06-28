---
tags: [struct, template, deliverable, policy]
pattern: deliverable-policy-reference
deliverableType: policy-reference
name: "정책참고보고서"
useWhen: "Brief.deliverableType이 policy-reference일 때. 건의 없음, 참고·사례·시사점."
outputStyle: "사례 풍부·중립. §2.2 IAEJ-lite(기반·사례·시사) 권장."
defaultLogicPattern: iaej-pattern
logicSections:
  - "§2.2 iaej-pattern lite (권장 — 건의·판단 제언 생략)"
requestedActionRequired: false
reference: "reference/president/대통령 보고서.md Ch.2 정책참고"
---

# {제목 — 참고 주제·범위}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| 보고 성격 | 정책참고 (건의·결정 요청 없음) |
| 보고 목적 | {Brief.reportPurpose} |

## 개요

> {핵심 참고 메시지 1문장}

- {이 자료로 알 수 있는 것 — 2~3 bullet}
- {건의·결정 요청 **없음**}

---

## 1. 보고 개요

### 1.1 목적·배경·범위

- **참고 목적**: { }
- **배경**: { }
- **범위**: {지역·시기·주제 한계}

### 1.2 작성 경위 (해당 시)

- {자료 수집·검토}

---

## 2. 현황과 사례

### 2.1 현황·실태

- {객관적 사실·통계}

| 지표 | 수치 | 출처 | 시점 |
|------|------|------|------|
| { } | { } | { } | { } |

### 2.2 배경 분석 (logic: iaej-pattern lite — **권장 전개**)

> 건의·판단(J) 제언 생략 — 기반·행동·사건만 참고 수준 분석

#### 2.2.1 기반구조 (Infrastructure)

- {관련 제도·환경·구조}

#### 2.2.2 행동 (Activity)

- {정책·제도 운영 패턴}

#### 2.2.3 사건 (Event)

- {주목할 변화·이슈·결과}

### 2.3 정책·제도 사례

#### 사례 1: {국가·정책명}

- **도입 배경**: { }
- **효과(+)**: { }
- **효과(-)·한계**: { }

| 사례 | 배경 | 효과(+) | 효과(-) | 유사도 |
|------|------|---------|---------|:------:|
| { } | { } | { } | { } | {高/中/低} |

### 2.4 전문가·연구 의견 (해당 시)

| 출처 | 핵심 견해 |
|------|----------|
| { } | { } |

---

## 3. 시사점

### 3.1 시사점과 교훈

- {우리 상황 시사점}
- {적용 가능·주의}

### 3.2 우리와의 차이

- {맥락 차이·이식 리스크}

### 3.3 자료 한계

- {출처·시점·해석 한계}

---

## 참고·첨부

- {원문·통계 링크}

---

## Appendix: 품질 점검

- 검증 결과: {passed / partial / failed}
- 건의 없음: {pass — 필수}
- logicSectionsFilled: {§2.2 IAEJ 3단계 — pass/fail/n/a}
- 사용 템플릿: deliverable-policy-reference.md
- prior thinking 기반: {yes / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### IAEJ lite

- §2.2.4 판단·제언 **출력 금지** (reference-only)
- §3 시사점은 사실·비교 기반, 건의 문구 금지

### Review T1

- [ ] 건의·승인 요청 **없음**
- [ ] §2 사례 2건 이상 또는 충분한 깊이
- [ ] §3.3 한계 명시

### Gold reference

`reference/president/정책참고보고서_독일노동시장개혁방안 검토보고.pdf`