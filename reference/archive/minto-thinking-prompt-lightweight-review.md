# Minto Thinking Agent 프롬프트 경량화 검토 보고서

> **검토 대상**: `.claude/agents/thinking.md` (285 lines)  
> **검토 일자**: 2026-06-18  
> **작성 목적**: 과도한 프롬프트 길이로 인한 할루시네이션 위험 분석 및 경량화 방안 제안

---

## 1. 현재 상태 요약

| 파일 | 라인 수 | 비고 |
|------|---------|------|
| `thinking.md` | **285** | 핵심 사고 엔진 (v3) |
| `problem-solving.md` | 191 | thinking 로직 내장 |
| `expression.md` | 207 | thinking 로직 내장 |
| `writing.md` | 139 | thinking 로직 내장 (가장 간결) |
| `orchestrator.md` | 150 | 라우팅 전용 |
| `reference/minto-thinking-gold-examples.md` | 449 | 예시 전용 (매우 큼) |
| `reference/minto-thinking-quality-rubric.md` | 204 | 품질 기준 |
| `reference/thinking_revised.md` (구버전) | 447 | v2 (JSON 출력 계약 포함) |

**thinking.md**가 에이전트 정의 중 **압도적으로 가장 길다**.

---

## 2. 주요 문제점 (할루시네이션 리스크 관점)

### 2.1 길이 자체의 문제
- 285라인은 프롬프트 토큰으로 환산 시 상당한 부담.
- LLM은 컨텍스트 후반부 지시를 상대적으로 덜 주목하는 경향이 있음.
- 특히 **Mandatory Critique Pass** (132~144라인), **Reverse Structuring**, **Edge Cases** 등 중요한 후반부 규칙이 무시될 가능성 높음.

### 2.2 중복 설명의 문제
4개 전문 에이전트가 모두 "Integrated Thinking Process" 또는 6-Step 설명을 **부분 중복**하고 있음:
- thinking.md: 상세 6-Step + Self-Check + Critique Pass
- writing/problem-solving/expression: "Phase 1: Minto 사고 정리 (6-Step 내부 실행)" 요약 버전

이로 인해:
- 한 곳 수정 시 다른 곳이 동기화되지 않음 (divergence).
- 전체 토큰 소비 증가.

### 2.3 예시 전략의 양면성
- 현재 thinking.md 내부에 5개 예시 요약이 포함되어 있음.
- 완전 예시는 `reference/minto-thinking-gold-examples.md` (449 lines)로 외부화 **시도**했으나, 여전히 inline 요약이 많음.
- 너무 많은 예시는 모델이 "예시 따라하기"에 치중하게 만들어 일반화 능력을 저하시킬 수 있음 (overfitting to examples).

### 2.4 출력 계약의 역사적 흔적
- v2 (`thinking_revised.md`)는 `ThinkingResult JSON v2`를 `/output/`에 Write하는 계약이었음.
- v3에서는 Markdown 직접 반환으로 변경되었으나, 여전히 상세한 내부 구조 설명(CS/GPS 필드 등)이 많이 남아 있음.
- 실제 실행 모델(Orchestrator가 Markdown을 받아 저장)과 프롬프트 설명 간 미세한 괴리가 남아있을 수 있음.

### 2.5 Read 도구 미활용
- 모든 전문 에이전트가 `tools: Read, Write`를 가지고 있음.
- 그럼에도 불구하고 대형 보조 문서(reference/)를 **런타임에 읽어서 참고**하는 지시가 약함.
- "상세 예시는 참조"라는 한 줄만 있고, **언제 어떻게 Read할지**에 대한 명시적 전략이 부족.

---

## 3. 경량화 전략 추천 (우선순위 순)

### 전략 A: Core + Reference 패턴 (가장 추천)

**목표**: `thinking.md`를 **110~140라인** 수준으로 축소.

**구체 내용**:
1. thinking.md에 유지할 것 (필수):
   - Frontmatter + Role + Quality 철학 (간결)
   - **Output Contract** (강조)
   - **Non-negotiables** (최상단 노출)
   - 6-Step Structured Reasoning **skeleton** + 핵심 Self-Check (상세 prose 축소)
   - Mandatory Critique Pass (간결 버전 유지)
   - Reverse Structuring (조건 + 1문단 프로세스)
   - Constraints + Language

