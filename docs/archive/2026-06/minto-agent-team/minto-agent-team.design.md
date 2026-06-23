# minto-agent-team Design Document

> **Summary**: Minto 피라미드 원칙 기반 5-에이전트 팀 — Claude Code 스킬/에이전트 시스템
>
> **Project**: agent-creator
> **Author**: 박수민
> **Date**: 2026-06-17
> **Status**: Draft
> **Planning Doc**: [minto-agent-team.plan.md](../01-plan/minto-agent-team.plan.md)

---

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 개인의 산재된 아이디어를 논리적으로 정리하고 다양한 형태로 표현하는 과정이 비효율적 |
| **WHO** | 단독으로 의사결정·분석·문서 작성을 수행하는 개인 전문가 |
| **RISK** | 에이전트 간 통신 복잡도, Minto 원칙 오류 해석, 컨텍스트 크기 증가 |
| **SUCCESS** | MECE 검증 정확도 >90%, 응답 <2분, 즉시 사용 가능 비율 >80% |
| **SCOPE** | 5-에이전트 팀 (Orchestrator + Thinking + Writing + Problem Solving + Expression), Claude Code 스킬 기반 |

---

## 1. Overview

### 1.1 Design Goals

- Claude Code Agent 도구를 활용한 프롬프트 기반 에이전트 팀 구현
- 호출 깊이 2단계 이내 유지 (스킬→Orchestrator→Agent)
- 각 전문 에이전트가 Thinking 로직을 내장하여 단일 호출로 완결
- `.minto-memory.json`을 통한 세션 간 컨텍스트 유지
- Markdown/Obsidian 호환 출력

### 1.2 Design Principles

- **Self-Contained Agent**: 전문 에이전트는 Thinking 로직을 내장하여 독립 실행 가능
- **Explicit Command**: 커맨드 기반 라우팅으로 의도 분류 오차 제거
- **Prompt-Only Implementation**: 코드 없이 Markdown 프롬프트만으로 구현

---

## 2. Architecture Options (v1.7.0)

### 2.0 Architecture Comparison

| Criteria | Option A: Minimal | Option B: Clean | Option C: Pragmatic |
|----------|:-:|:-:|:-:|
| **Approach** | 스킬→에이전트 직접 호출, Orchestrator 없음 | 5-에이전트 완전 분리, 2단 호출 | Thinking 내장, Orchestrator 라우팅 |
| **New Files** | 8 | 9 | 9 |
| **Complexity** | Low | High | Medium |
| **Maintainability** | Medium | High | High |
| **Effort** | Low | High | Medium |
| **Risk** | Low (커플링) | Medium (호출 깊이 3단계) | Low (균형) |
| **Recommendation** | 빠른 프로토타입 | 장기 프로젝트 | **Default choice** |

**Selected**: Option C — **Rationale**: 호출 깊이 2단계로 Claude Code 런타임 제약 회피, Thinking 로직을 각 전문 분야에 맞게 튜닝 가능, Orchestrator로 컨텍스트 관리 중앙화

### 2.1 Component Diagram

```
PRESENTATION LAYER (Skills)
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ /think   │ │ /write   │ │ /solve   │ │ /express │
└────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
     │            │            │            │
     v            v            v            v
┌──────────────────────────────────────────────────┐
│              orchestrator.md                      │
│  (커맨드 파싱 → 라우팅 → 컨텍스트 로드/저장)       │
└──┬──────────┬──────────────┬──────────────┬──────┘
   │          │              │              │
   v          v              v              v
┌──────┐ ┌────────┐ ┌───────────────┐ ┌──────────┐
│think │ │writing │ │problem-solving│ │expression│
│.md   │ │.md     │ │.md            │ │.md       │
│      │ │        │ │               │ │          │
│Minto │ │Minto   │ │Minto          │ │Minto     │
│Engine│ │+SCQA   │ │+Hypothesis    │ │+Slide    │
│      │ │+Report │ │+RCA           │ │+Story    │
└──────┘ └────────┘ └───────────────┘ └──────────┘

OUTPUT LAYER
┌──────────────────────────────────────────────────┐
│  .minto-memory.json  │  docs/01~04-*/ (Markdown) │
└──────────────────────────────────────────────────┘
```

### 2.2 Data Flow

