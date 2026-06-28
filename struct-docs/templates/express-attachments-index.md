---
tags: [struct, template, express]
pattern: express-attachments-index
name: "Attachments Index"
useWhen: "Express Package 생성 시 Full Report의 첨부·참고자료 목차. package 모드에서 executive-brief와 함께."
outputStyle: "목차 + 하이퍼링크. 본문-부속 연결."
reference: "reference/president/대통령 보고서.md Ch.6 첨부"
---

# 첨부·참고자료 목차

| # | 자료명 | 유형 | 링크·위치 |
|---|--------|------|----------|
| 0 | **Full Report (상세본)** | 본문 | [{제목}]({fullReportPath}) |
| 1 | **Executive Brief (요약)** | 요약 1:5 | [{제목}]({executiveBriefPath}) |
| 2 | {첨부1 — 표·도표·부록} | {표/부록/외부} | {Full Report §n 또는 URL} |
| 3 | {첨부2} | { } | { } |

## 본문-부속 연결

| 본문 섹션 | 관련 첨부 |
|----------|----------|
| {§2 현황} | {첨부 #2} |
| {§3 대안} | {첨부 #3} |

## 미포함·추후 보완

- {아직 없는 자료 — TBD}

---

## 패턴 가이드 (출력 제외)

- Full Report 내 표·Mermaid는 "본문 내장"으로 목록만
- 외부 URL·파일은 명시적 링크
- meeting slide deck 생성 시 #1 옆에 slide 경로 추가