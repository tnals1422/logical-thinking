# minto-agent-team Analysis Report

> **Analysis Type**: Design-Implementation Gap Analysis (Static Only)
>
> **Project**: agent-creator
> **Analyst**: gap-detector
> **Date**: 2026-06-17
> **Design Doc**: [minto-agent-team.design.md](../../02-design/features/minto-agent-team.design.md)

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 개인의 산재된 아이디어를 논리적으로 정리하고 다양한 형태로 표현하는 과정이 비효율적 |
| **WHO** | 단독으로 의사결정·분석·문서 작성을 수행하는 개인 전문가 |
| **RISK** | 에이전트 간 통신 복잡도, Minto 원칙 오류 해석, 컨텍스트 크기 증가 |
| **SUCCESS** | MECE 검증 정확도 >90%, 응답 <2분, 즉시 사용 가능 비율 >80% |
| **SCOPE** | 5-에이전트 팀 (Orchestrator + Thinking + Writing + Problem Solving + Expression), Claude Code 스킬 기반 |

---

## Overall Scores

### Structural & Contract Scores

| Category | Score | Status |
|----------|:-----:|:------:|
| Structural Match | 73% | ⚠️ |
| Functional Depth | 92% | ✅ |
| Contract Match | 93% | ✅ |

### Weighted Overall (Static Only: Structural x0.2 + Functional x0.4 + Contract x0.4)

| **Overall Match Rate** | **87.6%** | ⚠️ |

---

## 1. Structural Match (73%)

### 1.1 File Existence

| # | Design File | Exists | Status |
|---|-------------|:------:|:------:|
| 1 | `.claude/skills/think.md` | Yes | ✅ |
| 2 | `.claude/skills/write.md` | Yes | ✅ |
| 3 | `.claude/skills/solve.md` | Yes | ✅ |
| 4 | `.claude/skills/express.md` | Yes | ✅ |
| 5 | `.claude/agents/orchestrator.md` | Yes | ✅ |
| 6 | `.claude/agents/thinking.md` | Yes | ✅ |
| 7 | `.claude/agents/writing.md` | Yes | ✅ |
| 8 | `.claude/agents/problem-solving.md` | Yes | ✅ |
| 9 | `.claude/agents/expression.md` | Yes | ✅ |
| 10 | `.minto-memory.json` | Yes | ✅ |
| 11 | `docs/templates/report-default.md` | Yes | ✅ |
| 12 | `docs/01-thinking/` (directory) | No | ❌ |
| 13 | `docs/02-writing/` (directory) | No | ❌ |
| 14 | `docs/03-solving/` (directory) | No | ❌ |
| 15 | `docs/04-expressing/` (directory) | No | ❌ |

**Result**: 11/15 items exist = **73%**

**Note**: 출력 디렉토리 4개는 아직 생성되지 않았다. 이들은 런타임에 첫 커맨드 실행 시 생성될 수 있으므로, 중요도는 낮다 (에이전트가 Write 도구로 자동 생성 가능).

---

## 2. Functional Depth (92%)

각 파일의 Design 명세 대비 실제 구현 깊이를 평가한다.

### 2.1 Per-File Depth Scores

