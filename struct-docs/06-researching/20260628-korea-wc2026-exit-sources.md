---
tags: [struct, research]
created: 2026-06-28
type: source-validation
overall: partial
claimsCount: 8
sourcesCount: 3
targetTopic: "한국 2026 월드컵 32강 탈락 원인"
---

# Source Validation: 한국 2026 월드컵 32강 탈락

## Summary

- Overall: **partial**
- Claims catalogued: 8 · Sources: 3
- ST1 violations: 0 (핵심 사실은 Wikipedia·ESPN 교차) · ST2: 2 (전술·구조 원인은 분석 추론)

## Claim–Source Catalog

| # | 주장 | 유형 | 출처 | status | Tier |
|---|------|------|------|--------|------|
| 1 | A조 3위(승1·무0·패2·득2·실3·3점)로 32강 미진출 | fact | [Wikipedia Group A](https://en.wikipedia.org/wiki/2026_FIFA_World_Cup_Group_A) | corroborated | — |
| 2 | 3위 팀 중 상위 8개에 포함되지 못해 탈락 | fact | Wikipedia Group A | single-source | ST3 |
| 3 | 남아공전 0-1 패배로 조 2위·32강 직행 기회 상실 | fact | ESPN (2026-06-25), Wikipedia | corroborated | — |
| 4 | 홍명보 감독, 남아공전 선발에서 손흥민 제외 | fact | ESPN (2026-06-25) | single-source | ST3 |
| 5 | 홍 감독, 결과에 대해 "잘못된 결정" 인정 | fact | ESPN 인용 | single-source | ST3 |
| 6 | 멕시코·체코 등 A조 상위팀 대비 전력 열세 | opinion | Wikipedia 순위·전적 기반 추론 | unattributed | ST2 |
| 7 | 세대교체·K리그 기반 약화가 구조적 원인 | opinion | 업계 통상 분석 — **출처 미확인** | unattributed | ST2 |
| 8 | 2014년 이후 최악급 성적(홍 감독 2014 병행) | fact | ESPN (홍 2014 무승) | single-source | ST3 |

## Checklist (S1~S5)

| 차원 | 판정 | 비고 |
|------|------|------|
| S1 출처 명시 | partial | 경기 결과는 확정; 구조 원인은 추론 |
| S2 다중 출처 | partial | 핵심 패배는 ESPN+Wiki; 조 탈락 규칙은 Wiki 단일 |
| S3 균형 | pass | 감독 책임 vs 선수·구조 요인 병기 예정 |
| S4 시의성·권위 | pass | 2026-06 본선 직후 자료 |
| S5 인용 정합 | pass | ESPN 인용문 맥락 일치 |

## Gaps & Recommendations

1. FIFA 공식 경기 리포트·xG·세부 전술 통계 미확보 → 본문에 `출처 미확인` 표기
2. KFA·대한축구협회 공식 입장 없음 → 건의안은 일반 정책 프레임으로 작성
3. 3위 팀 순위(8팀 중 몇 위) 정확 수치 추가 조사 필요

## Balance Notes

- 감독 전술 실패만이 아닌 **조직·육성 구조** 원인 병렬 분석
- 손흥민 선발 제외는 팩트이나 인과(패배 단일 원인)는 단정 금지

## Source Validation Data (for orchestrator)

```json
{
  "overall": "partial",
  "sourceTiers": {
    "st1_violations": [],
    "st2_issues": ["구조적 원인 추론 다수", "3위 팀 순위 정밀 수치 부재"],
    "st3_notes": ["일부 ESPN 단일 출처"]
  },
  "claimsCatalogued": 8,
  "sourcesCatalogued": 3,
  "needs_clarification": false,
  "target_path": "struct-docs/06-researching/20260628-korea-wc2026-exit-sources.md"
}
```