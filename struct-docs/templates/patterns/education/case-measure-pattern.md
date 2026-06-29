---
tags: [struct, template, logic-pattern]
pattern: case-measure-pattern
name: "Case-Measure (조건-조치/판단)"
useWhen: "규칙·지침·절차·적용 기준 서술 시. '어떤 조건이면 어떻게 하라/판단하라/조치하라'. policy 문서의 적용 범위, coordination의 트리거 조건, event 실행 가이드, SOP·교육 자료에 유용."
outputStyle: "조건(Case) 명시 → 조치/판단/방법(Measure) 명시. 조건 유형(시작/완료/수칙) 구분. 표 또는 if-then 형식 + 주의사항."
scope: standalone | section-embed
---

# {문서 제목}

> **패턴**: Case(조건) → Measure(조치·판단·방법)

## Executive Summary

{전체 규칙/지침의 핵심: 주요 조건과 그에 따른 필수 조치 1~2문장 요약}

## 1. 조건 (Case)

{언제·어떤 상황에서 이 규칙이 적용되는가. 구체적이고 관찰 가능한 조건}

### 조건의 유형

- **시작 조건**: {언제 시작/적용하는가 — 예: "프라이팬이 달궈지면"}
- **완료 조건**: {언제 끝내는가 — 예: "양파가 갈색이 될 때까지"}
- **지시 수칙 / 주의 조건**: {항상 지켜야 할 제약 — 예: "타지 않도록 저온에서"}

- {추가 세부 조건 bullet}

**주의**: 조건을 생략하고 Measure만 전달하면, 수신자가 "왜 지금 이 조치인가"를 이해하지 못하거나, 부작용이 큰 경우 위험할 수 있다. 조건이 중대한 영향을 미칠 때는 반드시 명시.

## 2. 조치 / 판단 / 방법 (Measure)

{조건이 충족되었을 때 취해야 할 행동, 판단 기준, 또는 구체적 방법}

- {주요 조치 1 — 구체적 동사로}
- {판단 기준 (OK/NG, 정상/비정상)}
- {세부 절차 (번호 매김)}
- {예외 처리}

## 예시

### 예시 1. 조건 + 지시
**조건**: 출근·퇴근 시  
**조치**: 툴퇴근 관리 프로그램으로 시간을 기록한다.

### 예시 2. 조건 + 판단
**조건**: OK 응답이 있다면  
**판단**: 정상 동작 중이다.

### 예시 3. 조건 + 조치 (안전/중단)
**조건**: 중량이 초과되면  
**조치**: 출하 정지 조치한다.

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**개념**
"어떤 경우에(조건) + 어떻게 한다(조치/판단)" 구조. 조건을 무시한 채 결과만 지시하면 수신자가 맥락을 놓치거나 오용할 위험이 있다.

**용어**
- **Case (조건)**: 시작 조건, 완료 조건, 지시 수칙(제약)
- **Measure**: 평가/판단, 조치, 방법, 절차 등 실행·판단의 내용

**선택 기준 및 주의**
- 정책·규정·SOP·가이드라인 작성 시 적극 사용
- "무엇을 하라"만 있고 "언제"가 모호할 때 이 패턴으로 보강
- 지시에 이유가 붙는 경우: 지시와 이유를 분리 (조건과 별도 설명)
- Case-Measure와 조건·지시 패턴은 본질적으로 동일 계열 — 필요 시 통합 사용

**When embedded inside a deliverable (embed mode)**

**Core logic block to use**:
- 1. 조건 (Case) — 시작/완료/지시 수칙 구분
- 2. 조치 / 판단 / 방법 (Measure)

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 예시 섹션 전체 (필요 시 선택적으로 사용)

**Rules**:
- 조건을 먼저 명확히 하고, 그에 따른 Measure를 제시.
- "언제"가 모호한 지시가 있으면 이 패턴으로 보강.

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

**기존 deliverable 임베드 예시**
- `deliverable-policy-planning.md` §4 추진계획의 적용 조건·기준 부분
- `deliverable-coordination.md` 또는 `deliverable-event.md` 실행 가이드 섹션
- `deliverable-policy-reference.md` 참고 지침
- general: 규칙 나열 문서 standalone

**권장 표현**
- 표 형식: | 조건 유형 | 조건 내용 | Measure (조치/판단) |
- 번호 매김 절차 + "확인" 단계 강조
- Mermaid flowchart (condition → decision → action)도 유용

**기존 자료 기반 참고**
- Case를 강조해야 할 때: "조건에 해당하지 않는데 Measure에 중대한 부작용이 있을 수 있는 조치라면 Case를 반드시 포함"
