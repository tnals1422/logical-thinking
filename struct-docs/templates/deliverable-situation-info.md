---
tags: [struct, template, deliverable, situation, information]
pattern: deliverable-situation-info
deliverableType: situation | information
name: "상황·정보보고서"
useWhen: "Brief.deliverableType이 situation 또는 information일 때. 신속·정확·간결한 사실·현황·이슈 분석 보고."
outputStyle: "제목·도입문 5W + 개조식 본문. 논리 패턴 템플릿 미적용. 상황=subType situation, 정보=subType information."
subTypes: situation | information
reference: "reference/president/대통령 보고서.md Ch.3 상황·정보"
---

# {제목 — 핵심 사항 압축, 동작성 단어로 종결. '~동향'·'~현황' 단독 사용 지양}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| subType | {situation | information} |
| 시급성 | {urgent | normal} |

> **【도입문】** {who}가 {when} {where}에서 {what}. {핵심·흥미 요약 1~2문장. information 시 분석·대책 방향 포함 가능}

---

## 본문

### {핵심 사안 1 — 중요도 순}

- {사실 관계 — 능동형, 2~3줄 이내}
- {관련 수치·출처}

### {핵심 사안 2}

- {내용}

### {부수 사안 — 뒤쪽 배치}

- {내용}

| 항목 | 내용 | 비고 |
|------|------|------|
| {비교 항목} | {값} | {출처} |

### 예상 동향·관련 사항 (해당 시)

- {전망·연계 이슈}

---

## 결론

<!-- subType=situation 이고 긴급 속보만 전달 시 섹션 생략 가능 -->

- **평가**: {객관적 평가 1~2문장}
- **대응·대책**: {실현 가능한 조치 — information 시 권장, situation 시 해당 시}
- **고려사항**: {리스크·불확실성}

---

## 참고·첨부

- {첨부 — 짧고 중요하면 본문 끝에 통합}

---

## Appendix: 품질 점검

- subType: {situation | information}
- 제목·도입문 5W: {pass / fail}
- 사용 템플릿: deliverable-situation-info.md
- prior thinking 기반: {yes — 본문 보강만 / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### subType 분기

| subType | Brief 매핑 | 결론 | 톤 |
|---------|-----------|------|-----|
| `situation` | deliverableType=situation, timing=urgent | 생략 가능 | 사실·현황 중심 |
| `information` | deliverableType=information | 대책·평가 권장 | 단일 주제 분석 |

Orchestrator/Brief에 `subType`이 없으면 `deliverableType`에서 추론 (situation→situation, information→information).

### 제목 규칙

- 20자 이내 권장, 본문에 없는 내용 금지
- 약어는 풀어서 표기: `GPS` → `위성이용 위치측정장치(GPS)`
- 끝 단어 띄어쓰기로 강조 (예: `… 추진`, `… 요망`, `… 발생`)

### Pyramid Consumption (prior 있을 때)

- fact-first 산출 — prior는 **본문 근거 보강**에만 사용
- coreClaim → 도입문 또는 결론 첫 줄
- Level 1 → 본문 소제목 순서 (중요도 재정렬)

### Review T1 체크

- [ ] 도입문: who → when → where → what
- [ ] 제목이 본문과 일치
- [ ] 2~3줄 초과 문장 최소화

### Gold reference

`reference/president/상황보고서1_집중호우관련 상황보고.pdf`, `상황보고서2_주요 국정상황.pdf`, `정보보고서_OOO OOO활동실태.pdf`