2. reference/로 완전 이동 또는 대폭 축소:
   - 상세 Gold Examples (1~2개 최소 요약만 남기고 나머지 제거)
   - 상세 Edge Cases 테이블 (간소화 또는 별도 파일)
   - Step별 상세 rationale 설명

3. **Read 도구 적극 활용 지시 추가**:
   ```
   복잡하거나 판단이 어려운 입력의 경우, 반드시 다음을 Read하여 참고할 것:
   - reference/minto-thinking-gold-examples.md
   - reference/minto-thinking-quality-rubric.md
   ```

### 전략 B: 공통 Core Rules 추출 (중요)

새 파일 생성: `reference/minto-thinking-core-checklist.md` (목표 40~60라인)

- writing, problem-solving, expression이 **이 파일을 Read하거나 최소 인라인**할 수 있도록 함.
- 현재 중복되어 있는 "Phase 1: 6-Step" 설명을 이 파일로 통합.
- "내장 thinking 사용 시에는 이 core checklist를 따른다"는 한 문장으로 대체 가능.

### 전략 C: Specialist용 Lite 모드 구분

- thinking.md (직접 /minto-think 호출용) → 풀 버전 유지 (다만 축소)
- Specialist 내부 사용용 → "Minto 6-Step + Critique Pass를 내부 실행. 상세 규칙은 core-checklist를 따른다." 정도로 극단 축소.

Orchestrator가 specialist를 호출할 때 가벼운 버전을 주입하는 방법도 검토 가능.

### 전략 D: 형식 압축

- 긴 설명 → 테이블 + 번호 목록 위주로 전환.
- "목적 / 프로세스 / 강제 기록 / Self-Check" 구조를 더 압축된 표 형식으로.
- "왜 이 단계를 하는가" 설명은 reference 문서로 이동.

### 전략 E: 예시 개수 엄격 제한

아키텍처 문서(`minto-thinking-prompt-architecture.md`)에서 이미 권장:
> "High-Signal Examples: 5~6개의 Gold Standard 예시만 사용"

현재 gold-examples.md는 449라인으로, **프롬프트에 직접 넣는 것은 절대 금지**해야 함. 현재 상태는 양호하지만 inline 요약도 더 줄이는 것이 좋음.

---

## 4. 구체적 실행 계획 (제안)

### Phase 1: 준비 (reference 보강)
1. `reference/minto-thinking-core-checklist.md` 신규 작성 (간결 checklist 버전)
2. `reference/minto-thinking-edge-cases.md` 분리 (필요 시)
3. gold-examples.md는 현재 상태 유지 (너무 크니 직접 인라인 금지 명문화)

### Phase 2: thinking.md 대수술
- 목표 라인: **130라인 이하**
- Non-negotiables를 Role 바로 아래로 이동
- 6-Step 각 단계 설명을 3~5줄 이내로 압축 + Self-Check 유지
- 예시 섹션: 1개 최소 예시 + "나머지는 reference/... Read" 지시로 대체
- Edge Cases & Anti-Patterns: 표는 유지하되 설명 최소화
- "언제 Read를 사용할 것인가"에 대한 명시적 섹션 추가

### Phase 3: Specialist 동기화
- writing.md, problem-solving.md, expression.md의 "Integrated Thinking Process" 섹션을 **1~2문단 + core 참조**로 교체
- "thinking 로직을 내장"이라는 문구는 유지하되, 실제 로직 중복은 제거

### Phase 4: 검증
- 기존 `minto-docs/01-thinking/`의 결과물 3~5개에 대해 품질 루브릭으로 재평가
- Critique Pass 수행 여부, MECE 엄격도, coreClaim 1문장 준수 등을 확인
- 토큰 사용량(대략) 비교

### Phase 5: 문서화
- `minto-thinking-prompt-architecture.md` 업데이트 (경량화 적용 후)
- 이 검토 문서에 후속 조치 결과 기록

---

## 5. 예상 효과