```
User Input (/command + text)
    │
    v
Skill (.claude/skills/*.md)
    │ Agent 도구로 호출
    v
Orchestrator Agent
    │ 1. 커맨드 파싱
    │ 2. .minto-memory.json 로드
    │ 3. 대상 에이전트 결정
    │ 4. Agent 도구로 전문 에이전트 호출 (사용자 입력 + 컨텍스트 전달)
    v
Specialist Agent (thinking / writing / problem-solving / expression)
    │ 1. Minto Thinking 내부 실행 (피라미드 구성, MECE 검증)
    │ 2. 전문 처리 (SCQA / 가설-분석 / 슬라이드 등)
    │ 3. Markdown 출력 생성
    v
Orchestrator Agent (반환 수신)
    │ 1. .minto-memory.json 업데이트
    │ 2. docs/ 하위에 파일 저장
    │ 3. 사용자에게 최종 출력 전달
    v
User Output (Markdown 문서)
```

### 2.3 Dependencies

| Component | Depends On | Purpose |
|-----------|-----------|---------|
| Skills (4개) | Orchestrator Agent | 스킬은 Orchestrator를 Agent 도구로 호출 |
| Orchestrator | Specialist Agents | 라우팅 대상 에이전트 호출 |
| Orchestrator | `.minto-memory.json` | 세션 간 컨텍스트 유지 |
| Specialist Agents | 없음 (자기 완결) | Thinking 로직 내장, 독립 실행 |

---

## 3. Data Model

### 3.1 ThinkingResult (에이전트 내부 산출물)

```typescript
// 각 전문 에이전트가 내부적으로 생성하는 Thinking 결과 구조
interface ThinkingResult {
  coreClaim: string;           // 핵심 주장 1문장
  pyramid: PyramidNode[];      // 피라미드 계층 구조
  meceCheck: {
    passed: boolean;
    issues: string[];
  };
  summary: string;             // 전체 요약 3줄 이내
  rawIdeas: string[];          // 원본 아이디어 목록
}

interface PyramidNode {
  level: number;               // 1 = 최상위
  statement: string;           // 주요 근거
  supports: string[];          // 세부 근거
}
```

### 3.2 MintoMemory (세션 컨텍스트)

```typescript
// .minto-memory.json 구조
interface MintoMemory {
  previousThoughts: ThoughtEntry[];    // 최근 생각 정리 결과
  previousAnalysis: AnalysisEntry[];   // 최근 문제 분석 결과
  previousDocuments: DocumentEntry[];  // 최근 생성 문서
  maxItemsPerArray: number;            // 배열 최대 크기 (기본 20)
  userPreferences: {
    reportTemplate: string;            // 선호 보고서 템플릿
    preferredFormat: "markdown" | "obsidian";
  };
}

interface ThoughtEntry {
  id: string;
  timestamp: string;
  coreClaim: string;
  pyramidSummary: string;
  savedTo: string;             // 저장 파일 경로
}

interface AnalysisEntry {
  id: string;
  timestamp: string;
  problem: string;
  hypothesis: string[];
  solution: string;
  savedTo: string;
}

interface DocumentEntry {
  id: string;
  timestamp: string;
  type: "report" | "memo" | "slide" | "thought";
  title: string;
  savedTo: string;
}
```

### 3.3 출력 문서 Frontmatter (Obsidian 호환, Phase 5)

```yaml
---
tags: [minto, thinking]        # 에이전트 유형별 태그
created: 2026-06-17
related: []                     # 관련 문서 링크 (v2 Wikilink)
type: thought | report | analysis | slide | memo
coreClaim: "핵심 주장 요약"
---
```

---

## 4. API Specification

> 이 프로젝트는 REST API가 아닌 Claude Code 스킬/에이전트 시스템이므로, 에이전트 간 인터페이스를 API로 정의합니다.

### 4.1 스킬 → Orchestrator 인터페이스

| Skill | 전달 데이터 | 기대 반환 |
|-------|-----------|----------|
| `/think` | `{command: "think", input: "사용자 텍스트"}` | ThinkingResult JSON + 피라미드 Markdown |
| `/write` | `{command: "write", input: "사용자 텍스트", options?: {template: string}}` | SCQA 기반 보고서 Markdown |
| `/solve` | `{command: "solve", input: "문제 설명"}` | 가설 + RCA + 해결안 Markdown |
| `/express` | `{command: "express", input: "사용자 텍스트", format: "slide"|"memo"|"story"}` | 발표/메모/스토리 Markdown |

