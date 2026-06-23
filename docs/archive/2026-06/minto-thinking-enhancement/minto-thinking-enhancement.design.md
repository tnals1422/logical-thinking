# Minto Thinking Enhancement Design Document

> **Summary**: Part 1(정보 정리 기초) CS/GPS 프레임워크를 Thinking 프로세스에 통합 — Clean Architecture (6-Step 전면 재구조화)
>
> **Project**: agent-creator (Minto Agent Team)
> **Version**: 0.1
> **Author**: 박수민
> **Date**: 2026-06-18
> **Status**: Draft
> **Planning Doc**: [minto-thinking-enhancement.plan.md](../01-plan/features/minto-thinking-enhancement.plan.md)

---

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 피라미드 구축 전 정보 정리 단계 부재로 인한 논리적 사고 품질 한계 |
| **WHO** | Minto 스킬 사용자 (개인 전문가) |
| **RISK** | 프로세스 단계 증가로 인한 응답 시간 증가 및 프롬프트 복잡도 상승 |
| **SUCCESS** | 6개 GAP 반영 완료, MECE 검증 통과율 유지(>90%), 응답 시간 <3분 |
| **SCOPE** | thinking.md → problem-solving.md → writing.md → expression.md 순차 개선 |

---

## 1. Overview

### 1.1 Design Goals

1. thinking.md를 5-Step에서 **6-Step 구조로 전면 재작성**하여 CS/GPS를 독립 단계로 확립
2. ThinkingResult JSON v2 스키마를 엄격하게 정의하여 하위 에이전트 간 구조 정보 전파 보장
3. 4개 에이전트 전체의 Thinking 로직을 6-Step으로 동기화
4. 역방향 구조화(질문→결론→행동) 경로를 별도 섹션으로 정의

### 1.2 Design Principles

- **Complete Restructuring**: 기존 Step 번호를 재배치하지 않고, 전체 프로세스를 v2로 새로 정의
- **Schema-First**: JSON v2 스키마를 먼저 확정하고, 각 Step이 스키마의 어떤 필드를 채우는지 명확히 매핑
- **Consistency**: 4개 에이전트의 내장 Thinking 로직이 동일한 6-Step 구조를 참조
- **Backward Compatibility**: v1 필드(coreClaim, pyramid, meceCheck, summary, rawIdeas)는 모두 유지

---

## 2. Architecture Options (v1.7.0)

### 2.0 Architecture Comparison

| Criteria | Option A: Minimal | Option B: Clean | Option C: Pragmatic |
|----------|:-:|:-:|:-:|
| **Approach** | 기존 Step에 인라인 추가 | 6-Step 전면 재구조화 | 새 Step 추가 + 번호 이동 |
| **변경량** | ~80줄 | ~350줄 | ~200줄 |
| **프롬프트 크기 증가** | ~15% | ~50% | ~30% |
| **CS/GPS 명확성** | 낮음 | 높음 | 높음 |
| **하위 호환성** | 완전 | v1 필드 유지 | 완전 (optional) |
| **유지보수성** | 낮음 | 높음 | 높음 |
| **구현 난이도** | 낮음 | 높음 | 중간 |

**Selected**: Option B: Clean — **Rationale**: CS/GPS 프레임워크가 Minto 사고의 기초 단계이므로, 독립된 명확한 구조로 정의해야 장기적 품질과 유지보수성이 보장됨. 프롬프트 크기 증가(~50%)는 NFR 한계에 근접하지만, 사고 품질 향상이라는 핵심 가치를 위해 수용.

### 2.1 Component Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    Skill Layer                           │
│  /minto-think  /minto-write  /minto-solve  /minto-express│
└────────┬───────────┬────────────┬────────────┬──────────┘
         │           │            │            │
         ▼           ▼            ▼            ▼
┌─────────────────────────────────────────────────────────┐
│                  Orchestrator Agent                       │
│              (routing + dispatch)                         │
└────────┬───────────┬────────────┬────────────┬──────────┘
         │           │            │            │
         ▼           ▼            ▼            ▼
