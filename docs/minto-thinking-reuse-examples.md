# Minto Thinking Reuse — 사용 예시 및 가이드

이 문서는 `/think` 결과를 `/write`, `/solve`, `/express`에서 어떻게 재사용하는지에 대한 모범 사례를 정리합니다.

## 기본 워크플로 (권장)

1. **먼저 구조화**
   ```
   /think 하반기 KOSPI 전망과 현대차 피지컬 AI 역할
   ```

2. **이어서 문서화/분석**
   ```
   /write 같은 주제에 대한 투자 보고서 작성
   ```
   또는
   ```
   /solve 현대차가 피지컬 AI로 재평가받기 위한 핵심 조건은 무엇인가
   ```

Orchestrator가 자동으로 가장 관련성 높은 prior thinking을 찾아 `## Previous Thinking Pyramid` 블록으로 주입합니다.

## 명시적 옵션 사용 (Phase 3)

### 강제로 prior 사용
```
/write 하반기 KOSPI 3두마차 시나리오 use-prior:true
```

### Prior 완전 무시 (raw input으로 새로 분석)
```
/write 새로운 주제에 대한 보고서 source:raw
/write 새로운 주제에 대한 보고서 ignore prior
```

스킬이 이 옵션을 전달하면 Orchestrator가 우선적으로 따릅니다.

## writing에서 prior pyramid 소비 예시

**Prior thinking 출력 (간략)**:
- coreClaim: "...현대차가 피지컬 AI 플랫폼으로 재평가받아..."
- Level 1:
  1. AI IPO가 시장 프레임을 전환하는 촉매
  2. 현대차의 재평가 조건 충족
  3. 중국 취약점으로 인한 반사 수혜

**writing 결과** (좋은 재사용 예):
- 4.1 핵심 주장 → prior coreClaim 거의 그대로 사용
- 4.2~4.4 근거 → Level 1을 기반으로 narrative 확장
- GPS 표 → 근거 섹션에 재배치
- Situation/Complication → 사용자 입력 + prior CS를 참고하여 작성
- 마지막 MECE 검증 → 새로 수행 (prior 사용 명시)

## problem-solving에서 prior pyramid 소비 예시

Prior thinking은 "답"이 아니라 "문제 구조"로 활용:

- CS 분석과 GPS의 분해 품질을 문제 정의 단계에서 차용
- Level 1 Arguments를 가설 생성의 가능한 원인 축으로 참고
- Prior의 Critique Pass를 반론 탐지에 사용

**중요**: solve에서는 prior의 주장을 직접 답으로 사용하지 않습니다.

## expression에서 prior pyramid 소비 예시

- coreClaim → 슬라이드 1~2번 (핵심 메시지)
- Level 1 → 각 근거별 슬라이드
- GPS 테이블/시퀀스 → 비교 슬라이드 또는 프로세스 슬라이드

## 품질 가드레일 (모두 적용)

- Prior를 사용하더라도 **항상 최종 MECE 검증 + Critique Pass**를 수행하고 출력에 포함
- Prior 사용 여부를 투명하게 기록하는 것을 권장
- Prior가 오래되었거나 관련성이 애매하면 "오래된 prior 사용" 또는 "부분 참고" 명시

## 자동 vs 명시적 판단

| 상황 | 동작 |
|------|------|
| 옵션 없음 + 관련 prior 있음 | Orchestrator가 자동 주입 |
| use-prior:true | 강제 주입 시도 |
| ignore prior / source:raw | Prior 무시 |
| 관련 prior 없음 | 일반 context만 전달 |

## 실제 검증 포인트 (End-to-End)

- `/think` 후 바로 `/write` 호출 시, writing의 Answer 섹션이 prior Level 1을 기반으로 하는지
- Orchestrator context에 `## Previous Thinking Pyramid` 블록이 포함되는지
- Prior를 사용했는데도 MECE 검증이 제대로 수행되었는지
- `use-prior:false` 전달 시 prior가 무시되는지

이 문서는 `docs/minto-thinking-reuse-roadmap.md` Phase 3의 예시 가이드로 작성되었습니다.
