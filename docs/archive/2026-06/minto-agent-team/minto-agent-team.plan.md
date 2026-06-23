# Minto 논리 기반 에이전트 팀 구축 — 계획 문서

## Executive Summary

| 관점 | 내용 |
|------|------|
| **문제** | 개인의 산재된 아이디어를 논리적으로 정리하고, 이를 다양한 형태(문서/솔루션/발표)로 표현하는 과정이 비효율적 |
| **솔루션** | Minto의 피라미드 원칙을 핵심 엔진으로 하는 5-에이전트 팀 구축 |
| **기능** | 생각 정리 → 피라미드 구성 → 다목적 출력 (보고서/솔루션/슬라이드/메모) |
| **핵심가치** | 논리적 사고 체계화 + 문서 품질 향상 + 문제 해결 능력 강화 |

---

## 1. 사용자 의도 발견 (Intent Discovery)

### Q1: 핵심 목표
**개인용 사고 도우미**
- 사용자 스스로 논리적 사고를 체계화하고 정리하는 도구

### Q2: 주요 사용자
**스스로 쓰는 개인**
- 단독으로 의사결정, 분석, 문서 작성을 수행하는 개인 전문가

### Q3: 성공 기준
1. **사고 정리 능력** - 산재한 아이디어를 체계적으로 구조화
2. **문서 생성 품질** - 논리적이고 설득력 있는 문서 자동 생성
3. **문제 해결 능력** - 복잡한 문제를 단계적으로 분석하고 해결

### Q4: 기술적 제약
**Claude Code 런타임 제약:**
- 서브에이전트 컨텍스트 윈도우 크기 제한 (대량 입력 시 잘림 가능)
- Agent 도구 중첩 호출 깊이 제한 (Orchestrator → Thinking → Specialist 3단계가 실질적 한계)
- 에이전트 간 데이터 전달은 프롬프트 인라인 또는 파일 I/O만 가능 (메모리 공유 불가)

---

## 2. 대안 탐색 (Alternatives Explored)

### Approach A: 5-에이전트 계층 구조 ✓ **선택됨**

**Orchestrator → Thinking Agent (공통 기반) → Writing/Problem Solving/Expression Agents**

**장점:**
- Minto 책의 3부 구조 정확히 반영
- 각 에이전트 전문화로 품질 관리 용이
- 재사용성 높음 (Thinking Agent 공통 활용)
- 확장성 우수 (새 에이전트 추가 용이)

**단점:**
- 에이전트 간 조율 복잡도 증가
- 초기 구현 복잡도 높음

**선택 이유:**
장기적 확장성과 품질 중심의 구조로, 개인용 도구에서 조직용으로 확대 가능한 기초 마련

---

### Approach B: 모놀리식 단일 에이전트

**통합 Minto Logic Agent**

**장점:** 빠른 구현, 간단한 컨텍스트 관리

**단점:** 기능 혼재, 품질 관리 어려움, 재사용성 낮음

**불선택 사유:** 개별 기능 최적화 불가능

---

### Approach C: 플러그인/모듈 기반

**Core Thinking Engine + 플러그인 시스템**

**장점:** 모듈화, 선택적 기능 로딩

**단점:** 플러그인 통신 오버헤드, 복잡한 초기 설계

**불선택 사유:** 현재 프로젝트 규모에 과도한 복잡도

---

## 3. YAGNI 검토 (YAGNI Review)

### 포함된 항목 ✓

#### Logic in Thinking Agent (필수)
- ✓ 생각 정리 (산재한 아이디어 구조화)
- ✓ 피라미드 구성 (핵심→근거 트리 만들기)
- ✓ 요약 및 핵심 추출

#### Logic in Writing Agent (필수)
- ✓ SCQA 구조 (설득력 있는 글쓰기 기초)
- ✓ 보고서 생성 (구조화된 문서 작성)
- ✓ 메모/요약 (빠른 문서 생성)
- ✓ 사용자 정의 보고서 양식 지원

#### Logic in Problem Solving Agent (필수)
- ✓ 가설 설정 (가능한 원인 리스트)
- ✓ 분석 엔진 (적실한 데이터 분석)
- ✓ 해결안 도출 (실행 가능한 솔루션)

#### Logic in Expression Agent (필수)
- ✓ 발표 슬라이드 (PowerPoint 기초)
- ✓ 메모 노트 (빠른 정리)
- ✓ 스토리텔링 (설득력 강화)

