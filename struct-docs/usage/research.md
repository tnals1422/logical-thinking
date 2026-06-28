---
tags: [struct, usage, research]
created: 2026-06-28
type: cheatsheet
---
# /struct-research — 출처·균형·다중 출처 검증

## 문법
```
/struct-research {주제·자료·인용}
/struct-research struct-docs/02-writing/xxx.md
/struct-write ... research-first:true
```

## 목적

대통령 보고서 가이드 **F3(읽을수록 의문 — 출처 불명)** 예방. 사용자 제공 자료만 분석 (웹 자동 수집 없음).

## 체크리스트 5항

| # | 차원 | 요약 |
|---|------|------|
| S1 | 출처 명시 | 주장·수치 ↔ 출처 1:1 |
| S2 | 다중 출처 | 핵심 주장 2+ 독립 출처 |
| S3 | 균형 | 반론·한계·대안 관점 |
| S4 | 시의성·권위 | 시점·기관·1차/2차 |
| S5 | 인용 정합 | 왜곡·불일치 없음 |

**Tier**: ST1(무출처)=강제 · ST2(편향)=중간 · ST3(형식)=권고

## 흐름

```
/struct-research {자료}     → Source Validation Report
/struct-research → /struct-write   (research-first)
/struct-write → /struct-review verification:source-quality
```

## 출력

- 저장: `struct-docs/06-researching/YYYYMMDD-{topic}-sources.md`
- Claim–Source Catalog 표 · Gaps · Balance Notes
- Orchestrator가 write/solve에 `## Source Validation` 주입

## 모드

| 모드 | 동작 |
|------|------|
| 참여형 | 주장·출처 표 확인 → 보완 루프 |
| 자율형 | pass/partial/fail + gap 요약 |

## 관련

- 체크리스트: `reference/source-validation-checklist.md`
- review: `verification: source-quality` — [review.md](review.md)
- 설계: `docs/struct-deliverable-system.design.md` Phase 6