---
tags: [struct, template, deliverable, situation, information]
pattern: deliverable-situation-info
deliverableTypes: [situation, information]
name: "상황·정보보고서"
useWhen: "Brief.deliverableType이 situation 또는 information일 때. 신속·정확·간결한 사실·현황·이슈·인과 분석 보고."
outputStyle: "제목·도입문 5W + 개조식 본문. subType 분기 + information·인과 주제 시 STAD 임베드."
subTypes: [situation, information]
defaultLogicPattern: incident-causal-pattern
logicPatternMode: stad
logicSections:
  - "§인과 분석 (logic: incident-causal-pattern, mode=stad) (information + 인과·장애·RCA 시 필수)"
reference: "reference/president/대통령 보고서.md Ch.3 상황·정보"
---

# {제목 — 핵심 사항 압축, 동작성 단어로 종결}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| subType | {situation | information} |
| 시급성 | {urgent | normal} |
| logicMode | {none | stad — information+인과 시 stad} |

> **【도입문】** {who}가 {when} {where}에서 {what}. {핵심 요약 1~2문장}

---

<!-- subType=situation: 아래 §사실 전달만. §인과 분석 생략 -->

## 본문 (subType = situation)

### {핵심 사안 1 — 중요도 순}

- {사실 관계 — 능동형, 2~3줄 이내}
- {관련 수치·출처}

### {핵심 사안 2}

- {내용}

| 항목 | 내용 | 비고 |
|------|------|------|
| {비교 항목} | {값} | {출처} |

### 예상 동향·관련 사항 (해당 시)

- {전망·연계 이슈}

---

<!-- subType=information: 아래 §인과 분석(STAD) 필수. 단순 현황만이면 §사실 요약 + STAD 생략 가능(패턴 가이드 참조) -->

## 인과 분석 (logic: incident-causal-pattern, mode=stad — **information + 인과·장애·RCA 시 필수**)

> incident-causal (stad): State → Trigger → Accident → Damage → Countermeasures.
> `patterns/reports/incident-causal-pattern.md` 참조.

### 상태 (State)

- {사고 전부터 지속된 설정·구조·환경}
- **위험성**: { }

### 트리거 (Trigger)

- {촉발 계기}
- **시점·조건**: { }

### 사고 (Accident)

- {직접 발생한 오류·장애·사건 — 시간순}
- {악화·확산 경로}

### 손해 (Damage)

- {서비스·인명·자산·신뢰 피해 — 정량 가능 시 수치}

### 대책 (Countermeasures)

| 단계 | 대책 | 담당·시기 | 재발 차단 |
|------|------|----------|:--------:|
| 상태 | { } | { } | {高/中/低} |
| 트리거 | { } | { } | |
| 사고 | { } | { } | |
| 손해 | { } | { } | |

---

## 결론

<!-- subType=situation + urgent 속보: 생략 가능 -->

- **평가**: {객관적 평가}
- **대응·대책**: {실현 가능한 조치 — information·STAD 시 §대책 요약}
- **고려사항**: {리스크·불확실성}

---

## 참고·첨부

- {첨부}

---

## Appendix: 품질 점검

- subType: {situation | information}
- logicSectionsFilled: {§인과 분석 (logic: incident-causal-pattern, mode=stad) 5단계 — pass/fail/n/a}
- 제목·도입문 5W: {pass / fail}
- 사용 템플릿: deliverable-situation-info.md
- logicPattern: {none | incident-causal-pattern mode=stad}
- prior thinking 기반: {yes / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### subType · STAD 적용

| 조건 | 본문 구조 | STAD |
|------|----------|------|
| `situation` | §사실 전달만 | **생략** |
| `information` + 단순 현황·이슈 | §사실 요약 + 결론 | 생략 가능 |
| `information` + 장애·RCA·인과·근본원인 | §인과 분석 | **필수** — 5단계 모두 |

`logicPattern: incident-causal-pattern` 또는 Brief 키워드(장애·RCA·인과) 시 **stad 필수**.

### Review T1

- [ ] situation: 도입문 5W, STAD 미출력
- [ ] information+stad: 상태·트리거·사고·손해·대책 **5단계** 채움
- [ ] 결론에 대책 요약 (stad 시)

### Gold reference

`reference/president/상황보고서1_집중호우관련 상황보고.pdf`, `상황보고서2_주요 국정상황.pdf`, `정보보고서_OOO OOO활동실태.pdf`