### 4.2 Orchestrator → Specialist Agent 인터페이스

Orchestrator는 Agent 도구의 `prompt` 파라미터에 아래 구조를 인라인으로 전달:

```
## Task
{command} 수행

## User Input
{사용자 원본 텍스트}

## Context (from .minto-memory.json)
{이전 생각 정리 요약, 관련 문서 목록 등}

## Output Requirements
- Markdown 형식
- Frontmatter 포함 (Obsidian 호환)
- 파일 저장 경로: docs/{category}/{filename}.md
```

---

## 5. Agent 상세 설계

> UI/UX 대신 각 에이전트의 프롬프트 구조와 역할을 상세 정의합니다.

### 5.1 Orchestrator Agent (`orchestrator.md`)

**역할**: 커맨드 파싱, 라우팅, 컨텍스트 관리

**프롬프트 구조**:
```markdown
---
model: sonnet
description: Minto 에이전트 팀 오케스트레이터
tools: Agent, Read, Write
---

## Role
사용자 스킬 호출을 수신하여 적절한 전문 에이전트로 라우팅하는 중앙 조율자.

## Responsibilities
1. 커맨드 파싱 (think/write/solve/express)
2. .minto-memory.json 로드 및 컨텍스트 준비
3. 대상 에이전트를 Agent 도구로 호출
4. 반환된 결과를 docs/ 하위에 저장
5. .minto-memory.json 업데이트

## Routing Rules
| Command | Agent | Category |
|---------|-------|----------|
| think   | thinking.md | docs/01-thinking/ |
| write   | writing.md | docs/02-writing/ |
| solve   | problem-solving.md | docs/03-solving/ |
| express | expression.md | docs/04-expressing/ |

## Context Template
(Agent 호출 시 prompt에 포함할 컨텍스트 구조)
```

**모델**: `sonnet` (라우팅은 가벼운 작업)

### 5.2 Thinking Agent (`thinking.md`)

**역할**: Minto 피라미드 원칙 적용, 사고 구조화

**프롬프트 구조**:
```markdown
---
model: opus
description: Minto 피라미드 원칙 기반 사고 정리 엔진
tools: Read, Write
---

## Role
사용자의 산재된 아이디어를 Minto 피라미드 원칙으로 구조화하는 핵심 사고 엔진.

## 5-Step Process
1. **Idea Structuring**: 입력에서 아이디어 추출 → 주제별 그룹핑
   - 귀납형 판단: 여러 사실 → 공통 결론
   - 연역형 판단: 전제 → 논리적 결론
2. **Pyramid Building**: 최상위 핵심 주장(SCQ Answer) → 하위 근거 계층
3. **MECE Validation**: 각 레벨에서 중복/누락 검증
4. **Logic Analysis**: 인과관계 일관성 확인
5. **Summary Generation**: coreClaim 1문장 + 3줄 요약

## Output Format
- ThinkingResult JSON (내부 처리용)
- 피라미드 구조 Markdown (사용자 출력용)
```

**모델**: `opus` (깊은 논리 분석 필요)

### 5.3 Writing Agent (`writing.md`)

**역할**: SCQA 기반 보고서·문서 생성

**프롬프트 구조**:
```markdown
---
model: opus
description: Minto SCQA 기반 문서 작성 에이전트
tools: Read, Write, Glob
---

## Role
사용자 입력을 Minto 피라미드로 구조화한 뒤 SCQA 기반 문서를 생성.

## Integrated Thinking Process
(Thinking Agent의 5-Step Process를 Writing 맥락에 맞게 내장)
1. 입력 분석 → 피라미드 구성 → MECE 검증
2. SCQA 매핑:
   - Situation: 현재 상황/배경
   - Complication: 문제/변화
   - Question: 핵심 질문
   - Answer: 핵심 주장 (= coreClaim)

## Document Templates
- 기본 보고서 (docs/templates/report-default.md 참조)
- 메모/요약 (간결 버전)
- 사용자 정의 템플릿 지원

## Output Format
- Markdown 보고서 (Obsidian Frontmatter 포함)
```