┌──────────┐  ┌──────────┐  ┌───────────┐  ┌──────────┐
│ thinking │  │ writing  │  │ problem-  │  │expression│
│   .md    │  │   .md    │  │solving.md │  │   .md    │
│ (6-Step) │  │(6-Step   │  │(6-Step    │  │(6-Step   │
│  FULL    │  │ embedded)│  │ embedded) │  │ embedded)│
└──────────┘  └──────────┘  └───────────┘  └──────────┘
     │              │             │              │
     ▼              ▼             ▼              ▼
┌─────────────────────────────────────────────────────────┐
│              ThinkingResult JSON v2                       │
│  (coreClaim, pyramid, meceCheck, summary, rawIdeas,      │
│   csAnalysis, gpsStructure)                              │
└─────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────┐
│              .minto-memory.json                          │
│         (세션 간 컨텍스트 저장)                            │
└─────────────────────────────────────────────────────────┘
```

### 2.2 Data Flow

```
사용자 입력
  │
  ▼
Step 1: CS Analysis ─────────────────────┐
  │ (범주·요점 추출)                       │
  ▼                                       │
Step 2: GPS Structuring                   │  ThinkingResult
  │ (그룹/패러렐/시리즈)                    │  JSON v2 축적
  ▼                                       │
Step 3: Pyramid Building                  │
  │ (CS/GPS 결과 기반 피라미드 구축)        │
  ▼                                       │
Step 4: MECE Validation                   │
  │ (로직트리 규칙 강화)                    │
  ▼                                       │
Step 5: Logic Analysis                    │
  │ (인과관계 일관성)                       │
  ▼                                       │
Step 6: Summary Generation ──────────────┘
  │
  ▼
ThinkingResult JSON v2 (완성)
  │
  ├──→ 사용자 출력 (Markdown 피라미드)
  └──→ .minto-memory.json 저장

[역방향 경로] (결론 선행 시)
  결론 식별 → 질문 역추적 → 근거 구성 → 행동 도출
  → Step 3(Pyramid Building)으로 합류
```

### 2.3 Dependencies

| Component | Depends On | Purpose |
|-----------|-----------|---------|
| thinking.md | 없음 | 핵심 사고 엔진 (독립) |
| problem-solving.md | thinking.md 6-Step 구조 참조 | 내장 Thinking 로직 동기화 |
| writing.md | thinking.md 6-Step 구조 참조 | 내장 Thinking 로직 동기화 |
| expression.md | thinking.md 6-Step 구조 참조 | 내장 Thinking 로직 동기화 |
| .minto-memory.json | ThinkingResult JSON v2 | 확장된 스키마 저장 |

---

## 3. Data Model

### 3.1 ThinkingResult JSON v2 Schema

```typescript
// ThinkingResult JSON v2 — 전체 스키마
interface ThinkingResultV2 {
  // === v1 필드 (유지) ===
  coreClaim: string;              // 핵심 주장 1문장
  pyramid: PyramidNode[];         // 피라미드 구조
  meceCheck: MeceCheck;           // MECE 검증 결과
  summary: string;                // 전체 요약 3줄 이내
  rawIdeas: string[];             // 원본에서 추출한 아이디어 목록

  // === v2 신규 필드 ===
  csAnalysis: CSAnalysis;         // Step 1 결과
  gpsStructure: GPSStructure;     // Step 2 결과
  structuringPath: "forward" | "reverse";  // 구조화 경로
}

// Step 1: CS Analysis 결과
interface CSAnalysis {
  categories: Category[];
  abstractionLevels: number;      // 추상화 계층 수 (최소 2)
  inferredCategories: string[];   // 본문에서 추론·보충한 범주명
}

interface Category {
  name: string;                   // 범주 (항목)
  summaries: string[];            // 요점 (정리된 핵심 내용)
}