#### Orchestrator Agent (필수)
- ✓ 사용자 의도 분류
- ✓ 적절한 에이전트 라우팅
- ✓ 컨텍스트 관리

### 제외된 항목 (Out of Scope - v2에서 추가)

- 팀 협업 기능 (다중 사용자, 권한 관리)
- 실시간 협업 편집
- 버전 관리 시스템
- 프로젝트 관리 통합
- 커맨드 없는 자유 입력 LLM 판단 라우팅 (오분류 위험, 명시적 커맨드로 충분)
- Markdown → PDF / PPT 변환 (외부 도구 의존, Marp CLI로 사용자가 별도 처리)

---

## 4. 설계 (Design)

### 4.0 구현 스택 결정

| 항목 | 결정 | 근거 |
|------|------|------|
| **구현 방식** | Claude Code 스킬 (.claude/agents/) | 별도 서버 없이 Claude Code 내에서 즉시 실행 |
| **에이전트 형식** | Markdown 기반 서브에이전트 (YAML Frontmatter) | bkit 표준, Claude Code Agent 도구 활용 |
| **언어** | 없음 (프롬프트 기반) | 코드 작성 없이 Claude가 직접 실행 |
| **컨텍스트 저장** | `.minto-memory.json` (JSON, 프로젝트 루트) | 세션 간 상태 유지 |
| **출력 포맷** | Markdown 우선, Obsidian 호환 Frontmatter 포함 | 추가 변환 도구 불필요 |
| **PPT 변환** | Marp 호환 Markdown (슬라이드 구조) | `---` 구분자 기반, 별도 변환 도구 없이 Marp CLI로 처리 가능 |
| **스킬 트리거** | `/think`, `/write`, `/solve`, `/express` 명시적 커맨드 | 의도 분류 오차 최소화 |

---

### 4.1 아키텍처 (5-Layer 구조)

```
PRESENTATION LAYER
├── Skills: /think, /write, /solve, /express
├── Triggers: Frontmatter 키워드 감지
└── Hooks: SessionStart, PreToolUse, PostToolUse

APPLICATION LAYER
└── Orchestrator Agent
    ├── Intent Classifier
    ├── Router
    ├── Context Manager
    └── Error Handler

DOMAIN LAYER (공통 기반)
└── Logic in Thinking Agent (Minto 엔진)
    ├── Idea Structurer
    ├── Pyramid Builder
    ├── MECE Validator
    ├── Logic Analyzer
    └── Summary Generator

SPECIALIST LAYER (전문 에이전트)
├── Writing Agent
│   ├── SCQA Composer
│   ├── Template Engine
│   ├── Document Generator
│   └── Format Converter
├── Problem Solving Agent
│   ├── Hypothesis Generator
│   ├── Analysis Engine
│   ├── Root Cause Analyzer
│   └── Solution Proposer
└── Expression Agent
    ├── Slide Generator
    ├── Memo Formatter
    └── Storytelling Engine

OUTPUT LAYER
├── Template System
│   ├── User-defined Report Template
│   ├── Slide Template (Marp 호환 Markdown)
│   ├── Memo Template
│   └── Analysis Template
└── Formatter
    └── Markdown → Obsidian (Frontmatter + Tag)
```

### 4.2 데이터 흐름 (6단계)

**INPUT** → **ORCHESTRATION** → **THINKING** → **SPECIALIZED** → **OUTPUT** → **PERSISTENCE**

1. **INPUT PHASE**: 사용자 입력 수신 (`/think`, `/write`, `/solve`, `/express` + 자유 텍스트)
2. **ORCHESTRATION PHASE**: Orchestrator가 커맨드 분석 → 대상 에이전트 결정 → ThinkingContext 초기화
3. **THINKING PHASE**: Thinking Agent 실행 → `ThinkingResult` 생성
4. **SPECIALIZED PHASE**: ThinkingResult를 입력으로 전달받아 전문 에이전트 실행
5. **OUTPUT PHASE**: Template Rendering → 포맷 변환 (Markdown / Obsidian / Marp)
6. **CONTEXT PERSISTENCE**: `.minto-memory.json` 업데이트 + `docs/` 파일 저장

#### 에이전트 간 전달 데이터 포맷

