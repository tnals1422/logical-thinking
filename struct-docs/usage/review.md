---
tags: [struct, usage, review]
created: 2026-06-23
type: cheatsheet
---

# /struct-review — Fidelity + Deliverable Quality 게이트

## 문법
```
/struct-review
/struct-review [대상 파일 경로]
/struct-review last
```

## 목적
thinking → write/solve/express 이후 **최종 독립 검수자** 역할.
**수요자가 한 번 보고 판단·조치할 수 있는가?** — Fidelity + Deliverable Quality를 검증하고 재생성 지침을 제공한다.

## 검증 유형 (Phase 4)

| Type | 내용 | 기본 시점 |
|------|------|----------|
| `thinking-compliance` | Minto Non-negotiables · MECE · Critique | think 후 |
| `writing-consumption` | Pyramid Consumption | write 후 (prior) |
| `deliverable-quality` | Brief·유형·4대 실패 유형 | write/solve/express 후 |
| `both` | consumption + deliverable | **write 후 권장** |
| `fidelity` | prior ↔ 산출물 | prior 있을 때 |
| `full` | fidelity + both | 고품질 write |
| `source-quality` | S1~S5 출처·균형 (Phase 6) | research 후 · F3 보조 |
| `deliverable+source` | Deliverable + Source | information·policy-planning |

옵션: `verification: both` (스킬 → Orchestrator)

## 주요 특징
- **세 가지 검증 영역**:
  - **Fidelity**: prior thinking 소비 충실도
  - **Compliance**: Minto 핵심 규칙 증거 감사
  - **Deliverable Quality** (Phase 4): Brief·유형·수요자 조치·Pipeline 흔적
- **DT-Submission** (Wave 2): `submissionTarget: true` 시 ST1~ST6 · `submissionReady` 게이트
- Compliance 범위: thinking (Non-negotiables/MECE/Critique), writing (Pyramid Consumption)
- 증거 기반 감사 (산출물에 기록된 흔적을 검사, 내부 재수행 금지)
- **Compliance Strictness (A)** + **Deliverable Tier (Phase 4)**:
  - Compliance Tier 1 / **Deliverable DT1** (건의 누락·틀 부재): 매우 엄격 → 강제 우선
  - Tier 2 / **DT2** (4대 실패 F1~F4): 중간
  - Tier 3 / **DT3** (표현): 관대 · 권고만

## Deliverable Quality — 4대 실패 유형

| # | 유형 | 증상 예 |
|---|------|--------|
| F1 | 기본 틀 부재 | 메타·섹션·제목·`draftStage` 누락 |
| F2 | 장황·초점 없음 | 중복·모호 표현·주장 없는 의견 |
| F3 | 읽을수록 의문 | 배경·근거·용어·출처 부족 |
| F4 | 문제의식 부재 | 현황·원인 부족, **수요자 조치 불명** |

**DT1 강제 예**: 정책기획인데 건의 없음 · `requestedAction` 미충족 · policy-reference에 건의 있음 · **`logicSectionsFilled: fail`** (D7 — 앵커 누락 또는 패턴 단계 미채움) · **`submissionReady: fail`** (DT-Submission — Working 잔존·메타 미달)

**DT-Submission (ST1~ST6)** — `submissionTarget: true` write 전용. 상세: `reference/submission-ready-checklist.md`

| ST | 항목 | fail 시 fix_stage |
|----|------|------------------|
| ST1 | logic 임베드 (D7) | W2 draft |
| ST2 | TBD·출처 미확인 | W2/W3 |
| ST3 | Working·내부 링크 잔존 | **W4 external-face** |
| ST4 | 표지 메타 | W4 |
| ST5 | 수요자 조치 | W3 |
| ST6 | audience 톤 | W3 |
- 항상 **Regeneration Directives** 생성 + 강제/권고 구분
- Orchestrator가 단계 완료 게이트를 제어하고 재생성 루프를 결정 (강제 시 우선)

## 모드
- **참여형 (Collaborative)**: 상세 비교 + 인용 + "이대로 재생성할까요?" 확인 루프
- **자율형 (Autonomous)**: Fidelity 요약 + 문제 시 자동 재생성 + 최소 변경 요약

## 언제 쓰나
- think 후: `thinking-compliance`
- write 후: `both` (prior + Brief) 또는 `deliverable-quality`
- solve/express 후: `deliverable-quality`
- "수요자 관점 점검", "건의 확인", "review까지" 요청 시

**예시 흐름**:
1. `/struct-think` → review (`thinking-compliance`)
2. `/struct-write` (Brief·W1~W4) → review (`both` + DT-Submission)
3. DT1(건의 누락) → W3 재생성 또는 write 재호출
4. DT1 D7(logic embed fail) → **W2 draft** 보강 후 write 재호출
5. DT-Submission fail (ST3 Working 잔존) → **W4 external-face** 후 review 재실행
6. `submissionReady: pass` 후 → express Package (split-1-5)

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
- Fidelity Summary (해당 시)
- Compliance Report (해당 시)
- **Deliverable Quality Report** — F1~F4 · DT1~DT3 · **D7** · **DT-Submission (ST1~ST6)**
- 주요 이슈 · Regeneration Directives (`fix_stage`: W1/W2/W3/**W4**)
- Recommendation · Review Data (`deliverableQuality`, `submissionReady`, `submissionTiers`, `logicPatternEmbedding`)

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
Orchestrator는 `force_rework` + `deliverableQuality` + `submissionReady` + DT1 + Mode를 종합한다.
- `submissionReady: fail` → express Package 보류 · ST3/ST4는 W4 재호출
- Tier 1 / **DT1** 또는 force_rework=true → 강제 우선
- `deliverableQuality: fail` + F4(건의) → 강제 고려
- Autonomous에서는 Tier 2 + Fidelity 영향 시에도 강제 경향
- Collaborative에서는 강제라 해도 사용자 확인 기회를 부여 ("강제 권고")
- 자세한 결정 표는 `.claude/agents/orchestrator.md` Step R4 참조.

## 권장 조합
- 고품질 보고서: `think → write → review`
- 고품질 발표: `think → express slide → review`
- 문제 해결 후 검증: `think → solve → review`