// Step 2: GPS Structuring 결과
interface GPSStructure {
  groups: Group[];
  parallels: Parallel[];
  series: Series[];
  diagram: string;                // ASCII 다이어그램 (텍스트)
}

interface Group {
  name: string;
  type: "branch" | "contain" | "hub";  // 분기형 / 포함형 / 중심형
  elements: string[];
  typeRationale: string;          // 유형 판단 근거
}

interface Parallel {
  axis: string;                   // 비교 축 (e.g., "문제-영향-해결")
  items: Record<string, string>[]; // 비교 항목들
}

interface Series {
  criterion: string;              // 순서 기준 (e.g., "발생 순서", "중요도")
  order: string[];
  gaps: string[];                 // 순서에서 발견된 누락 항목
}

// 기존 v1 타입 (변경 없음)
interface PyramidNode {
  level: number;
  statement: string;
  supports: string[];
}

interface MeceCheck {
  passed: boolean;
  issues: string[];
  // v2 추가
  logicTreeRules: LogicTreeRules;
}

// Step 4 Enhanced: 로직트리 규칙
interface LogicTreeRules {
  depth: number;                  // 현재 피라미드 깊이
  strictLevels: number;           // 엄격 MECE 적용 레벨 수 (기본: 2)
  relaxedAfter: number;           // 완화 시작 레벨 (기본: 3)
  singleParentViolations: string[];  // 부모 1개 규칙 위반 항목
}
```

### 3.2 역방향 구조화 데이터

```typescript
// 역방향 구조화 시 추가 데이터
interface ReverseStructuring {
  identifiedConclusion: string;   // 입력에서 식별된 결론
  retroQuestions: string[];       // 역추적된 질문들
  evidenceMapping: {              // 질문 → 근거 매핑
    question: string;
    evidence: string[];
  }[];
  derivedActions: string[];       // 도출된 행동/액션
}
```

### 3.3 인과관계 모델 확장 (problem-solving.md)

```typescript
// Phase 3 Root Cause Analysis 확장
interface CausalModel {
  // 기존
  fiveWhys: string[];
  rootCause: string;
  // v2 추가
  independentEvents: CausalEvent[];   // 독립 사건 (원인)
  dependentEvents: CausalEvent[];     // 종속 사건 (결과)
  predictions: Prediction[];          // 예측 활용
}

interface CausalEvent {
  event: string;
  type: "independent" | "dependent";
  relatedTo: string[];                // 관련 사건 ID
}

interface Prediction {
  if: string;                         // 독립 사건 관찰 조건
  then: string;                       // 예측되는 종속 사건
  confidence: "high" | "medium" | "low";
}
```

---

## 4. API Specification

> 본 프로젝트는 Starter 레벨 (프롬프트 기반 에이전트)이므로 REST API는 없음.
> 에이전트 간 인터페이스는 ThinkingResult JSON v2 스키마로 정의됨 (Section 3 참조).

### 4.1 에이전트 호출 인터페이스

| Caller | Callee | Input | Output |
|--------|--------|-------|--------|
| /minto-think | orchestrator.md | 사용자 텍스트 | 라우팅 결정 |
| orchestrator.md | thinking.md | 사용자 텍스트 | ThinkingResult v2 + Markdown |
| /minto-write | writing.md | 사용자 텍스트 + options | SCQA 문서 |
| /minto-solve | problem-solving.md | 문제 설명 | 분석 + 해결안 |
| /minto-express | expression.md | 사용자 텍스트 + format | 슬라이드/메모/스토리 |

### 4.2 인터페이스 변경 사항

| 인터페이스 | 변경 | 하위 호환 |
|-----------|------|----------|
| Skill → Orchestrator | 변경 없음 | 완전 호환 |
| Orchestrator → Agent | 변경 없음 (텍스트 전달) | 완전 호환 |
| Agent → 사용자 출력 | CS/GPS 섹션 추가 (Markdown) | 추가 출력 (기존 유지) |
| Agent → .minto-memory.json | csAnalysis, gpsStructure 필드 추가 | v1 필드 유지 |

---

## 5. UI/UX Design

> 본 프로젝트는 CLI 기반 에이전트이므로 UI 없음.
> 사용자 출력은 Markdown 형식이며, 아래는 출력 구조 변경 사항.

### 5.1 thinking.md 출력 구조 변경

**v1 출력 구조:**
```markdown
## 핵심 주장
## 피라미드 구조
## MECE 검증
## 요약
```

**v2 출력 구조:**
```markdown
## CS 분석 (범주·요점)
### 범주 1: {name}
- 요점: {summaries}
### 범주 2: {name}
- 요점: {summaries}
> 추론 보충 범주: {inferredCategories}