```json
// ThinkingResult — Thinking Agent → 전문 에이전트로 전달되는 표준 구조
{
  "coreClaim": "핵심 주장 1문장",
  "pyramid": [
    {
      "level": 1,
      "statement": "주요 근거",
      "supports": ["세부 근거 A", "세부 근거 B"]
    }
  ],
  "meceCheck": { "passed": true, "issues": [] },
  "summary": "전체 요약 3줄 이내",
  "rawIdeas": ["원본 아이디어 목록"]
}
```

#### ThinkingResult 전달 메커니즘

**방식: 프롬프트 인라인 전달**

Orchestrator(또는 스킬)가 Thinking Agent를 호출한 뒤, 반환된 `ThinkingResult` JSON을 다음 전문 에이전트 호출 시 프롬프트에 직접 포함하여 전달한다.

```
[스킬/Orchestrator]
  → Agent 도구로 Thinking Agent 호출 → ThinkingResult JSON 반환
  → Agent 도구로 Specialist Agent 호출 (프롬프트에 ThinkingResult 포함)
  → 최종 출력 Markdown
```

- **장점**: 별도 파일 불필요, 단순하고 빠름
- **제약**: 컨텍스트 윈도우 내에서 처리 가능한 크기여야 함
- **대안**: 결과가 너무 클 경우 `docs/` 하위에 파일로 저장 후 경로만 전달 (fallback)

#### Orchestrator 라우팅 기준

| 커맨드 / 조건 | 라우팅 대상 |
|--------------|-----------|
| `/think` 또는 "정리해줘", "구조화해줘" | Thinking Agent (단독 출력) |
| `/write` 또는 "보고서", "문서", "작성" | Writing Agent |
| `/solve` 또는 "문제", "원인", "해결" | Problem Solving Agent |
| `/express` 또는 "발표", "슬라이드", "메모" | Expression Agent |
| 커맨드 없는 자유 입력 | *(v2 예정 — v1에서는 커맨드 필수, 미지정 시 안내 메시지 출력)* |

