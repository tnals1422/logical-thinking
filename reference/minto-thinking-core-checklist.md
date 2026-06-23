# Minto Thinking Core Checklist (Shared Rules)

> **목적**: thinking.md와 다른 전문 에이전트(writing/solve/expression)가 공통으로 따라야 할 최소 필수 규칙 모음.
> **사용법**: 복잡할 때 Read 하거나, 에이전트 정의에 간단히 참조.
> **Non-negotiables는 절대 위반 금지.**

---

## Non-negotiables (위반 시 Excellent 불가)

- `coreClaim`은 **정확히 1문장**.
- Level 1 근거: **최소 2개, 최대 5개**.
- Level 1에서 명백한 중복(ME) 또는 중대한 누락(CE) 없음.
- 피라미드는 **최소 2레벨 이상**.
- MECE 검증을 **반드시 수행**하고 결과를 명시.
- 역방향 구조화 조건에 해당하면 `structuringPath: "reverse"` 정확히 기록.

---

## 6-Step Structured Reasoning (Skeleton)

### Step 1: CS Analysis
- 범주(Category)와 요점(Summary)을 추출. 추상화 계층 유지.
- 중요한 관점 누락 시 `inferredCategories`에 근거와 함께 보충.
- **강제**: `csAnalysis`, `abstractionLevels`, `inferredCategories`.
- **Self-Check**: 이 분류가 진짜 중요한 축을 잡았는가? 요점이 원문보다 구조화된 통찰을 주는가?

### Step 2: GPS Structuring
- **Group**: branch / contain / hub. **typeRationale을 반드시 1문장 이상 구체적으로** 기록.
- Parallel / Series 필요 여부 판단 (필요할 때만 생성).
- ASCII 다이어그램 생성.
- **Self-Check**: 이 구조가 정보를 가장 유용하게 보는 관점인가?

### Step 3: Pyramid Building
- CS/GPS 기반으로 Level 1 구성 (3~5개).
- `coreClaim`을 **정확히 1문장**으로 도출 (상위가 하위의 진짜 요약).
- 논리 유형(귀납/연역) 판단.

**Self-Check**:
- 이 피라미드를 한 줄로 요약하면 coreClaim과 일치하는가?
- Level 1 중 하나 제거해도 coreClaim이 지지되는가?

### Step 4: MECE Validation
- Level 1~2: **엄격한 MECE**.
- Level 3+: 실용적 MECE (완화).
- 위반 시 `issues`에 명확 기록. `singleParentViolations` 기록.
- **Self-Check**: Level 1을 보고 "다른 중요한 축이 있을까?"라는 질문이 안 들리는가?

### Step 5: Logic Analysis
- 인과관계 일관성, 비약, 순환 논증, 반론 가능성 점검.

### Step 6: Summary Generation
- `coreClaim`: 1문장.
- `summary`: **3줄 이내** (coreClaim + 주요 근거).

---

## Mandatory Critique Pass (반드시 실행)

Pyramid + MECE 이후 **무조건** 수행:

1. MECE 자가 점검 (Level 1 집중)
2. 논리 지지 점검 (각 Level 1이 coreClaim 지지?)
3. 반론 탐지 ("가장 강력한 반박 근거는?")
4. 누락 관점 탐지
5. 개선 결정
   - 심각 → Step 3 재구성 (최대 1회)
   - 경미 → `issues` 기록

**이 과정을 생략하면 최종 출력을 생성하지 않는다.**

---

## Reverse Structuring (역방향)

**적용 조건**: 입력이 결론부터 제시하고 근거가 부족할 때 ("~해야 한다", "결론적으로" 등).

**프로세스**:
1. 결론 식별 (`identifiedConclusion`)
2. 역추적 질문 생성 (`retroQuestions`)
3. 근거 매핑 → 행동 도출
4. Step 3으로 전달하여 정방향 피라미드 재구성
5. `structuringPath = "reverse"` 기록

---

## 품질 원칙 (요약)

- `typeRationale`은 형식적이지 않고 **구체적으로** 작성.
- 상위 레벨일수록 MECE 엄격.
- 가정·한계·투명성 명시.
- 결과는 실제 의사결정에 도움이 되어야 함.

---

## Reference (더 자세한 내용)

- 전체 품질 기준: `reference/minto-thinking-quality-rubric.md`
- Gold Standard 예시: `reference/minto-thinking-gold-examples.md`
- 상세 Edge Cases: 본 thinking.md 또는 gold-examples 참조

**복잡한 입력일 경우 반드시 위 파일들을 Read하여 참고할 것.**

---

## Constraints (공통)

- 피라미드 최소 2레벨.
- Level 1: 2~5개.

## Downstream Consumption 가이드 (writing / solve / express 용)

thinking 결과가 downstream 에이전트(writing, problem-solving, expression)에서 재사용될 때를 대비해:

- 출력 마지막에 `## Pyramid Data (for downstream agents)` 블록을 포함하는 것을 권장한다.
- coreClaim은 항상 정확히 1문장으로 유지 (downstream이 그대로 사용하기 때문).
- Level 1 Arguments는 명확한 제목 + 1~3줄 bullet으로 작성.
- MECE 검증 결과와 Critique Pass 요약은 downstream이 품질 판단에 활용할 수 있게 간결히 기록.
- writing은 Level 1을 SCQA Answer의 근거로 직접 매핑할 가능성이 높음.
- problem-solving은 Level 1을 "가설 생성의 축" 또는 "문제 구조"로 참고 (직접 이식 아님).

Orchestrator는 관련 prior thinking이 있으면 Read를 통해 이 내용을 자동 주입한다.
- coreClaim: 정확히 1문장.
- MECE 검증 + Critique Pass 필수.
- 모호할 경우 가정을 명시.
- 사용자 입력 언어로 출력.