## GPS 구조화
### 그룹 구조
{ASCII 다이어그램}
### 비교 분석 (패러렐)
| {axis columns} |
### 순서 분석 (시리즈)
{순서 → 누락 탐지}

## 핵심 주장
> {coreClaim}

## 피라미드 구조
### 근거 1: {statement}
- {supports}

## MECE 검증
- 결과: {passed/failed}
- 로직트리 규칙: 엄격 1~2단계 / 완화 3단계+
- {issues}

## 요약
{summary}
```

### 5.2 GPS 다이어그램 출력 형식

**분기형 (Branch):**
```
문제 원인
├── 불분명한 절차
│   ├── 매뉴얼 불완전
│   └── 업데이트 미반영
├── 입력 실수
└── 오류 검출 부재
```

**포함형 (Contain):**
```
┌─────────────────────────────┐
│ 프로젝트 리스크               │
│  ┌───────────────────────┐  │
│  │ 기술 리스크              │  │
│  │  ┌─────────────────┐  │  │
│  │  │ 성능 리스크        │  │  │
│  │  └─────────────────┘  │  │
│  └───────────────────────┘  │
└─────────────────────────────┘
```

**패러렐 (비교표):**
```
| 문제          | 영향          | 해결            |
|--------------|--------------|----------------|
| 불분명한 절차  | 작업 혼란      | 매뉴얼 보완      |
| 입력 실수     | 공수 50% 증가  | 오류 검출 기능    |
```

**시리즈 (순서):**
```
절차 불명확 인식 → 입력 실수 발생 → 오류 검출 실패 → 공수 증가
                                    ↑
                              [누락 탐지: 재작업 단계]