**모델**: `opus` (고품질 문서 생성 필요)

### 5.4 Problem Solving Agent (`problem-solving.md`)

**역할**: 가설 생성 → 분석 → 해결안 도출

**프롬프트 구조**:
```markdown
---
model: opus
description: Minto 논리 기반 문제 해결 에이전트
tools: Read, Write
---

## Role
문제를 Minto 피라미드로 분해하고, 가설 기반 분석으로 해결안을 도출.

## Integrated Thinking Process
(Thinking Agent의 5-Step Process를 Problem Solving 맥락에 맞게 내장)
1. 문제 구조화 → 피라미드 분해 → MECE 검증
2. 가설 생성 (최소 3개)
3. Root Cause Analysis (인과관계 추적)
4. 해결안 도출 (실행 가능성 기준 우선순위)
5. 액션 플랜 구성

## Output Format
- 문제 분석 Markdown (가설 → 분석 → 해결안 → 액션 플랜)
```

**모델**: `opus` (복잡한 분석 필요)

### 5.5 Expression Agent (`expression.md`)

**역할**: 발표 슬라이드, 메모, 스토리텔링

**프롬프트 구조**:
```markdown
---
model: sonnet
description: Minto 논리 기반 표현 에이전트
tools: Read, Write
---

## Role
구조화된 사고를 발표 슬라이드, 메모, 스토리로 변환.

## Integrated Thinking Process
(Thinking Agent의 5-Step Process를 Expression 맥락에 맞게 내장)
1. 입력 분석 → 피라미드 구성 → 핵심 메시지 추출

## Output Formats
### Slide (Marp 호환)
- `---` 구분자 기반 슬라이드 분할
- 피라미드 최상위 = 제목 슬라이드
- 각 근거 = 개별 슬라이드

### Memo
- 1페이지 이내 핵심 요약
- coreClaim + 3개 근거 구조

### Storytelling
- 도입(Hook) → 전개(Build) → 결론(Deliver)
- SCQA 기반 내러티브
```

**모델**: `sonnet` (표현은 속도 우선, 충분한 품질)

---

## 6. Error Handling

### 6.1 에이전트 레벨 에러 처리

| 상황 | 처리 방법 |
|------|----------|
| 커맨드 미지정 | Orchestrator가 사용 가능한 커맨드 안내 메시지 출력 |
| 입력 텍스트 없음 | "정리할 내용을 입력해주세요" 안내 |
| `.minto-memory.json` 없음 | 기본 구조로 신규 생성 |
| `.minto-memory.json` 파싱 실패 | 백업 후 기본 구조로 재생성 |
| 전문 에이전트 호출 실패 | Orchestrator가 에러 메시지 전달 + 재시도 안내 |
| 출력 파일 저장 실패 | 콘솔에 출력 + 저장 실패 알림 |
| 컨텍스트 크기 초과 | 최근 5건만 전달 (maxItemsPerArray 적용) |

---

## 7. Security Considerations

- [x] 프롬프트 인젝션 방어: 에이전트 프롬프트에 역할 경계 명시
- [x] 파일 I/O 범위 제한: `docs/` 및 `.minto-memory.json`만 쓰기 대상
- [ ] 민감 정보 필터링: 사용자 입력에 API 키 등 포함 시 경고 (v2)
- [N/A] HTTPS: 해당 없음 (로컬 실행)
- [N/A] Rate Limiting: 해당 없음 (Claude Code 자체 제한)

---

## 8. Test Plan

> Claude Code 스킬/에이전트 시스템이므로 수동 검증 기반 테스트.

### 8.1 Test Scope

| Type | Target | Method | Phase |
|------|--------|--------|-------|
| L1: 커맨드 테스트 | 각 스킬 호출 → 올바른 에이전트 라우팅 | 수동 실행 | Do |
| L2: 출력 품질 테스트 | 생성된 Markdown 구조·내용 검증 | 수동 검토 | Do |
| L3: E2E 시나리오 | 전체 흐름 (입력→구조화→출력→저장) | 수동 실행 | Do |

### 8.2 L1: 커맨드 테스트 시나리오

