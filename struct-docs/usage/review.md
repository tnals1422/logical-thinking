---
tags: [struct, usage, review]
created: 2026-06-23
type: cheatsheet
---

# /struct-review — Fidelity 검증 게이트

## 문법
```
/struct-review
/struct-review [대상 파일 경로]
/struct-review last
```

## 목적
thinking → write/solve/express 이후 **최종 독립 검수자** 역할.
Pyramid Consumption **Fidelity** (충실도)를 최우선으로 검증하고, 문제가 있으면 구체적인 재생성 지침을 제공한다.

## 주요 특징 (중간 지점 설계)
- **두 가지 검증**:
  - Fidelity 검증: prior thinking이 downstream에 충실히 소비됐는가
  - Compliance 검증 (범위 제한): 단계 완료 시 핵심 규칙 준수 여부
- 현재 Compliance 감사 범위:
  - thinking 완료 후: Non-negotiables, MECE 검증, Mandatory Critique Pass 수행 여부
  - writing 완료 후: Pyramid Consumption 규칙 준수 여부
- 증거 기반 감사 (산출물에 기록된 흔적을 검사, 내부 재수행 금지)
- **Compliance Strictness Model (A)** 채택:
  - Non-negotiables (Tier 1): 매우 엄격 (위반 시 강제 우선)
  - 과정 증거 (Tier 2): 중간 강도 ("최소 증거 바" 중심)
  - 품질 깊이 (Tier 3): 관대
- 항상 **Regeneration Directives** 생성 + 강제/권고 구분
- Orchestrator가 단계 완료 게이트를 제어하고 재생성 루프를 결정 (강제 시 우선)

## 모드
- **참여형 (Collaborative)**: 상세 비교 + 인용 + "이대로 재생성할까요?" 확인 루프
- **자율형 (Autonomous)**: Fidelity 요약 + 문제 시 자동 재생성 + 최소 변경 요약

## 언제 쓰나 (단계 완료 게이트 중심)
- thinking 완료 직후: Non-negotiables / MECE / Critique Pass 수행 여부 확인
- writing 완료 직후 (특히 prior 사용): Pyramid Consumption 규칙 준수 + Fidelity 확인
- 최종 산출물의 신뢰도를 높이고 싶을 때
- Autonomous에서 "review까지" 또는 "규칙 제대로 지켰는지 확인해줘" 요청 시

**예시 흐름**:
1. /struct-think ...
2. (Orchestrator 또는 사용자) review 호출 → thinking compliance 감사
3. /struct-write ...
4. review 호출 → writing consumption compliance + fidelity 감사
5. 문제가 있으면 강제 또는 권고로 재생성

## 예시
```
# 기본 (최근 결과 검토)
/struct-review

# 참여형으로 자세히
/struct-review 자세히 같이

# 특정 파일 검토
/struct-review struct-docs/02-writing/20260623-xxx.md
```

## 출력 위치
`struct-docs/05-reviewing/YYYYMMDD-{title}-review.md`

## 출력 주요 섹션
- Fidelity Summary (Overall + 차원별 pass/partial/fail)
- coreClaim / Level 1 비교 분석 (표)
- 주요 이슈 (인용 포함)
- Regeneration Directives (재생성 지침 — 구체적)
- Recommendation
- Review Data (JSON, Orchestrator용)

## 관련 흐름
1. `/struct-think` → pyramid 생성
2. `/struct-write` (또는 solve/express) → prior 소비
3. `/struct-review` → Fidelity 게이트 + 필요시 재생성 유도

전체 워크플로우는 [workflow.mmd](workflow.mmd) 참조.

## Orchestrator 동작 (중요)
- 단계 완료 후 review를 호출하여 Compliance + Fidelity를 검사
- Review Data의 `force_rework` 또는 Recommendation의 "강제" 여부에 따라:
  - 강제: 자동 또는 확인 후 해당 단계 재호출
  - 권고: 사용자 선택 후 재호출
- Review Feedback에는 Compliance 이슈와 Fidelity 이슈를 함께 포함
- Review 자체는 재생성을 호출하지 않음 (Orchestrator만 제어)

## Compliance 강도 정책 (Option A)
- Non-negotiables 위반은 거의 항상 강제.
- 과정 증거(기록 여부)는 "최소한의 구체적 흔적"이 있으면 준수 인정.
- 내부 사고의 품질 자체는 specialist에게 맡기고, "했는지 + 기록했는지"만 본다.
- 자세한 기준은 `.claude/agents/review.md`의 "Compliance Strictness Model" 섹션 참조.

## Orchestrator의 force_rework 판단 (구체화)
Orchestrator는 Review의 `force_rework` + Recommendation + `tiers_violated` + Mode를 종합하여 최종 결정을 내린다.
- Tier 1 위반 또는 force_rework=true → 강제 우선
- Autonomous에서는 Tier 2 + Fidelity 영향 시에도 강제 경향
- Collaborative에서는 강제라 해도 사용자 확인 기회를 부여 ("강제 권고")
- 자세한 결정 표는 `.claude/agents/orchestrator.md` Step R4 참조.

## 권장 조합
- 고품질 보고서: `think → write → review`
- 고품질 발표: `think → express slide → review`
- 문제 해결 후 검증: `think → solve → review`