```

---

## 6. Error Handling

> 에이전트 프롬프트 기반이므로 코드 수준 에러 핸들링은 없음.
> 아래는 에이전트 실행 중 발생 가능한 엣지 케이스와 처리 방식.

| 상황 | 처리 방식 |
|------|----------|
| 입력이 너무 짧아 CS 분석 불가 | categories를 1개로 생성, 가정을 명시 |
| GPS 그룹 유형 판단 불가 | "branch"를 기본값으로 사용, typeRationale에 "기본값 적용" 명시 |
| 패러렐 비교가 불가능한 경우 | parallels를 빈 배열로 반환, 건너뛴 이유를 summary에 포함 |
| 시리즈 순서 기준이 없는 경우 | series를 빈 배열로 반환 |
| 역방향 구조화 조건이 애매한 경우 | 정방향(forward) 기본 사용, structuringPath에 기록 |
| MECE 3단계 이후 완화 적용 | logicTreeRules에 relaxedAfter 기록, issues에 "완화 적용" 표시 |

---

## 7. Security Considerations

> 에이전트 프롬프트 프로젝트이므로 일반적 보안 항목은 해당 없음.

- [x] 민감 데이터 없음 (에이전트 프롬프트 + JSON 스키마만 변경)
- [x] 외부 API 호출 없음
- [x] 사용자 입력은 Claude가 처리 (별도 검증 불필요)
- [x] .minto-memory.json은 로컬 파일 (원격 전송 없음)

---

## 8. Test Plan

> 본 프로젝트는 프롬프트 기반이므로 자동화 테스트 대신 수동 검증으로 수행.

### 8.1 Test Scope

| Type | Target | Tool | Phase |
|------|--------|------|-------|
| M1: 기능 검증 | 각 Step 출력 정확성 | 수동 실행 + 출력 확인 | Do |
| M2: 하위 호환 검증 | v1 입력으로 동일 출력 확인 | 수동 실행 비교 | Do |
| M3: 통합 검증 | 4개 에이전트 연동 | /minto-* 스킬 실행 | Do |

### 8.2 M1: 기능 검증 시나리오

| # | 대상 | 테스트 입력 | 기대 결과 |
|---|------|-----------|----------|
| 1 | CS Analysis | "S 프로젝트 입력 작업" 예제 (원서 Part 1) | categories 3개 이상, summaries 각 1개 이상 |
| 2 | GPS - 분기형 | 계층적 문제 원인 입력 | type="branch", 트리 다이어그램 출력 |
| 3 | GPS - 패러렐 | 다차원 비교 가능한 입력 | parallels 비교표 출력, axis 명시 |
| 4 | GPS - 시리즈 | 순서가 있는 프로세스 입력 | series 순서 출력, gaps에 누락 탐지 |
| 5 | MECE 로직트리 | 4단계 깊이 피라미드 | 1~2단계 엄격, 3단계+ 완화 적용 |
| 6 | 역방향 구조화 | 결론이 먼저 나온 입력 | structuringPath="reverse", 질문→근거 역추적 |
| 7 | 인과관계 모델 | 독립/종속 사건 구분 가능한 문제 | independentEvents, dependentEvents 분리 |

### 8.3 M2: 하위 호환 검증 시나리오

| # | 대상 | 테스트 | 기대 결과 |
|---|------|-------|----------|
| 1 | ThinkingResult v2 | v1과 동일 입력 | coreClaim, pyramid, meceCheck, summary, rawIdeas 동일 |
| 2 | /minto-think | 기존 사용법 | 기존 출력 + CS/GPS 섹션 추가 |
| 3 | /minto-write | 기존 사용법 | 동일 SCQA 문서 생성 |
| 4 | /minto-solve | 기존 사용법 | 동일 분석 + 인과관계 프레임 추가 |
| 5 | /minto-express | 기존 사용법 | 동일 슬라이드/메모/스토리 생성 |

### 8.4 M3: 통합 검증 시나리오

| # | 시나리오 | 검증 항목 |
|---|---------|----------|
| 1 | /minto-think 실행 → .minto-memory.json 확인 | csAnalysis, gpsStructure 필드 포함 저장 |
| 2 | /minto-write 실행 → CS/GPS 참조 확인 | Phase 1에서 CS/GPS 단계 실행 흔적 |
| 3 | /minto-solve 실행 → 인과관계 모델 확인 | independentEvents/dependentEvents 출력 |
| 4 | /minto-express 실행 → CS/GPS 참조 확인 | Phase 1에서 CS/GPS 단계 실행 흔적 |

---

## 9. Clean Architecture

> Starter 레벨 프로젝트 — 에이전트 프롬프트와 JSON 스키마만 존재.

### 9.1 Layer Structure

| Layer | Responsibility | Location |
|-------|---------------|----------|
| **Skill** | 사용자 인터페이스 (호출 진입점) | `.claude/skills/` |
| **Orchestrator** | 라우팅 + 디스패치 | `.claude/agents/orchestrator.md` |
| **Agent** | 사고 로직 + 출력 생성 | `.claude/agents/{name}.md` |
| **Storage** | 세션 간 컨텍스트 | `.minto-memory.json` |

### 9.2 Dependency Rules

```
Skill ──→ Orchestrator ──→ Agent ──→ Storage
  │                          │
  └── 직접 호출 가능 ──────────┘
       (orchestrator bypass)

