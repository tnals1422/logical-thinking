---
tags: [struct, template, logic-pattern]
pattern: incident-causal-pattern
name: "Incident Causal (구조-사건-대처 / 상태-트리거-사고-손해)"
modes: ser | stad
useWhen: |
  ser — 과거 사건 기록·조정·대응 분석. '구조는 유지 → 사건 발생 → 사람이 대처'.
  stad — 복잡 인과·장애·RCA. '지속 상태 → 트리거 → 사고 → 손해' + 대책 매핑.
outputStyle: "모드별 단계 제목을 그대로 사용. Mermaid flowchart 권장. deliverable에 임베드 시 해당 섹션 제목 유지."
scope: standalone | section-embed
replaces: structure-event-response-pattern, state-trigger-accident-damage-pattern
---

# {문서 제목}

<!-- mode=ser: 아래 SER 골격만 작성. STAD 생략 -->

> **패턴 (ser)**: Structure → Event → Response

## Executive Summary

{구조적 배경 + 사건 요지 + 대처·결과 1~2문장}

## 1. 구조 (Structure)

{변하지 않는 시스템·제도·조직·물리 조건}

- {핵심 구조 요소}
- **이 구조가 중요한 이유**: { }

## 2. 사건 (Event)

{의도와 무관하게 발생한 일 — 구조와의 인과 명시}

**발생 경위**

1. { }
2. { }

- **구조적 필연**: { }
- **우연·외부 요인**: { }

## 3. 대처 (Response)

{사람이 의도적으로 한 수습·조정·결정}

- **즉시 대처**: { }
- **후속 대처**: { }

## 결과 및 교훈

- **직접 영향**: { }
- **대처 효과**: { }
- **향후 개선**: { }

---

<!-- mode=stad: 아래 STAD 골격만 작성. SER 생략 -->

> **패턴 (stad)**: State → Trigger → Accident → Damage

## Executive Summary

{상태 + 트리거 + 사고 + 손해 + 대책 방향 1~2문장}

## 1. 상태 (State)

{사고 전부터 지속된 구조·설정·습관·환경}

- {상태 A}
- {상태 B}
- **위험성**: { }

## 2. 트리거 (Trigger)

{작지만 결정적인 촉발 계기}

- {내용}
- **시점·조건**: { }

## 3. 사고 (Accident)

{트리거로 직접 발생한 오류·장애·사건}

- {경위 — 시간순}
- {확산·악화 경로}

## 4. 손해 (Damage)

{인명·자산·서비스·신뢰 등 최종 피해 — 정량 가능 시 수치}

- { }

## 5. 대책 (Countermeasures)

| 단계 | 대책 | 담당·시기 | 재발 차단 기대 |
|------|------|----------|:-------------:|
| 상태 | { } | { } | {高/中/低} |
| 트리거 | { } | { } | |
| 사고 | { } | { } | |
| 손해 | { } | { } | |

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### 모드 선택

| mode | 선택 힌트 | deliverable 임베드 위치 |
|------|----------|------------------------|
| `ser` | 조정·쟁점·대응 기록, 계획→변경→조치 | coordination §3, event B.2, meeting-result §2 안건 |
| `stad` | 장애·RCA·인과 체인·대책 매핑 | situation-info §인과 분석, information + incident |

### SER vs STAD 용어

| SER | STAD | 공통 의미 |
|-----|------|----------|
| Structure | State | 변하지 않거나 지속되는 배경 |
| Event | Trigger + Accident | 발생·촉발·악화 |
| Response | Damage + Countermeasures | 결과·대응 |

### When embedded inside a deliverable (embed mode)

**Core logic block (ser)**:
- 1. 구조 (Structure)
- 2. 사건 (Event)
- 3. 대처 (Response)
- 결과·교훈

**Core logic block (stad)**:
- 1. 상태 (State)
- 2. 트리거 (Trigger)
- 3. 사고 (Accident)
- 4. 손해 (Damage)
- 5. 대책 (Countermeasures)

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 전체 결론 (deliverable 상위 섹션 사용)

**Rules**:
- `(logic: incident-causal-pattern, mode=ser|stad)` 앵커의 단계 제목·순서 **변경 금지**.
- 각 단계에 bullet·표·Mermaid **최소 1개 이상** 채움.
- mode는 Brief나 입력 키워드에서 추론 (ser: 조정·대응, stad: 장애·RCA).

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

### 기존 deliverable 임베드 규칙 (참고)

1. deliverable skeleton에 `(logic: incident-causal-pattern, mode=…)` 표시된 섹션은 **단계 제목·순서 변경 금지**
2. W2 Draft에서 각 단계 bullet·표·Mermaid로 **반드시** 채움 — 한 줄 요약으로 대체 금지
3. standalone 사용 시 Brief·입력에서 mode 추론 (`장애·RCA`→stad, `조정·대응`→ser)

### 시각 자료

- ser: block-beta 3열 (구조|사건|대처)
- stad: flowchart LR 또는 block-beta 4단