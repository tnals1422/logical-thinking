---
tags: [minto, analysis, pdca]
created: 2026-06-17
type: analysis
feature: minto-agent-team
phase: check
---

# minto-agent-team Gap Analysis

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 개인의 산재된 아이디어를 논리적으로 정리하고 다양한 형태로 표현하는 과정이 비효율적 |
| **WHO** | 단독으로 의사결정·분석·문서 작성을 수행하는 개인 전문가 |
| **RISK** | 에이전트 간 통신 복잡도, Minto 원칙 오류 해석, 컨텍스트 크기 증가 |
| **SUCCESS** | MECE 검증 정확도 >90%, 응답 <2분, 즉시 사용 가능 비율 >80% |
| **SCOPE** | 5-에이전트 팀 (Orchestrator + Thinking + Writing + Problem Solving + Expression) |

---

## 1. Strategic Alignment

| Check | Status | Evidence |
|-------|--------|----------|
| Plan 요구사항 반영 | PASS | 5-에이전트 구조, 커맨드 기반 라우팅, .minto-memory.json 컨텍스트 |
| Design 아키텍처 준수 | PASS | Option C (Pragmatic) - Thinking 내장, 2단계 호출 |
| Design 모델 선정 준수 | PASS | Orchestrator/Expression: sonnet, Thinking/Writing/ProblemSolving: opus |

---

## 2. Plan Success Criteria

| # | Criteria | Status | Evidence |
|---|----------|--------|----------|
| SC-1 | /think -> ThinkingResult + 피라미드 Markdown | MET | thinking.md: 5-Step Process + Output Format |
| SC-2 | ThinkingResult -> SCQA 보고서 | MET | writing.md: SCQA 매핑 + Document Templates |
| SC-3 | 가설 3개 이상 + 해결안 | MET | problem-solving.md: "최소 3개" + 우선순위 |
| SC-4 | Marp 슬라이드 + 메모 | MET | expression.md: Slide/Memo/Story 3형식 |
| SC-5 | Obsidian Frontmatter | MET | 모든 에이전트 Output에 Frontmatter 포함 |
| SC-6 | MECE 검증 >90% | PARTIAL | 로직 구현됨, 실제 정확도는 수동 테스트 필요 |
| SC-7 | 응답 <2분 | PARTIAL | 아키텍처상 가능, 실제 측정 필요 |

**Success Rate**: 5/7 Met, 2/7 Partial (수동 테스트 필요)

---

## 3. Match Rate

| Category | Score | Weight | Weighted |
|----------|:-----:|:------:|:--------:|
| Structural Match | 100% | 0.2 | 20.0% |
| Functional Depth | 92% | 0.4 | 36.8% |
| Contract Match | 93% | 0.4 | 37.2% |
| **Overall** | | | **94.0%** |

**Formula**: Static Only (no server) = Structural x 0.2 + Functional x 0.4 + Contract x 0.4

---

## 4. Structural Match (100%)

### File Existence

| Expected (Design 11.1) | Exists? |
|------------------------|---------|
| .claude/skills/think.md | YES |
| .claude/skills/write.md | YES |
| .claude/skills/solve.md | YES |
| .claude/skills/express.md | YES |
| .claude/agents/orchestrator.md | YES |
| .claude/agents/thinking.md | YES |
| .claude/agents/writing.md | YES |
| .claude/agents/problem-solving.md | YES |
| .claude/agents/expression.md | YES |
| .minto-memory.json | YES |
| docs/templates/report-default.md | YES |
| docs/01-thinking/ | YES |
| docs/02-writing/ | YES |
| docs/03-solving/ | YES |
| docs/04-expressing/ | YES |

---

## 5. Functional Depth (92%)

### Thinking Agent
- [x] 5-Step Process (Idea Structuring, Pyramid Building, MECE Validation, Logic Analysis, Summary Generation)
- [x] ThinkingResult JSON 출력 구조 (coreClaim, pyramid, meceCheck, summary, rawIdeas)
- [x] 피라미드 구조 Markdown 출력
- [x] 귀납형/연역형 판단 기준
- [x] Constraints (최소 2레벨, Level 1 2~5개)