Rule: Agent는 다른 Agent를 호출하지 않는다 (자기 완결)
Rule: Skill은 Orchestrator 또는 Agent를 호출한다
Rule: Storage 형식(JSON v2)은 Agent가 정의한다
```

### 9.3 This Feature's Layer Assignment

| Component | Layer | Location | 변경 사항 |
|-----------|-------|----------|----------|
| thinking.md | Agent | `.claude/agents/thinking.md` | 6-Step 전면 재작성 |
| problem-solving.md | Agent | `.claude/agents/problem-solving.md` | Phase 3에 인과관계 모델 확장 |
| writing.md | Agent | `.claude/agents/writing.md` | Phase 1을 6-Step으로 동기화 |
| expression.md | Agent | `.claude/agents/expression.md` | Phase 1을 6-Step으로 동기화 |
| orchestrator.md | Orchestrator | `.claude/agents/orchestrator.md` | 변경 없음 |
| .minto-memory.json | Storage | 프로젝트 루트 | v2 필드 추가 저장 |

---

## 10. Coding Convention Reference

### 10.1 에이전트 프롬프트 작성 규칙

| Target | Rule | Example |
|--------|------|---------|
| Step 이름 | 영문 (한글 부제) | `Step 1: CS Analysis (범주·요점 추출)` |
| JSON 필드명 | camelCase | `csAnalysis`, `gpsStructure` |
| 유형 상수 | lowercase string literal | `"branch"`, `"contain"`, `"hub"` |
| 출력 섹션 헤더 | `## {한글 제목}` | `## CS 분석 (범주·요점)` |
| Frontmatter 필수 필드 | name, model, description, tools | 기존 유지 |

### 10.2 프롬프트 구조 규칙

| Item | Convention |
|------|-----------|
| Step 정의 | `### Step N: {Name} ({한글 부제})` 형식 |
| Step 내부 구조 | 목적 → 프로세스 → 출력 → 제약 |
| JSON 스키마 | TypeScript interface로 정의, 코드 블록 내 |
| 다이어그램 | ASCII art, 코드 블록 내 |
| 조건부 로직 | `**적용 조건:**` 키워드로 명시 |

---

## 11. Implementation Guide

### 11.1 File Structure

```
.claude/
├── agents/
│   ├── orchestrator.md     # 변경 없음
│   ├── thinking.md         # [Module 1] 6-Step 전면 재작성
│   ├── problem-solving.md  # [Module 2] Phase 3 인과관계 확장
│   ├── writing.md          # [Module 3] Phase 1 동기화
│   └── expression.md       # [Module 3] Phase 1 동기화
├── skills/                 # 변경 없음
└── ...
```

### 11.2 Implementation Order

1. [ ] **thinking.md 전면 재작성** — 6-Step v2 프로세스 + JSON v2 스키마 + 역방향 구조화 + Markdown 출력 형식
2. [ ] **problem-solving.md 확장** — Phase 3에 독립/종속 사건 인과관계 프레임 추가
3. [ ] **writing.md 동기화** — Phase 1 Thinking 로직을 6-Step으로 업데이트
4. [ ] **expression.md 동기화** — Phase 1 Thinking 로직을 6-Step으로 업데이트
5. [ ] **통합 검증** — /minto-think, /minto-write, /minto-solve, /minto-express 실행 테스트

### 11.3 Session Guide

#### Module Map

| Module | Scope Key | Description | Files | Estimated Turns |
|--------|-----------|-------------|-------|:---------------:|
| thinking.md 재작성 | `module-1` | 6-Step 프로세스, JSON v2, 역방향 구조화, MECE 규칙, 출력 형식 | thinking.md | 30-40 |
| problem-solving.md 확장 | `module-2` | 인과관계 모델에 독립/종속 프레임 추가 | problem-solving.md | 10-15 |
| writing.md + expression.md 동기화 | `module-3` | Phase 1을 6-Step으로 업데이트 | writing.md, expression.md | 15-20 |

