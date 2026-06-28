---
tags: [struct, template, express]
pattern: express-executive-brief
name: "Executive Brief (1:5 요약)"
useWhen: "Brief.summaryDetailRatio가 split-1-5 또는 summary-only일 때. Full Report의 약 1/5 분량 요약본."
outputStyle: "보고 취지 + 결론·건의 중심. 세부는 Full Report 링크. 개조식."
ratio: "1:5"
reference: "reference/president/대통령 보고서.md Ch.6 요약본"
---

# {제목 — Full Report와 동일 또는 축약}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 유형 | Executive Brief (1:5) |
| 수요자 | {Brief.audience} |
| 상세본 | [{Full Report 제목}]({fullReportPath}) |

## 보고 취지

> {reportPurpose — 1~2문장}

- {왜 지금 이 보고인가 — bullet 1~2}

## 핵심 결론

> {coreClaim — Full Report 개요·건의 요지}

| 항목 | 요약 |
|------|------|
| 현황·문제 | {1~2 bullet 압축} |
| 권고 대안 | {선정안 1줄} |
| 수요자 조치 | {requestedAction — decision-maker 시 필수} |

## 건의·조치 (해당 시)

- **요청 사항**: {구체적 조치 — 누가·무엇을·언제}
- **기대 효과**: {1줄}

## 상세 참조

본 요약은 상세 보고서의 약 **1/5** 분량입니다. 근거·대안 비교·추진계획은 아래를 참조하세요.

- **Full Report**: [{파일명}]({fullReportPath})
- **주요 섹션**: {§2 현황 · §3 대안 · §4 추진 등 링크 앵커 또는 섹션명}

---

## 패턴 가이드 (출력 제외)

- 분량: Full Report 대비 **약 20%** (문단·bullet 수 기준)
- 두괄식: 결론·건의를 앞에, 배경은 최소
- `summary-only` 시 위 구조를 더 압축 (1페이지 이내)
- policy-reference: 건의 섹션 생략, 시사점만