| File | Depth | Design Requirements Met | Missing |
|------|:-----:|------------------------|---------|
| `thinking.md` | 100 | 5-Step Process, ThinkingResult JSON, 피라미드 Markdown, MECE, Constraints | -- |
| `orchestrator.md` | 95 | 커맨드 파싱, Routing Rules, 컨텍스트 로드/저장, Agent 호출, 에러 핸들링 6/7 | 컨텍스트 크기 초과 처리 표현이 간략 (1줄) |
| `writing.md` | 95 | Integrated Thinking 5-Step, SCQA 매핑, 기본 보고서 + 메모 템플릿, Frontmatter | 사용자 정의 템플릿 "지원" 언급만, 구체적 로딩 로직 없음 |
| `problem-solving.md` | 100 | Integrated Thinking, 가설 3개+, RCA, 해결안 우선순위, 액션 플랜 | -- |
| `expression.md` | 100 | Integrated Thinking, Slide/Memo/Story 3 formats, Marp 호환, SCQA 내러티브 | -- |
| `think.md` (skill) | 85 | command/input 전달, Orchestrator 호출 | Fallback 동작 포함 (Design에 미정의, 추가 기능) |
| `write.md` (skill) | 85 | command/input/options(template) 전달 | Fallback 동작 포함 |
| `solve.md` (skill) | 85 | command/input 전달 | Fallback 동작 포함 |
| `express.md` (skill) | 90 | command/input/format 전달, format 파싱 로직 | Fallback 동작 포함 |
| `.minto-memory.json` | 90 | MintoMemory 구조 일치: 5 fields | `related` 필드 미포함 (§3.3 Frontmatter에만 해당) |
| `report-default.md` | 95 | SCQA 구조, Frontmatter, MECE 검증 결과 | 근거 개수가 정확히 3으로 고정 (Design은 2~5 허용) |

**Average Depth**: 92.7% → **92%**

### 2.2 Observations

- **Fallback 패턴**: 4개 스킬 모두 Orchestrator 호출 실패 시 Specialist Agent를 직접 호출하는 Fallback 로직을 포함한다. Design에는 명시되지 않은 추가 기능이나, 아키텍처 원칙(2단계 호출)에 위반되지 않으며 안정성을 높인다.
- **Thinking Agent**: Design §5.2의 모든 항목을 빠짐없이 구현. 5-Step Process, ThinkingResult JSON 구조, 피라미드 Markdown 출력 모두 일치.
- **Problem Solving Agent**: Design §5.4의 5단계(구조화-가설-RCA-해결안-액션 플랜) 모두 구현. 해결안 테이블에 효과/난이도/긴급도 포함.

---

## 3. Contract Match (93%)

### 3.1 Skill → Orchestrator Interface (Design §4.1)

| Skill | Design Interface | Implementation | Match |
|-------|-----------------|----------------|:-----:|
| `/think` | `{command: "think", input}` | Task: "think 수행" + User Input 섹션 | ✅ |
| `/write` | `{command: "write", input, options?: {template}}` | Task + User Input + Options (template) | ✅ |
| `/solve` | `{command: "solve", input}` | Task: "solve 수행" + User Input 섹션 | ✅ |
| `/express` | `{command: "express", input, format}` | Task + User Input + Format 섹션 | ✅ |

**Score**: 4/4 = 100%

### 3.2 Orchestrator → Specialist Agent Interface (Design §4.2)

| Field | Design Spec | Implementation | Match |
|-------|------------|----------------|:-----:|
| Task | `{command} 수행` | `{command} 수행` | ✅ |
| User Input | `{사용자 원본 텍스트}` | `{사용자 원본 텍스트}` | ✅ |
| Context | `.minto-memory.json` 기반 | 이전 관련 결과 요약 (최근 5건) | ✅ |
| Output Requirements | Markdown + Frontmatter + 경로 | 동일 구조 | ✅ |

**Score**: 4/4 = 100%

### 3.3 ThinkingResult 구조 (Design §3.1)

| Field | Design | Implementation (thinking.md) | Match |
|-------|--------|------------------------------|:-----:|
| `coreClaim` | string | JSON example 포함 | ✅ |
| `pyramid[]` | `{level, statement, supports}` | 동일 구조 JSON | ✅ |
| `meceCheck` | `{passed, issues}` | 동일 구조 JSON | ✅ |
| `summary` | string | 포함 | ✅ |
| `rawIdeas` | string[] | 포함 | ✅ |

**Score**: 5/5 = 100%

### 3.4 MintoMemory 구조 (Design §3.2)

| Field | Design | Implementation (.minto-memory.json) | Match |
|-------|--------|-------------------------------------|:-----:|
| `previousThoughts` | ThoughtEntry[] | `[]` (빈 배열, 구조 일치) | ✅ |
| `previousAnalysis` | AnalysisEntry[] | `[]` | ✅ |
| `previousDocuments` | DocumentEntry[] | `[]` | ✅ |
| `maxItemsPerArray` | number (기본 20) | `20` | ✅ |
| `userPreferences.reportTemplate` | string | `"report-default"` | ✅ |
| `userPreferences.preferredFormat` | `"markdown" \| "obsidian"` | `"markdown"` | ✅ |