#### Recommended Session Plan

| Session | Phase | Scope | Turns |
|---------|-------|-------|:-----:|
| Session 1 | Plan + Design | 전체 | 완료 |
| Session 2 | Do | `--scope module-1` | 30-40 |
| Session 3 | Do | `--scope module-2,module-3` | 25-35 |
| Session 4 | Check + Report | 전체 | 20-30 |

---

## Appendix A: thinking.md v2 상세 설계

### A.1 Step 1: CS Analysis (범주·요점 추출)

**목적**: 입력 텍스트에서 "무엇을 써야 하는가"를 결정한다.

**프로세스**:
1. 입력 텍스트를 읽고 정보 항목(범주, Category)을 식별한다
2. 각 범주 아래에 요점(Summary, 정리된 핵심 내용)을 배치한다
3. 추상화 계층을 확인한다: 본문 < 요점 < 범주
4. 본문에서 생략된 범주가 있으면 추론하여 보충한다 (inferredCategories에 기록)

**GPS 유형 판단 기준표**:

| 유형 | 판단 기준 | 예시 |
|------|----------|------|
| 분기형 (branch) | 요소 간 상하 계층이 있음 | 원인→하위원인→세부원인 |
| 포함형 (contain) | 여러 요소를 하나로 모을 수 있음 | 리스크(기술, 일정, 인력) |
| 중심형 (hub) | 요소가 중심 개념을 촉진/지원함 | 고객만족(가격, 품질, 서비스) |

**출력**: `csAnalysis` 필드에 기록

### A.2 Step 2: GPS Structuring (구조화)

**목적**: CS에서 추출한 범주와 요점을 "어떻게 구조화할 것인가"를 결정한다.

**프로세스**:
1. **Group 탐색**: 같은 종류끼리 묶을 수 있는 요소를 찾는다
   - 유형 판단: 분기형 / 포함형 / 중심형
   - typeRationale에 판단 근거를 반드시 기록한다
2. **Parallel 탐색**: 그룹이 여러 개라면 공통 항목으로 비교표를 구성할 수 있는지 검토한다
3. **Series 탐색**: 그룹 내 요소에 순서를 매길 기준이 있는지 확인한다
   - 순서를 매기는 이유: 빠짐/중복 확인, 추가 그룹 발견, 목적 정립
   - 누락된 단계가 있으면 gaps에 기록한다
4. **다이어그램 생성**: GPS 결과를 ASCII 다이어그램으로 변환한다

**출력**: `gpsStructure` 필드에 기록

### A.3 Step 3: Pyramid Building (피라미드 구축)

**변경사항**: 기존 Step 2와 동일한 로직이지만, CS/GPS 결과를 입력으로 활용.

- CS의 categories를 피라미드 Level 1 후보로 사용한다
- GPS의 groups로 계층 구조를 구성한다
- GPS의 parallels를 동일 레벨 배치의 근거로 사용한다
- GPS의 series로 근거 순서를 결정한다

### A.4 Step 4: MECE Validation (강화)

**기존 로직 유지 + 로직트리 규칙 추가**:

- 피라미드 1~2단계: **엄격한 MECE** 적용
- 피라미드 3단계 이후: **MECE 완화** (완벽한 분류보다 실용적 분류 우선)
- 자식 요소는 부모 1개만 가진다 (중복 배치 금지 → singleParentViolations에 기록)
- MECE가 잘 안 되는 경우: "문제를 올바르게 이해할 기회"로 해석하여 재구조화 유도

**출력**: `meceCheck.logicTreeRules` 필드에 기록

### A.5 Step 5: Logic Analysis (변경 없음)

기존 로직 그대로 유지.

### A.6 Step 6: Summary Generation (변경 없음)

기존 로직 그대로 유지.

### A.7 역방향 구조화: 질문→결론→행동

