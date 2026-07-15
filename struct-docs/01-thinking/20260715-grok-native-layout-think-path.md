---
tags: [minto, think, grok-build, layout, smoke]
created: 2026-07-15
type: thought
coreClaim: "Struct 스킬·에이전트는 .grok 경로에 두어야 Grok-only 결정과 운영이 일치한다."
smoke: S3
feature: grok-native-layout
---

## 핵심 주장
> Struct 스킬·에이전트는 .grok 경로에 두어야 Grok-only 결정과 운영이 일치한다.

## 피라미드 구조

```
        Struct → .grok 배치 (결정·운영 일치)
                 /        |        \
        ① 결정 정합   ② 혼동 제거   ③ 동작 유지·배치만
```

### 근거 1: HOST-TARGET grok-only 결정과 경로 정합
- primary runtime이 Grok Build로 고정됨
- 스킬·에이전트 SoT가 `.grok/skills`, `.grok/agents`여야 결정·AGENTS.md와 동일 기준

### 근거 2: Claude 호환 경로 잔존은 온보딩 혼동
- `.claude` vs `.grok` 병존 시 수정·호출 위치 오판
- 네이티브 단일 경로가 smoke 검증 기준점을 단순화

### 근거 3: Parent-as-Orchestrator는 유지, 배치만 이전
- 프로세스 SoT·Parent 인라인·specialist-only spawn 불변
- 이전 범위 = 파일 위치·문서 경로 참조

## MECE 검증
- 결과: **passed**

## Pyramid Data (for downstream agents)

```json
{
  "coreClaim": "Struct 스킬·에이전트는 .grok 경로에 두어야 Grok-only 결정과 운영이 일치한다.",
  "level1": [
    "HOST-TARGET grok-only 결정과 경로 정합",
    "Claude 호환 경로 잔존은 온보딩 혼동",
    "Parent-as-Orchestrator 동작 유지, 배치만 이전"
  ],
  "structuringPath": "forward",
  "meceStatus": "passed",
  "gpsSummary": "branch 3축: 결정 정합 / 혼동 제거 / 동작 불변·배치 이전"
}
```