**Obsidian 포맷 지원 (v1.4~):**
- Frontmatter (메타데이터: tags, created, related)
- Tag 시스템 (#thinking #writing #solving)
- Wikilink / Backlink (v2 예정)

### 4.3 주요 컴포넌트

#### Orchestrator Agent
- 역할: 사용자 의도 파악 → 적절한 에이전트로 라우팅
- 입력: 사용자 명령 + 컨텍스트
- 출력: 대상 에이전트 + 준비된 컨텍스트

#### Logic in Thinking Agent (핵심 엔진)
- 역할: Minto 피라미드 원칙 적용
- 출력: `ThinkingResult` JSON (4.2 포맷 참조)
- 5단계 처리 로직:
  1. **Idea Structuring**: 입력 텍스트에서 아이디어 추출 → 주제별 그룹핑 (귀납적 or 연역적 판단)
  2. **Pyramid Building**: 최상위 핵심 주장(SCQ Answer) 도출 → 하위 근거 계층 구성
  3. **MECE Validation**: 각 레벨에서 Mutually Exclusive(중복) / Collectively Exhaustive(누락) 검증
  4. **Logic Analysis**: 인과관계 일관성 확인, 근거가 주장을 실제로 지지하는지 검토
  5. **Summary Generation**: coreClaim 1문장 + 3줄 요약 생성
- Minto 원칙 적용 기준:
  - 귀납형: 여러 사실 → 공통 결론 (데이터 분석, 현상 정리)
  - 연역형: 전제 → 논리적 결론 (주장 설득, 권고안 제시)

#### Writing Agent
- SCQA 구조: Situation → Complication → Question → Answer
- 보고서 양식: 사용자 정의 템플릿 지원
- 출력: Markdown 형식 문서

#### Problem Solving Agent
- 가설 생성 → 분석 → 해결안 도출
- Root Cause Analysis 포함
- 구현 단계별 액션 플랜 제시

#### Expression Agent
- 발표 슬라이드: PowerPoint 구조
- 메모 노트: 빠른 정리 형식
- 스토리텔링: 설득력 있는 내러티브

### 4.4 메모리 및 컨텍스트

**세션 메모리 (.minto-memory.json):**
```json
{
  "previousThoughts": [],
  "previousAnalysis": [],
  "previousDocuments": [],
  "maxItemsPerArray": 20,
  "userPreferences": {
    "reportTemplate": "...",
    "preferredFormat": "obsidian"
  }
}
```

**영구 저장소 (docs/):**
```
docs/
├── 01-thinking/      (생각 정리 결과)
├── 02-writing/       (생성된 문서)
├── 03-solving/       (문제 분석 및 솔루션)
└── 04-expressing/    (발표 자료 및 메모)
```

---

## 5. 구현 단계 (Phased Implementation)

### Phase 1: 기초 구축 (v1.0)
- Logic in Thinking Agent 구현 (`.claude/agents/thinking.md`)
- `/think` 커맨드 스킬 등록 (`.claude/skills/think.md`)
- `.minto-memory.json` 초기 구조 생성
- **완료 기준**: `/think "아이디어 텍스트"` 실행 시 ThinkingResult JSON + 피라미드 구조 Markdown 출력 확인

### Phase 2: 라이팅 + Orchestrator 도입 (v1.1)
- Orchestrator Agent 구현 (`.claude/agents/orchestrator.md`) — 에이전트 2개 이상이 되는 시점에 도입
- Logic in Writing Agent 구현 (`.claude/agents/writing.md`)
- SCQA 구조 프롬프트 정의
- 기본 보고서 템플릿 1종 (`docs/templates/report-default.md`)
- `/write` 스킬 등록
- **완료 기준**: ThinkingResult → SCQA 기반 보고서 Markdown 자동 생성 확인

### Phase 3: 문제 해결 (v1.2)
- Logic in Problem Solving Agent 구현 (`.claude/agents/problem-solving.md`)
- 가설 생성 → Root Cause Analysis → 액션 플랜 프롬프트 정의
- `/solve` 스킬 등록
- **완료 기준**: 문제 설명 입력 → 가설 3개 이상 + 우선순위 해결안 출력 확인

### Phase 4: 표현 (v1.3)
- Logic in Expression Agent 구현 (`.claude/agents/expression.md`)
- Marp 호환 슬라이드 구조 템플릿, 메모 템플릿
- `/express` 스킬 등록
- **완료 기준**: ThinkingResult → Marp 슬라이드 Markdown + 요약 메모 출력 확인

### Phase 5: Obsidian 통합 (v1.4)
- 모든 출력에 Frontmatter + Tag 자동 삽입 로직 추가
- `docs/` 저장 경로 구조 확정 및 자동 저장 구현
- **완료 기준**: 생성 문서에 `tags`, `created`, `related` Frontmatter 포함 확인

### Phase 6: 최적화 및 문서화 (v2.0)
- 사용자 가이드 (`README.md`) 작성
- 템플릿 라이브러리 추가 (보고서 2종, 메모 1종)
- `.minto-memory.json` 컨텍스트 요약 압축 로직
- 커맨드 없는 자유 입력 LLM 라우팅
- Wikilink / Backlink 자동 생성
- **완료 기준**: 신규 사용자가 README만 보고 `/think` ~ `/express` 전체 흐름 실행 성공

---

## 6. 성공 지표

| 기준 | 측정 방법 | 목표 |
|------|---------|------|
| **논리 정확성** | MECE 검증 결과 `meceCheck.passed: true` 비율 (수동 10회 테스트) | >90% |
| **문서 생성 속도** | `/think` ~ 최종 출력까지 Claude Code 응답 완료 시간 (수동 측정) | <2분 |
| **사용자 만족도** | 생성 결과 즉시 사용 가능 비율 (재작성 없이 사용한 케이스 / 전체) | >80% |
| **Obsidian 호환성** | Phase 5 이후 생성 Markdown을 Obsidian에서 오류 없이 열리는 파일 비율 | 100% |

---

## 7. 위험 및 완화 방안

| 위험 | 영향 | 완화 방안 |
|------|------|---------|
| 에이전트 간 통신 복잡도 | 높음 | 명확한 인터페이스 정의, 자동화 테스트 |
| 사용자 템플릿 관리 | 중간 | 기본 템플릿 제공, 템플릿 검증 로직 |
| 컨텍스트 크기 증가 | 중간 | 압축 및 요약 메커니즘 구현 |
| Minto 원칙 오류 해석 | 높음 | 상세 가이드 문서, 검증 체크리스트 |

---

## 8. 다음 단계

**Plan Plus 완료 → Design 단계 진행**

```bash
/pdca design minto-agent-team
```

---

**작성일:** 2026-06-17
**상태:** Plan 완료