**적용 조건**: 입력에 이미 결론이 포함되어 있으나 논리적 설명이 부족한 경우

**판단 기준**:
- 입력이 "~해야 한다", "~이 답이다", "결론적으로" 등 결론 표현으로 시작/포함
- 근거나 배경 설명이 결론 대비 부족한 경우

**프로세스**:
1. 입력에서 결론(주장)을 먼저 식별한다 → `identifiedConclusion`
2. "이 결론에 대해 어떤 질문이 나올 수 있는가?"를 역추적한다 → `retroQuestions`
3. 질문에 대한 답변으로 근거를 구성한다 → `evidenceMapping`
4. 근거 → 구체적 행동(액션)을 도출한다 → `derivedActions`
5. 결과를 **Step 3(Pyramid Building)**으로 전달하여 정방향 피라미드로 재구성한다
6. `structuringPath = "reverse"`로 기록한다

---

## Appendix B: problem-solving.md 인과관계 모델 확장 상세

### B.1 Phase 3 확장: 독립/종속 사건 프레임

기존 5 Whys 분석 후, 추가 프레임을 적용한다:

**프로세스**:
1. 5 Whys로 도출된 원인들을 **독립 사건**과 **종속 사건**으로 분류한다
   - 독립 사건: 다른 사건의 영향 없이 스스로 발생하는 사건
   - 종속 사건: 독립 사건에 의해 발생하는 사건
2. 독립→종속 인과관계를 매핑한다
3. **예측 활용**: 독립 사건의 관찰 여부로 종속 사건 발생을 예측한다

**출력 위치**: Phase 3 "근본 원인 분석" 섹션에 추가

```markdown
### 3.3 인과관계 모델

#### 독립 사건 (원인)
- {사건 A}: {설명}
- {사건 B}: {설명}

#### 종속 사건 (결과)
- {사건 C}: ← {사건 A}에 의해 발생
- {사건 D}: ← {사건 A, B}에 의해 발생

#### 예측
- IF {사건 A 관찰} THEN {사건 C 발생 예상} (confidence: high)
```

---

## Appendix C: writing.md / expression.md 동기화 상세

### C.1 Phase 1 변경 (writing.md)

**현재**:
```markdown
### Phase 1: Minto 사고 정리 (내부 실행)
1. Idea Structuring
2. Pyramid Building
3. MECE Validation
4. Logic Analysis
5. Summary Generation
```

**변경 후**:
```markdown
### Phase 1: Minto 사고 정리 (6-Step 내부 실행)
1. CS Analysis: 입력에서 범주·요점을 추출한다
2. GPS Structuring: 그룹/패러렐/시리즈로 구조화한다
3. Pyramid Building: CS/GPS 결과 기반으로 피라미드를 구축한다
4. MECE Validation: 로직트리 규칙(1~2단계 엄격, 3단계+ 완화)을 적용한다
5. Logic Analysis: 인과관계 일관성을 확인한다
6. Summary Generation: coreClaim 1문장 + 3줄 요약을 생성한다
```

### C.2 Phase 1 변경 (expression.md)

writing.md와 동일한 6-Step 참조 구조를 적용한다. 단, expression.md는 Step 4(핵심 메시지 추출)가 있으므로:

**변경 후**:
```markdown
### Phase 1: Minto 사고 정리 (6-Step 내부 실행)
1. CS Analysis: 입력에서 범주·요점을 추출한다
2. GPS Structuring: 그룹/패러렐/시리즈로 구조화한다
3. Pyramid Building: CS/GPS 결과 기반으로 피라미드를 구축한다
4. MECE Validation: 로직트리 규칙 적용
5. 핵심 메시지 추출: 청중에게 전달할 1개 핵심 메시지 결정
```

> Step 5(Logic Analysis)와 Step 6(Summary)는 expression.md 특성상 Phase 2에서 처리하므로 생략.

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-06-18 | Initial draft — Option B (Clean Architecture) 선택 | 박수민 |