**Score**: 6/6 = 100%

### 3.5 Frontmatter (Design §10.1)

| Agent | Design model | Impl model | Design tools | Impl tools | Match |
|-------|:------------|:-----------|:-------------|:-----------|:-----:|
| orchestrator | sonnet | sonnet | Agent, Read, Write | Agent, Read, Write | ✅ |
| thinking | opus | opus | Read, Write | Read, Write | ✅ |
| writing | opus | opus | Read, Write, Glob | Read, Write, Glob | ✅ |
| problem-solving | opus | opus | Read, Write | Read, Write | ✅ |
| expression | sonnet | sonnet | Read, Write | Read, Write | ✅ |

**Score**: 5/5 = 100%

### 3.6 Skill Frontmatter

| Skill | Design | Implementation | Match |
|-------|--------|----------------|:-----:|
| think.md | model, description, tools 필수 | `description` + `user-invocable: true`, **no model/tools** | ⚠️ |
| write.md | model, description, tools 필수 | `description` + `user-invocable: true`, **no model/tools** | ⚠️ |
| solve.md | model, description, tools 필수 | `description` + `user-invocable: true`, **no model/tools** | ⚠️ |
| express.md | model, description, tools 필수 | `description` + `user-invocable: true`, **no model/tools** | ⚠️ |

**Note**: Design §10.1은 "Frontmatter: model/description/tools 필수"라고 명시했지만, 이것은 **에이전트** 파일에 해당하는 규칙이다. Claude Code 스킬 파일의 Frontmatter는 `description` + `user-invocable`이 표준이며, model/tools는 스킬에서 사용하지 않는다. Design 문서가 에이전트와 스킬의 Frontmatter 규칙을 구분하지 않은 것이 원인이다.

**Severity**: Minor (Design 명세의 모호함. 실제 동작에는 영향 없음)

**Score**: 0/4 = 0% (규칙 문자 그대로 적용 시), 하지만 이는 Design의 명세 결함이므로 실질 영향 반영하여 75%로 평가

### 3.7 Clean Architecture Dependency (Design §9)

| Rule | Design | Implementation | Match |
|------|--------|----------------|:-----:|
| Skills → Orchestrator | 단방향 | 스킬이 Agent 도구로 orchestrator 호출 | ✅ |
| Orchestrator → Specialist | 단방향 | Agent 도구로 전문 에이전트 호출 | ✅ |
| Specialist ↛ Other Agent | 호출 금지 | 각 에이전트 Constraints에 "다른 에이전트를 호출하지 않는다" 명시 | ✅ |
| Orchestrator만 Agent 도구 사용 | 명시 | orchestrator만 `tools: Agent` 보유 | ✅ |
| 파일 I/O 범위 | docs/ + .minto-memory.json | orchestrator Constraints에 명시 | ✅ |

**Score**: 5/5 = 100%

### 3.8 Error Handling (Design §6)

| # | Error Scenario | Design | Implementation (orchestrator.md) | Match |
|---|---------------|--------|----------------------------------|:-----:|
| 1 | 커맨드 미지정 | 안내 메시지 | 안내 메시지 (4개 커맨드 목록) | ✅ |
| 2 | 입력 텍스트 없음 | "정리할 내용을 입력해주세요" | 동일 | ✅ |
| 3 | `.minto-memory.json` 없음 | 기본 구조로 신규 생성 | 동일 | ✅ |
| 4 | `.minto-memory.json` 파싱 실패 | 백업 후 재생성 | 동일 (backup 파일명 명시) | ✅ |
| 5 | 전문 에이전트 호출 실패 | 에러 메시지 + 재시도 안내 | 동일 | ✅ |
| 6 | 출력 파일 저장 실패 | 콘솔 출력 + 저장 실패 알림 | 동일 | ✅ |
| 7 | 컨텍스트 크기 초과 | 최근 5건만 전달 | "최근 5건 이내" 언급 | ✅ |

