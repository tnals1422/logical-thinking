---
tags: [struct, write, general, smoke, grok, native-layout]
created: 2026-07-15
type: report
deliverableType: general
audience: expert
submissionTarget: false
draftStage: audience-revised
submissionReady: false
writingPipeline: w1-w2-w3
logicPattern: scqa-pattern
logicSectionsFilled: pass
coreClaim: "Struct 스킬·에이전트는 .grok 경로에 두어야 Grok-only 결정과 운영이 일치한다."
smoke: S3
feature: grok-native-layout
priorSource: struct-docs/01-thinking/20260715-grok-native-layout-think-path.md
---

# Grok Build 네이티브 레이아웃 이전 스모크 검증 메모

| 항목 | 내용 |
|------|------|
| 작성일 | 2026-07-15 |
| 작성자 | Struct Agent Team |
| 목적 | Grok 네이티브 레이아웃 이전 스모크 검증 기록 |
| 수요자 | expert |
| 요청 조치 | 없음 (검증 기록용) |
| Prior | `01-thinking/20260715-grok-native-layout-think-path.md` |

## Executive Summary

> Struct 스킬·에이전트는 `.grok` 경로에 두어야 HOST-TARGET grok-only 결정과 운영 경로가 일치한다.

- **결정 정합**: primary runtime = Grok Build → SoT = `.grok/skills` · `.grok/agents`
- **혼동 제거**: `.claude` 잔존 시 온보딩·수정 위치 오판
- **동작 유지**: Parent-as-Orchestrator 불변, **배치만** 이전

---

## 1. 상황 (Situation)

- Primary runtime이 **Grok Build**로 고정됨 (HOST-TARGET grok-only).
- Struct 스킬·에이전트는 공식 호스트 경로인 `.grok/`에 배치하는 것이 결정·문서(AGENTS.md)와 동일 기준.
- Parent-as-Orchestrator(프로세스 SoT 인라인 · specialist-only spawn)는 이미 검증된 운영 모델.

## 2. 문제 (Complication)

- Claude 호환 경로(`.claude`)가 잔존하면 **어디를 고칠지·어디를 호출할지** 온보딩 혼동이 남는다.
- 레이아웃 이전 없이 문서만 Grok-only로 쓰면 **결정과 파일 위치 불일치**가 스모크·운영 기준점을 흐린다.
- 이전 범위를 프로세스 재설계로 확대하면 검증 범위가 비대해짐 → **배치 이전만** 검증할 필요가 있음.

## 3. 핵심 질문 (Question)

> Struct 스킬·에이전트를 `.grok`에 두면 HOST-TARGET grok-only 결정과 운영 경로가 일치하는가?

## 4. 답변 및 근거 (Answer)

### 4.1 핵심 주장

> Struct 스킬·에이전트는 `.grok` 경로에 두어야 Grok-only 결정과 운영이 일치한다.

### 4.2 근거

| # | 근거 | 요지 |
|---|------|------|
| 1 | **결정 정합** | grok-only 결정 ↔ `.grok/skills` · `.grok/agents` SoT 동일 기준 |
| 2 | **혼동 제거** | 단일 네이티브 경로 → 온보딩·수정·호출 위치 오판 제거 |
| 3 | **동작 유지·배치만** | Parent-as-Orchestrator·프로세스 SoT 불변; 이전 범위 = 파일·문서 경로 참조 |

```
        Struct → .grok 배치 (결정·운영 일치)
                 /        |        \
        ① 결정 정합   ② 혼동 제거   ③ 동작 유지·배치만
```

#### 근거 1: HOST-TARGET grok-only 결정과 경로 정합

- primary runtime 고정에 맞춰 스킬·에이전트 물리 경로를 정렬
- 결정 stub·AGENTS.md·실제 배치가 한 기준선을 공유

#### 근거 2: Claude 호환 경로 잔존은 온보딩 혼동

- `.claude` vs `.grok` 병존 시 “어느 쪽이 SoT인가” 불명확
- 스모크 검증 기준점을 **`.grok` 단일**로 단순화

#### 근거 3: Parent-as-Orchestrator 동작 유지, 배치만 이전

- Brief · Spec · Prior · 저장 · memory = Parent 인라인 유지
- `orchestrator.md` = 프로세스 SoT(스폰 대상 아님) 유지
- 이전 범위 = 디렉터리·경로 참조; 오케스트레이션 모델 변경 아님

## 5. 결론 및 제언

- **결론**: 네이티브 레이아웃 이전의 핵심은 **경로 정합 + 혼동 제거 + 동작 불변**이다. 스모크 기록상 `.grok` 배치가 결정·운영과 일치한다.
- **요청 조치**: 없음 (검증 기록용).

### 액션 아이템

- [x] 스모크 검증 메모 기록 (본 문서)
- [ ] (선택) 잔존 `.claude` 참조·문서 정리 여부는 별도 운영 판단

---

## Working (submissionTarget: false)

### Audience Pass (expert)

| # | 점검 | 결과 |
|---|------|------|
| 1 | 검증 기록 목적 해소 | pass — 결정·혼동·동작 3축 정리 |
| 2 | 객관·설득 | pass — prior L1 매핑, 조치 요구 없음 |
| 3 | 중복·모호 표현 | pass — 1페이지 분량·개조식 |
| 4 | 수요자 조치 | N/A — `requestedAction: none` |

### MECE 검증

- 축: 결정 정합 / 온보딩 혼동 / 동작·배치 범위 → **중복·누락 없음**
- 결과: **passed**

### Critique Pass

- So What: 경로를 `.grok`에 두지 않으면 결정·문서·실제 배치가 어긋남
- Why So: L1 3축이 각각 독립 근거로 coreClaim 지지
- 가정: smoke 수준; 런타임 재검증 세부 로그는 본 메모 범위 밖

### Pipeline Meta

| 항목 | 값 |
|------|-----|
| writingPipeline | w1-w2-w3 |
| draftStage | audience-revised |
| logicPattern | scqa-pattern |
| logicSectionsFilled | pass |
| prior 소비 | yes (coreClaim + L1 → Answer 근거 1~3) |
| 사용 템플릿 | general + `patterns/general/scqa-pattern.md` |