| # | Command | Input | Expected | Verification |
|---|---------|-------|----------|-------------|
| 1 | `/think` | "AI가 교육을 바꿀 3가지 이유" | ThinkingResult + 피라미드 Markdown | coreClaim 존재, pyramid 3개 이상 |
| 2 | `/write` | "분기 실적 보고서 작성" | SCQA 구조 보고서 | S/C/Q/A 섹션 존재 |
| 3 | `/solve` | "팀 생산성 저하 원인 분석" | 가설 3개 + 해결안 | 가설·RCA·액션플랜 존재 |
| 4 | `/express slide` | "신제품 런칭 발표" | Marp 슬라이드 Markdown | `---` 구분자, 슬라이드 5개 이상 |
| 5 | `/express memo` | "회의 결과 정리" | 1페이지 메모 | coreClaim + 3근거 구조 |
| 6 | 커맨드 없음 | "아무 텍스트" | 커맨드 안내 메시지 | 4개 커맨드 목록 표시 |

### 8.3 L2: 출력 품질 테스트

| # | Agent | Check Item | Pass Criteria |
|---|-------|-----------|---------------|
| 1 | Thinking | MECE 검증 | `meceCheck.passed: true` |
| 2 | Thinking | 피라미드 깊이 | 최소 2레벨 |
| 3 | Writing | SCQA 구조 | 4개 섹션 모두 존재 |
| 4 | Writing | Frontmatter | tags, created, type 필드 존재 |
| 5 | Problem Solving | 가설 수 | 최소 3개 |
| 6 | Problem Solving | 액션 플랜 | 구체적 실행 항목 포함 |
| 7 | Expression | Marp 호환 | 헤더 + `---` 구분자 구조 |

### 8.4 L3: E2E 시나리오

| # | Scenario | Steps | Success Criteria |
|---|----------|-------|-----------------|
| 1 | 생각→문서 | `/think` → `/write` (이전 결과 활용) | 두 번째 호출에서 이전 컨텍스트 반영 |
| 2 | 문제→발표 | `/solve` → `/express slide` | 문제 분석 결과가 슬라이드에 반영 |
| 3 | 메모리 지속성 | `/think` → 세션 종료 → 재시작 → `/write` | `.minto-memory.json`에서 이전 결과 로드 |
| 4 | 파일 저장 | 아무 커맨드 실행 | `docs/` 하위에 Markdown 파일 생성 |

---

## 9. Clean Architecture

### 9.1 Layer Structure

| Layer | Responsibility | Location |
|-------|---------------|----------|
| **Presentation** | 사용자 인터페이스 (스킬 커맨드) | `.claude/skills/` |
| **Application** | 라우팅, 컨텍스트 관리 | `.claude/agents/orchestrator.md` |
| **Domain** | Minto 논리 엔진 (각 에이전트 내장) | `.claude/agents/{specialist}.md` |
| **Infrastructure** | 파일 I/O, 메모리 저장 | `.minto-memory.json`, `docs/` |

### 9.2 Dependency Rules

```
┌────────────────────────────────────────────────────────────┐
│                    Dependency Direction                      │
├────────────────────────────────────────────────────────────┤
│                                                            │
│   Skills ──→ Orchestrator ──→ Specialist Agents            │
│                    │                   │                    │
│                    └──→ .minto-memory.json (Read/Write)    │
│                                        │                    │
│                                        └──→ docs/ (Write)  │
│                                                            │
│   Rule: Specialist Agent는 다른 Agent를 호출하지 않음       │
│         Orchestrator만 Agent 도구 사용                      │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### 9.3 This Feature's Layer Assignment

| Component | Layer | Location |
|-----------|-------|----------|
| `/think` skill | Presentation | `.claude/skills/think.md` |
| `/write` skill | Presentation | `.claude/skills/write.md` |
| `/solve` skill | Presentation | `.claude/skills/solve.md` |
| `/express` skill | Presentation | `.claude/skills/express.md` |
| Orchestrator Agent | Application | `.claude/agents/orchestrator.md` |
| Thinking Agent | Domain | `.claude/agents/thinking.md` |
| Writing Agent | Domain | `.claude/agents/writing.md` |
| Problem Solving Agent | Domain | `.claude/agents/problem-solving.md` |
| Expression Agent | Domain | `.claude/agents/expression.md` |
| MintoMemory | Infrastructure | `.minto-memory.json` |
| Output Files | Infrastructure | `docs/01~04-*/` |

---

## 10. Coding Convention Reference

> 이 프로젝트는 코드가 아닌 Markdown 프롬프트 기반이므로, 프롬프트 작성 컨벤션을 정의합니다.

### 10.1 에이전트 파일 컨벤션

| Target | Rule | Example |
|--------|------|---------|
| 에이전트 파일명 | kebab-case.md | `problem-solving.md` |
| 스킬 파일명 | kebab-case.md | `think.md` |
| Frontmatter | YAML, model/description/tools 필수 | `model: opus` |
| 섹션 구분 | `##` H2 기준 | `## Role`, `## Process` |
| 출력 파일명 | `{timestamp}-{title}.md` | `20260617-ai-education.md` |

