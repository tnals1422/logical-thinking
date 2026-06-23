---
tags: [struct, usage, express]
created: 2026-06-17
type: cheatsheet
---
# /struct-express — 슬라이드/메모/스토리 변환

## 문법
```
/struct-express {텍스트}              → 슬라이드 (기본값)
/struct-express slide {텍스트}        → Marp 호환 슬라이드
/struct-express memo {텍스트}         → 1페이지 메모
/struct-express story {텍스트}        → 스토리텔링
```
## 모드
- **참여형 (기본)**: "자세히 같이" → prior 매핑 확인 + 슬라이드 수/구조 미리보기 승인
- **자율형**: "빠르게" → 자동 매핑 + 간단 결과만 (7장 슬라이드 등)

Orchestrator가 format 파싱과 prior 주입을 처리합니다.

## 예시
```
/struct-express 자세히 같이 slide 신제품 런칭 전략 발표
/struct-express 빠르게 memo 오늘 팀 회의 결과 정리
```
## 출력
- `slide`: Marp `---` 구분 슬라이드 (5~12장) | `memo`: 1페이지 메모 | `story`: 기승전결
- coreClaim → 핵심 메시지, Level 1 → 근거 매핑
- 기본: 개조식 + 시각 구분
- 저장: `struct-docs/04-expressing/YYYYMMDD-{title}.md`

## 관련
- `/struct-think` 또는 `/struct-write` 결과가 있으면 Orchestrator가 express 호출 시 prior pyramid를 주입한다.
- coreClaim → 핵심 메시지, Level 1 → 슬라이드/메모 근거로 매핑.
- 전체 흐름: [workflow.mmd](workflow.mmd)
- 상세 시나리오: [scenarios.md](scenarios.md) (S04)