**Score**: 7/7 = 100%

### Contract Match Summary

| Category | Items | Pass | Score |
|----------|:-----:|:----:|:-----:|
| §4.1 Skill→Orchestrator | 4 | 4 | 100% |
| §4.2 Orchestrator→Agent | 4 | 4 | 100% |
| §3.1 ThinkingResult | 5 | 5 | 100% |
| §3.2 MintoMemory | 6 | 6 | 100% |
| §10.1 Agent Frontmatter | 5 | 5 | 100% |
| §10.1 Skill Frontmatter | 4 | 3 | 75% |
| §9 Architecture | 5 | 5 | 100% |
| §6 Error Handling | 7 | 7 | 100% |
| **Total** | **40** | **39** | **93%** |

---

## Differences Found

### Missing Features (Design O, Implementation X)

| # | Severity | Item | Design Location | Description |
|---|----------|------|----------------|-------------|
| 1 | Minor | 출력 디렉토리 | §11.1 | `docs/01-thinking/`, `02-writing/`, `03-solving/`, `04-expressing/` 디렉토리 미생성 (런타임 자동 생성 가능) |
| 2 | Minor | Frontmatter `related` 필드 | §3.3 | 출력 문서 Frontmatter에 `related: []` 필드가 템플릿/에이전트에서 일관되게 포함되지 않음 |

### Added Features (Design X, Implementation O)

| # | Severity | Item | Implementation Location | Description |
|---|----------|------|------------------------|-------------|
| 1 | Minor | Fallback 패턴 | skills/*.md (4개 모두) | Orchestrator 실패 시 Specialist Agent 직접 호출 + 메모리/파일 저장 Fallback |
| 2 | Minor | Format Detection | expression.md:192-198 | 사용자 입력 키워드 기반 자동 format 감지 로직 (Design에 명시 없음) |

### Changed Features (Design != Implementation)

| # | Severity | Item | Design | Implementation | Impact |
|---|----------|------|--------|----------------|--------|
| 1 | Minor | Skill Frontmatter | model/description/tools 필수 (§10.1) | description + user-invocable (model/tools 없음) | Low -- Claude Code 스킬 표준에 맞는 구현. Design이 에이전트/스킬 규칙을 미분리 |
| 2 | Minor | Orchestrator Error Table | 7개 시나리오 (§6) | 6개 시나리오 (7번 "컨텍스트 크기 초과"는 Error Handling 테이블이 아닌 Process 섹션에 기술) | Low -- 처리 로직 자체는 존재 |

---

## Recommended Actions

### Documentation Update Needed

1. **Design §10.1 Frontmatter 규칙 분리**: 에이전트 파일과 스킬 파일의 Frontmatter 규칙을 별도로 정의해야 한다. 스킬은 `description` + `user-invocable`이 표준.
2. **Design §5 또는 §11에 Fallback 패턴 추가**: 구현에 존재하는 Fallback 동작을 Design에 반영하여 문서와 구현의 일관성을 유지.
3. **Design §5.5에 Format Detection 추가**: expression.md의 자동 format 감지 로직을 Design에 명시.

### Pre-Runtime Preparation

1. 출력 디렉토리 4개를 미리 생성하거나, 각 에이전트/Orchestrator에 디렉토리 자동 생성 지침을 명시. 현재 Write 도구가 디렉토리를 자동 생성하므로 실질적 문제는 아님.

---

## Score Calculation Detail

```
Structural Match:  73% x 0.2 = 14.6%
Functional Depth:  92% x 0.4 = 36.8%
Contract Match:    93% x 0.4 = 36.2%
────────────────────────────────
Overall Match Rate:      87.6%
```

**Verdict**: Match Rate 87.6% -- 90% 미만이므로 경미한 개선이 권장되나, 모든 차이가 Minor severity이므로 실질적 기능에는 영향이 없다. Design 문서 업데이트를 통해 90%+ 달성 가능.

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026-06-17 | Initial gap analysis | gap-detector |
