# Minto Thinking Agent — Prompt Architecture (Quality-First)

> **목적**: Quality Rubric을 달성하기 위한 최고 수준의 thinking.md 프롬프트 구조를 정의한다.
> **기반**: `reference/minto-thinking-quality-rubric.md`
> **버전**: 0.1 (2026-06-18)
> **전략**: Gold Standard Minto Reasoning Prompt

---

## 1. 품질 목표 (Derived from Rubric)

이 아키텍처는 다음 Rubric 목표를 달성하기 위해 설계된다:

- **Excellent** 수준 출력 비율을 높인다 (목표: 30%+)
- 모든 Non-negotiables를 거의 항상 준수한다
- 특히 다음 차원에서 높은 점수를 안정적으로 얻는다:
  - Pyramid Integrity
  - MECE Rigor (상위 레벨)
  - GPS Structuring Quality
  - Transparency & Usefulness
  - Logical Soundness

---

## 2. 핵심 아키텍처 원칙

| 원칙 | 설명 | Rubric 연결 |
|------|------|-------------|
| **1. Runtime Reality First** | 실제 실행 모델(Orchestrator → Markdown 반환)에 정확히 맞춘다 | 모든 품질의 기반 |
| **2. Forced Structured Reasoning** | 모델이 항상 CS → GPS → Pyramid → Critique 순으로 생각하게 강제 | CS, GPS, Pyramid, MECE, Logic 품질 |
| **3. Explicit Quality Gate** | Rubric의 Non-negotiables와 주요 기준을 마지막에 자가 검증하게 한다 | 모든 차원 |
| **4. High-Signal Examples** | 5~6개의 Gold Standard 예시만 사용 (품질이 낮은 예시는 넣지 않는다) | 전체 품질 |
| **5. Transparency by Default** | typeRationale, gaps, issues, assumptions 등을 형식적이 아닌 실질적으로 기록하게 한다 | Transparency & Usefulness |
| **6. Critique Pass 필수화** | Pyramid + MECE 이후 별도의 자기 공격 단계 | MECE Rigor, Logical Soundness, Pyramid Integrity |

---

## 3. Runtime & Output Contract (가장 중요한 수정)

> **2026-06-18 업데이트**: 경량화 작업으로 `thinking.md`가 154 lines로 축소되었으며, 공통 규칙은 `reference/minto-thinking-core-checklist.md`로 분리. 전문 에이전트들은 이 파일을 참조한다.

**2026-06 Phase 3 추가**: thinking 결과를 downstream (writing/solve/expression)에서 재사용하기 위한 아키텍처가 도입되었다.
- Orchestrator가 Read를 통해 prior thinking 파일을 로드하고 `## Previous Thinking Pyramid` 블록을 주입
- Specialists에 "Pyramid Consumption" / "Problem Framing" 규칙 추가
- 스킬 레벨 `use-prior` 옵션 지원
- 자세한 내용은 `docs/minto-thinking-reuse-roadmap.md` 및 `docs/minto-thinking-reuse-examples.md` 참조

### 3.1 실제 실행 모델

- Orchestrator는 Agent를 호출하고 **Markdown 형식**의 결과를 받는다.
- 결과는 `minto-docs/01-thinking/YYYYMMDD-{kebab-title}.md` 에 저장된다.
- Frontmatter (tags, created, type, coreClaim)를 포함해야 한다.
- 내부 JSON(ThinkingResult v2)은 **파일로 Write하지 않는다**. reasoning 과정에서만 구조화된 형태로 사용.

### 3.2 Prompt 내 Contract 명시

프롬프트 최상단에 다음과 같이 명확히 선언:

```markdown
## Output Contract (반드시 준수)

이 에이전트의 **유일한 반환값**은 다음과 같다:

1. Obsidian 호환 Frontmatter (필수)
2. 사용자에게 보여줄 Markdown 구조 (아래 "User-Facing Output Format" 참조)

절대 다음을 해서는 안 된다:
- `/output/` 경로에 파일 Write 시도
- ThinkingResult JSON을 별도 파일로 저장하려 시도
- Orchestrator가 기대하지 않는 형식으로 응답
```

### 3.3 /minto-think 사용자 출력 정책

**기본 정책 (강력 추천)**:
- `/minto-think` 호출 시 **CS 분석 + GPS 구조화 섹션을 기본 포함**한다.
- 이유: 이 기능의 본질이 "사고 과정"이며, 사용자가 CS/GPS를 보는 것이 큰 가치다.
- writing/solve/expression 내장 사용 시에는 더 압축된 형태로 사용 가능.

---

## 4. Prompt 전체 구조 (권장)

```
---
name, model, description, tools
---

## Role (간결 + 품질 철학 포함)

## Output Contract (위 3.2 참조)

## Quality Rubric Summary (핵심 Non-negotiables + 주요 기준만 발췌)

## 6-Step Structured Reasoning Process
  - 각 Step마다:
    - 목적
    - 상세 프로세스
    - 강제 기록 항목
    - Step-end Self-Check (1~3개 질문)

## Mandatory Critique Pass (Pyramid + MECE 이후)

## Reverse Structuring (독립 섹션)

## Gold Standard Examples (5~6개, 매우 상세)
  - 각 예시: Input → Reasoning Trace 요약 → Full User Markdown

## User-Facing Output Format (정확한 Markdown 템플릿)

## Edge Cases & Anti-Patterns

## Constraints & Language
```

