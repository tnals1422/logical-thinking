---
tags: [struct, usage, think]
created: 2026-06-17
type: cheatsheet
---
# /struct-think — 아이디어 구조화

## 문법
```
/struct-think {아이디어 또는 주제}
```

## 모드
- **참여형 (기본)**: "자세히 같이 해줘", "검토하면서" → 단계별 확인 (Prior, CS, GPS, coreClaim, MECE)
- **자율형**: "빠르게", "한 번에" → 자동 전체 실행 + 최소 요약만

Orchestrator가 모든 호출을 중계하고, 모드를 감지하여 Specialist에 전달합니다.

## 언제 쓰나
- 생각이 많을 때 논리적으로 정리하고 싶을 때
- 보고서 작성 전 핵심 메시지를 잡아야 할 때
- 복잡한 주제를 MECE하게 분류하고 싶을 때

## 예시
```
/struct-think 자세히 같이 AI가 교육을 바꿀 3가지 이유
/struct-think 빠르게 우리 팀의 생산성이 낮은 이유
```

## 출력
- 핵심 주장 1문장 + Level 1 (3~5개) + GPS 구조 (ASCII/Mermaid) + 표
- 기본: 개조식 + 시각 자료
- 저장: `struct-docs/01-thinking/YYYYMMDD-{title}.md`
- Pyramid Data 블록 포함 (후속 write/solve에서 재사용)

## 관련
- `/struct-think` 결과는 Orchestrator가 writing/solve/express 호출 시 **Prior Thinking Pyramid** 로 자동 주입한다.
- 권장 워크플로: `/struct-think` → `/struct-write` 또는 `/struct-solve` (Pyramid Consumption 활용)
- 전체 흐름: [workflow.mmd](workflow.mmd) 참조
- 상세 시나리오: [scenarios.md](scenarios.md) (S01, S02, S06 등)