| 항목 | 개선 전 | 개선 후 (목표) | 효과 |
|------|---------|----------------|------|
| thinking.md 길이 | 285 lines | 110~140 lines | 주의 집중도 ↑, 토큰 ↓ |
| Specialist 중복 | 3곳에 6-Step 설명 | 1곳(core) + 참조 | 유지보수성 ↑ |
| 예시 과다 노출 | inline 요약 다수 | 최소 + Read 지시 | hallucination(예시 따라하기) ↓ |
| Reference 활용 | 거의 없음 | Read 명시적 지시 | 동적 고품질 참고 가능 |
| Critique Pass 준수율 | 불확실 (후반부) | 높음 (상단 배치 + 축소) | 품질 안정화 |

---

## 6. 리스크 및 주의사항

- **과도한 축소 금지**: Non-negotiables, Critique Pass, coreClaim 1문장 규칙, MECE 로직트리 규칙은 **절대 희생하면 안 됨**.
- Read 실패 처리: 파일이 없을 때 fallback 동작을 명시해야 함.
- 기존 산출물과의 호환: output format (User-Facing Markdown)은 현재 구조를 최대한 유지.
- Orchestrator와의 계약: 최종 반환은 여전히 순수 Markdown이어야 함 (JSON Write 시도 금지).

---

## 7. 결론 및 추천 순서

**즉시 추천**:
1. `thinking.md`를 140라인 이하로 과감히 축소 (전략 A 중심)
2. `reference/minto-thinking-core-checklist.md` 작성 (전략 B)
3. Specialist 3개 파일의 thinking 설명 통일

이 작업만으로도 **할루시네이션 위험을 상당히 낮추고**, 품질 일관성을 높일 수 있다.

장기적으로는 "에이전트 정의도 모듈화"라는 관점에서, bkit/Claude Code의 Agent 호출 패턴과 결합해 더 정교한 로딩 전략을 고민할 가치가 있다.

---

## 부록: 제안 최종 파일 구조 (경량화 후)

```
.claude/
├── agents/
│   ├── thinking.md                 ← 110~140 lines (core contract + process skeleton)
│   ├── writing.md                  ← 축소된 Integrated Thinking 언급
│   ├── problem-solving.md
│   ├── expression.md
│   └── orchestrator.md
├── skills/...

reference/
├── minto-thinking-core-checklist.md   ← NEW (공통 최소 규칙)
├── minto-thinking-gold-examples.md    ← 유지 (Read 전용)
├── minto-thinking-quality-rubric.md   ← 유지
├── minto-thinking-prompt-architecture.md
├── minto-thinking-edge-cases.md       ← (선택) 분리
└── minto-thinking-prompt-lightweight-review.md  ← 본 문서
```

---

**검토자 의견**: 현재 아키텍처 자체는 잘 설계되어 있다. 문제는 "문서화의 성실함"이 "프롬프트의 적정 길이"를 넘어선 점이다. 이제는 **품질을 담는 그릇의 크기**를 현실에 맞게 줄일 때다.

---

## 실행 결과 (2026-06-18)

**경량화 작업 완료**

| 파일 | 변경 전 | 변경 후 | 감소 |
|------|---------|---------|------|
| `.claude/agents/thinking.md` | 285 lines | **154 lines** | -131 |
| `.claude/agents/writing.md` | 139 lines | 123 lines | -16 |
| `.claude/agents/problem-solving.md` | 191 lines | 177 lines | -14 |
| `.claude/agents/expression.md` | 207 lines | 205 lines | -2 |
| `reference/minto-thinking-core-checklist.md` | 신규 | 112 lines | - |

**주요 개선 사항**:
- thinking.md 대폭 압축 (6-Step을 스켈레톤으로 축약, 예시 최소화, Read 지시 강화)
- 3개 전문 에이전트의 "Integrated Thinking Process" 중복 제거 → 공통 `reference/minto-thinking-core-checklist.md` 참조로 통일
- Non-negotiables, Mandatory Critique Pass, `coreClaim` 1문장 규칙 **원본 그대로 유지**
- Output Contract와 User-Facing Output Format은 변경 없음
- 모든 에이전트에 reference 파일 Read 활용 명시 추가

**검증**:
- Non-negotiables 최상단 유지
- Critique Pass 강제 조항 유지
- Specialists 간 로직 중복 제거 완료

**다음 추천**:
- 실제 minto-docs/ 결과물 3~5개로 품질 재확인 (Rubric 기준)
- 필요 시 thinking.md를 한 번 더 130줄 이하로 미세 조정