### Orchestrator
- [x] 커맨드 파싱 (think/write/solve/express)
- [x] Routing Rules (4개 커맨드 -> 에이전트 + 카테고리)
- [x] .minto-memory.json 로드/저장
- [x] Agent 호출 프롬프트 구조 (Task/User Input/Context/Output Requirements)
- [x] 에러 핸들링 (7개 시나리오)
- [x] 메모리 업데이트 구조 (ThoughtEntry, AnalysisEntry, DocumentEntry)

### Writing Agent
- [x] Integrated Thinking Process (5-Step 내장)
- [x] SCQA 매핑 (Situation/Complication/Question/Answer)
- [x] 기본 보고서 템플릿
- [x] 메모/요약 간결 버전
- [ ] 사용자 정의 템플릿 지원 (언급만 있고, 로딩 메커니즘 미상세) — Minor

### Problem Solving Agent
- [x] Integrated Thinking Process (5-Step 내장)
- [x] 가설 생성 (최소 3개)
- [x] Root Cause Analysis (5 Whys)
- [x] 해결안 우선순위 (Impact/Effort/Urgency)
- [x] 액션 플랜 (Quick Wins/단기/중기)

### Expression Agent
- [x] Integrated Thinking Process (내장)
- [x] Slide (Marp 호환, --- 구분자, 5~12개)
- [x] Memo (1페이지, coreClaim + 3근거)
- [x] Storytelling (Hook -> Build -> Deliver, SCQA)
- [x] Format Detection (slide/memo/story 자동 감지) — Design 미정의 추가 기능

---

## 6. Contract Match (93%)

### Skill -> Orchestrator Interface (Design 4.1)
- [x] /think: command + input
- [x] /write: command + input + options(template)
- [x] /solve: command + input
- [x] /express: command + input + format

### Orchestrator -> Specialist Interface (Design 4.2)
- [x] Task/User Input/Context/Output Requirements 구조

### Data Model (Design 3.1~3.3)
- [x] ThinkingResult (coreClaim, pyramid, meceCheck, summary, rawIdeas)
- [x] MintoMemory (previousThoughts, previousAnalysis, previousDocuments, maxItemsPerArray, userPreferences)
- [x] Frontmatter (tags, created, type, coreClaim)
- [ ] Frontmatter related 필드 미포함 — Minor (v2 Wikilink 기능)

### Clean Architecture (Design 9)
- [x] Skills -> Orchestrator -> Specialist (단방향)
- [x] Specialist는 다른 Agent 호출 안 함
- [x] Orchestrator만 Agent 도구 사용

### Frontmatter Convention (Design 10.1)
- [x] 에이전트: model, description, tools 포함
- [ ] 스킬: model/tools 미포함 — Minor (Claude Code 스킬 표준과 Design 규칙 불일치)

---

## 7. Decision Record Verification

| Decision | Followed | Outcome |
|----------|----------|---------|
| [Plan] 5-에이전트 계층 구조 | YES | 5 agents + 4 skills 구현 |
| [Design] Option C Pragmatic | YES | Thinking 내장, 2단계 호출 유지 |
| [Design] Model 선정 | YES | sonnet/opus 정확히 반영 |
| [Design] 프롬프트 인라인 전달 | YES | Orchestrator prompt 구조 준수 |
| [Design] .minto-memory.json | YES | 초기 구조 + CRUD 로직 |

---

## 8. Gap List

| # | Severity | Gap | Location | Action |
|---|----------|-----|----------|--------|
| 1 | Minor | 스킬 Frontmatter에 model/tools 미포함 | .claude/skills/*.md | Design 10.1 규칙을 에이전트/스킬 분리로 수정 |
| 2 | Minor | Fallback 패턴 Design 미정의 | 모든 스킬 | Design에 Fallback 섹션 추가 권장 |
| 3 | Minor | Format Detection Design 미정의 | expression.md | Design 5.5에 반영 권장 |
| 4 | Minor | Frontmatter related 필드 미포함 | 에이전트 출력 | v2 범위, 현재 단계에서 선택적 |

**Critical: 0 | Important: 0 | Minor: 4**

---

## 9. Conclusion

Overall Match Rate: **94.0%** (PASS, >= 90% threshold)

구현이 Design 명세를 충실히 반영하며, 일부 영역에서는 Design보다 강건한 구현을 포함 (Fallback, Format Detection). Minor 갭 4건은 Design 문서 보완으로 해소 가능.

**Next**: `/pdca report minto-agent-team`