---

## 5. 각 Rubric 차원을 어떻게 강화할 것인가

| Rubric 차원 | Prompt 내 강화 방법 |
|-------------|---------------------|
| CS Analysis Quality | Step 1에 "추상화 레벨 확인" + "중요 관점 누락 탐지" 명시적 지시. inferredCategories 강제 고려 |
| GPS Structuring Quality | typeRationale을 "반드시 1문장 이상 구체적으로" 기록하게 강제. Parallel/Series 생성 여부 판단 기준을 테이블로 명시 |
| Pyramid Integrity | coreClaim = "정확히 1문장" 반복 강조. Level 1은 3~5개로 제한하고, "상위가 하위 요약" 규칙을 엄격히 |
| MECE Rigor | Critique Pass에서 "Level 1 MECE 자가 점검"을 별도 단계로 실행. strictLevels / relaxedAfter 명시 |
| Logical Soundness | Critique Pass에서 "반론 최강 포인트", "인과 비약 여부"를 반드시 검토하게 함 |
| Reverse Structuring Fidelity | 전용 섹션 + 전용 예시. identifiedConclusion, retroQuestions, evidenceMapping, derivedActions을 모두 기록 |
| Transparency & Usefulness | 모든 판단에 rationale 강제. 마지막에 "이 결과가 실제로 도움이 되는가?" 자문 |

---

## 6. Critique Pass 상세 설계 (신규)

Pyramid Building + MECE Validation이 끝난 후 **반드시** 실행:

1. **MECE 자가 점검** (Level 1 집중)
2. **논리 지지 점검** (각 Level 1이 coreClaim을 실제로 지지하는가?)
3. **반론 탐지** ("이 주장을 가장 강력하게 반박할 수 있는 근거는?")
4. **누락 관점 탐지** ("이 상황에서 가장 중요한데 아직 다루지 않은 축은?")
5. **개선 결정**:
   - 심각한 문제 → Step 3으로 돌아가 재구성 (최대 1회 추가)
   - 경미한 문제 → issues에 기록하고 진행

이 과정을 거치지 않으면 최종 출력을 생성하지 못하게 지시.

---

## 7. 예시 전략 (Gold Standard Only)

- **개수**: 정확히 5~6개
- **구성** (권장):
  1. 단순 정방향 (기본 CS/GPS/Pyramid)
  2. 복잡 정방향 (parallels + series + branch 혼합, 실전적 난이도)
  3. 역방향 구조화 (상세한 retroQuestions → 재구성 과정 포함)
  4. MECE 재구성 유발 사례 (초기 MECE 실패 → Critique 후 개선)
  5. 모호/짧은 입력 엣지 케이스
  6. (선택) 실제 프로젝트에서 나온 고난이도 사례

각 예시에는 다음을 포함:
- 사용자 입력
- 주요 판단 포인트 (특히 typeRationale, 핵심 MECE 결정)
- 완성된 User-Facing Markdown (실제 출력과 동일 형식)

나쁜 예시는 절대 넣지 않는다.

---

## 8. 주요 개선 포인트 (기존 대비)

1. **Tool Usage Guidelines 완전 재작성** — 현실과 일치시킴
2. **Critique Pass 신설** — Rubric의 MECE + Logic 차원을 직접 강화
3. **Non-negotiables를 prompt 최상위에 노출** — 모델이 항상 인지
4. **CS/GPS 사용자 노출 정책 명확화**
5. **예시를 대폭 줄이고 품질을 극대화**
6. **각 단계에 Self-Check 질문 강제**

---

## 9. 구현 로드맵 (제안)

| 단계 | 작업 | 산출물 |
|------|------|--------|
| 1 | 이 Architecture 확정 | 본 문서 승인 |
| 2 | Quality Rubric 최종 확정 (필요 시 수정) | minto-thinking-quality-rubric.md v0.2 |
| 3 | Gold 예시 5~6개 제작 | 별도 예시 문서 또는 thinking.md 내부 |
| 4 | thinking.md v3 초안 작성 | `.claude/agents/thinking.md` (또는 thinking-v3.md) |
| 5 | 실제 출력 3~5개로 품질 검증 | Rubric 기반 평가 결과 |
| 6 | 1회 이상 Iteration (필요 시) | 최종 thinking.md |

---

## 10. 의사결정 필요 항목 (지금 결정 필요)

1. **CS/GPS를 /minto-think에서 기본 노출**할 것인가? (현재 추천: 예)
2. **Critique Pass에서 재구성 최대 횟수**를 1회로 할 것인가, 2회로 할 것인가?
3. **예시 개수**를 5개로 할 것인가, 6개로 할 것인가?
4. **rawIdeas 필드**를 유지할 것인가? (현재는 거의 무용지물로 보임 — 제거 추천)

---

이 문서가 승인되면, 즉시 Gold 예시 제작 또는 thinking.md v3 작성으로 진행할 수 있습니다.

**사용자 액션**: 
- 이 Architecture에 동의하는가?
- 위 "의사결정 필요 항목"에 대한 의견은?
- 바로 다음으로 무엇을 할지 (예시 제작 vs 프롬프트 작성) 지정.