---
description: 생성된 결과물(write/solve/express)의 Pyramid Consumption 충실도를 검증하고, 필요 시 재생성을 유도합니다
user-invocable: true
---

# /struct-review

thinking 이후 write / problem-solving / expression 결과물에 대해 **Fidelity 검증**을 수행하는 독립 검수 스킬. 최종 결과물 나오기 전 마지막 품질 게이트 역할.

## 주요 사용 목적
- prior thinking pyramid가 downstream에서 얼마나 충실히 소비됐는지 검증 (Fidelity 최우선)
- 문제 발견 시 구체적인 재생성 지침(Regeneration Directives) 제공
- Orchestrator가 제어하는 자동 재생성 루프 지원

## 문법
```
/struct-review
/struct-review [파일경로]
/struct-review last
```

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트와 최근 산출물을 확인한다.
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
review 수행

## User Input
{사용자 추가 지시 또는 빈 값}

## Target
{검토할 대상 파일 경로. 지정되지 않으면 최근 write/solve/express 결과 추론}

## Options
source: {명시적 prior thinking 경로 (선택)}
auto-regenerate: true | false   (기본: mode에 따라 Orchestrator 판단)

## Context (from .struct-memory.json)
{최근 thinking / write / solve / express 결과 요약}
```

3. Orchestrator가 반환한 Review 결과를 사용자에게 출력한다.

## 동작 원칙
- Fidelity 검증을 최우선으로 수행 (coreClaim 계승, Level 1 매핑, 구조 활용, 투명성)
- Rubric 기반 품질 평가는 보조
- 항상 **Regeneration Directives** (재생성 지침)를 구체적으로 생성
- Review 결과는 `struct-docs/05-reviewing/` 에 저장
- Orchestrator가 review 후 fidelity 상태에 따라 재생성 루프를 제어 (autonomous에서 적극, collaborative에서는 확인 후)

## Fallback
Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "review"`**으로 직접 호출한다:
1. 반환된 검토 결과를 사용자에게 출력한다
2. `.struct-memory.json`에 review 결과 요약 기록
3. `struct-docs/05-reviewing/YYYYMMDD-{title}-review.md` 저장

## 예시
```
/struct-review
/struct-review 자세히 같이
/struct-review struct-docs/02-writing/20260620-xxx.md
```