### 10.2 출력 문서 컨벤션

| Item | Convention |
|------|-----------|
| Frontmatter | 모든 출력에 tags/created/type 포함 |
| 태그 체계 | `#minto`, `#thinking`, `#writing`, `#solving`, `#expressing` |
| 저장 경로 | `docs/01-thinking/`, `02-writing/`, `03-solving/`, `04-expressing/` |
| 파일명 | `YYYYMMDD-{kebab-title}.md` |

---

## 11. Implementation Guide

### 11.1 File Structure

```
.claude/
├── skills/
│   ├── think.md              # /think 커맨드 스킬
│   ├── write.md              # /write 커맨드 스킬
│   ├── solve.md              # /solve 커맨드 스킬
│   └── express.md            # /express 커맨드 스킬
├── agents/
│   ├── orchestrator.md       # 중앙 라우터
│   ├── thinking.md           # Minto 사고 엔진
│   ├── writing.md            # SCQA 문서 생성
│   ├── problem-solving.md    # 가설-분석-해결
│   └── expression.md         # 슬라이드/메모/스토리
docs/
├── 01-thinking/              # 생각 정리 결과
├── 02-writing/               # 생성된 문서
├── 03-solving/               # 문제 분석 및 솔루션
├── 04-expressing/            # 발표 자료 및 메모
├── templates/
│   └── report-default.md     # 기본 보고서 템플릿
.minto-memory.json            # 세션 컨텍스트 저장
```

### 11.2 Implementation Order

1. [ ] `.minto-memory.json` 초기 구조 생성
2. [ ] `thinking.md` 에이전트 구현 (Minto 핵심 엔진)
3. [ ] `think.md` 스킬 구현 + 단독 테스트
4. [ ] `orchestrator.md` 구현 (라우팅 + 컨텍스트)
5. [ ] `writing.md` 에이전트 구현 + `write.md` 스킬
6. [ ] `report-default.md` 기본 템플릿 생성
7. [ ] `problem-solving.md` 에이전트 구현 + `solve.md` 스킬
8. [ ] `expression.md` 에이전트 구현 + `express.md` 스킬
9. [ ] 전체 통합 테스트 (메모리 지속성 포함)

### 11.3 Session Guide

#### Module Map

| Module | Scope Key | Description | Estimated Turns |
|--------|-----------|-------------|:---------------:|
| Core Engine | `module-1` | `.minto-memory.json` + `thinking.md` + `think.md` 스킬 + 단독 테스트 | 30-40 |
| Orchestrator + Writing | `module-2` | `orchestrator.md` + `writing.md` + `write.md` 스킬 + 보고서 템플릿 | 30-40 |
| Problem Solving | `module-3` | `problem-solving.md` + `solve.md` 스킬 | 20-30 |
| Expression | `module-4` | `expression.md` + `express.md` 스킬 + 통합 테스트 | 20-30 |

#### Recommended Session Plan

| Session | Phase | Scope | Turns |
|---------|-------|-------|:-----:|
| Session 1 | Plan + Design | 전체 | 30-35 |
| Session 2 | Do | `--scope module-1` | 30-40 |
| Session 3 | Do | `--scope module-2` | 30-40 |
| Session 4 | Do | `--scope module-3,module-4` | 30-40 |
| Session 5 | Check + Report | 전체 | 20-30 |

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-06-17 | Initial draft — Option C (Pragmatic) 선택 | 박수민 |
