---
tags: [struct, usage, solve]
created: 2026-06-17
type: cheatsheet
---
# /struct-solve — 문제 분석 + 해결안

## 문법
```
/struct-solve {문제 설명}
```

## Brief (Phase 1)

Orchestrator가 solve 전 Brief를 수집합니다 (`audience`, `deliverable-type`, `purpose` 등).
- `audience: decision-maker` → 액션 플랜에 수요자 조치 명시
- `deliverable-type: coordination` → 쟁점·이해관계 분석 강화

상세: `reference/deliverable-brief-schema.md`

## 모드
- **참여형 (기본)**: Brief 확인 → framing, 가설, Root Cause, 해결안 단계별 확인
- **자율형**: Brief 추론 → 전체 자동 + 요약만

Orchestrator: **Brief → Prior** 순 Context 구성.

## 언제 쓰나
- 원인을 모르는 문제를 체계적으로 파헤치고 싶을 때
- 가설 3개 이상 세우고 우선순위를 매기고 싶을 때
- Impact/Effort/Urgency로 해결 방향 평가가 필요할 때

## 예시
```
/struct-solve 자세히 같이 팀 생산성이 지난달 대비 30% 떨어진 원인 분석
/struct-solve 빠르게 고객 이탈률이 증가하는 이유와 대응 방안
```
## 출력
- 가설 3개 이상 (MECE) + 5 Whys + 인과 모델 + Impact/Effort/Urgency 표
- 기본: 개조식 + 표
- 저장: `struct-docs/03-solving/YYYYMMDD-{title}.md`

## 관련
- `/struct-think` 로 먼저 구조화하면 Orchestrator가 solve 호출 시 prior pyramid를 **framing 품질 향상** 용도로 주입한다 (직접 주장 이식 금지).
- 분석 결과를 문서화하고 싶을 때 `/struct-write` 사용.
- 전체 흐름: [workflow.mmd](workflow.mmd)
- 상세 시나리오: [scenarios.md](scenarios.md) (S03)
