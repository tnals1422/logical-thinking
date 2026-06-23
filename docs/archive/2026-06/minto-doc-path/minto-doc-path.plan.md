# minto-doc-path Planning Document

> **Summary**: minto-agent-team 산출물 저장 경로를 `docs/`에서 `minto-docs/`로 분리
>
> **Project**: agent-creator
> **Version**: -
> **Author**: 박수민
> **Date**: 2026-06-17
> **Status**: Draft

---

## Executive Summary

| Perspective | Content |
|-------------|---------|
| **Problem** | minto-agent-team이 `docs/`에 산출물을 저장하여 PDCA 문서와 섞임 |
| **Solution** | minto-agent-team의 모든 출력 경로를 `docs/` → `minto-docs/`로 교체 |
| **Function/UX Effect** | PDCA 문서와 minto 산출물이 명확히 분리되어 탐색·관리 편의성 향상 |
| **Core Value** | 두 시스템의 파일 충돌 제거 및 경로 의미 명확화 |

---

## Context Anchor

> Auto-generated from Executive Summary. Propagated to Design/Do documents for context continuity.

| Key | Value |
|-----|-------|
| **WHY** | minto-agent-team 산출물과 PDCA 문서가 `docs/`에 혼재하여 구분 불가 |
| **WHO** | agent-creator 프로젝트 사용자 (개인) |
| **RISK** | 기존에 `docs/0X-*/`에 저장된 minto 산출물의 경로 불일치 |
| **SUCCESS** | minto 커맨드 실행 시 산출물이 `minto-docs/` 하위에 저장됨 |
| **SCOPE** | `.claude/agents/` 및 `.claude/skills/` 내 경로 참조 수정 (6개 파일) |

---

## 1. Overview

### 1.1 Purpose

minto-agent-team(`/think`, `/write`, `/solve`, `/express`)이 생성하는 모든 Markdown 산출물의 저장 기본 경로를 `docs/`에서 `minto-docs/`로 변경하여, PDCA 사이클 문서(`docs/01-plan/`, `docs/02-design/` 등)와 명확히 분리한다.

### 1.2 Background

현재 minto-agent-team은 산출물을 `docs/01-thinking/`, `docs/02-writing/`, `docs/03-solving/`, `docs/04-expressing/`에 저장한다. PDCA도 `docs/01-plan/`, `docs/02-design/` 등을 사용하므로 같은 `docs/` 트리 안에서 두 시스템의 파일이 혼재된다. 경로를 `minto-docs/`로 분리하면 각 시스템의 역할이 경로 수준에서 명확해진다.

### 1.3 Related Documents

- 기존 Plan: `docs/archive/2026-06/minto-agent-team/minto-agent-team.plan.md`
- Orchestrator: `.claude/agents/orchestrator.md`

---

## 2. Scope

### 2.1 In Scope

- [x] `.claude/agents/orchestrator.md` 내 `docs/` 경로 참조 → `minto-docs/`로 변경
- [x] `.claude/skills/think.md` 경로 참조 변경
- [x] `.claude/skills/write.md` 경로 참조 변경
- [x] `.claude/skills/solve.md` 경로 참조 변경
- [x] `.claude/skills/express.md` 경로 참조 변경
- [x] `.claude/agents/writing.md` 템플릿 경로 참조 변경

### 2.2 Out of Scope

- 기존 `docs/0X-*/`에 생성된 minto 산출물 파일 이동 (별도 작업)
- `.minto-memory.json`의 `savedTo` 값 소급 수정
- PDCA 관련 경로 변경

---

## 3. Requirements

### 3.1 Functional Requirements

| ID | Requirement | Priority | Status |
|----|-------------|----------|--------|
| FR-01 | orchestrator.md의 모든 `docs/` 출력 경로를 `minto-docs/`로 변경 | High | ✅ Complete |
| FR-02 | 4개 스킬 파일(think/write/solve/express)의 저장 경로를 `minto-docs/`로 변경 | High | ✅ Complete |
| FR-03 | writing.md의 템플릿 참조 경로를 `minto-docs/templates/`로 변경 | Medium | ✅ Complete |
| FR-04 | 변경 후 `/think` 커맨드 실행 시 `minto-docs/01-thinking/`에 파일이 저장됨 | High | ✅ Complete |

### 3.2 Non-Functional Requirements

| Category | Criteria | Measurement Method |
|----------|----------|-------------------|
| 일관성 | 6개 파일 모두 동일한 `minto-docs/` prefix 사용 | 파일 내 `docs/0` grep 결과 0건 |
| 하위 호환 | PDCA 관련 `docs/` 경로는 미변경 | pdca 관련 파일 diff 확인 |

---

## 4. Success Criteria

### 4.1 Definition of Done

- [x] `.claude/` 하위 6개 파일에서 minto 관련 `docs/` 경로 참조가 0건
- [x] `/think`, `/write`, `/solve`, `/express` 커맨드 실행 시 `minto-docs/` 하위에 파일 생성 확인
- [x] PDCA 관련 파일 미변경 확인

### 4.2 Quality Criteria

- [x] `grep -r "docs/0[1-4]-" .claude/` 결과 0건 (minto 경로 잔존 없음)
- [x] `minto-docs/templates/report-default.md` 경로로 writing.md 참조 정상 동작

---

## 5. Risks and Mitigation

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| 기존 `docs/0X-*/` minto 파일과 경로 불일치 | Low | High | Out of Scope으로 분리, 필요 시 후속 작업으로 이동 |
| `minto-docs/templates/` 디렉터리 미존재 | Medium | Medium | Do 단계에서 `minto-docs/templates/` 디렉터리 생성 및 기존 템플릿 복사 포함 |
| PDCA 경로 실수 변경 | High | Low | 변경 범위를 `.claude/agents/orchestrator.md`, `.claude/skills/*.md`, `.claude/agents/writing.md`로 명시 한정 |

---

## 6. Impact Analysis

### 6.1 Changed Resources

| Resource | Type | Change Description |
|----------|------|--------------------|
| `.claude/agents/orchestrator.md` | Agent 정의 (Markdown) | 라우팅 테이블·저장 경로·제약 조항의 `docs/` → `minto-docs/` |
| `.claude/skills/think.md` | Skill 정의 (Markdown) | 저장 경로 `docs/01-thinking/` → `minto-docs/01-thinking/` |
| `.claude/skills/write.md` | Skill 정의 (Markdown) | 저장 경로 `docs/02-writing/` → `minto-docs/02-writing/` |
| `.claude/skills/solve.md` | Skill 정의 (Markdown) | 저장 경로 `docs/03-solving/` → `minto-docs/03-solving/` |
| `.claude/skills/express.md` | Skill 정의 (Markdown) | 저장 경로 `docs/04-expressing/` → `minto-docs/04-expressing/` |
| `.claude/agents/writing.md` | Agent 정의 (Markdown) | 템플릿 참조 `docs/templates/` → `minto-docs/templates/` |

### 6.2 Current Consumers

| Resource | Operation | Code Path | Impact |
|----------|-----------|-----------|--------|
| `docs/01-thinking/` | WRITE | orchestrator.md → think 라우팅 결과 저장 | Breaking (경로 변경) |
| `docs/02-writing/` | WRITE | orchestrator.md → write 라우팅 결과 저장 | Breaking (경로 변경) |
| `docs/03-solving/` | WRITE | orchestrator.md → solve 라우팅 결과 저장 | Breaking (경로 변경) |
| `docs/04-expressing/` | WRITE | orchestrator.md → express 라우팅 결과 저장 | Breaking (경로 변경) |
| `docs/templates/report-default.md` | READ | writing.md → 보고서 템플릿 로드 | Breaking (경로 변경) |
| `.minto-memory.json` savedTo 값 | READ | orchestrator.md → 메모리 업데이트 | Needs verification (기존 기록은 구 경로 유지) |

### 6.3 Verification

- [x] 변경 후 6개 파일 모두 `minto-docs/` 경로 사용 확인
- [x] `docs/templates/report-default.md` → `minto-docs/templates/report-default.md` 복사 완료
- [x] `.minto-memory.json` 기존 `savedTo` 값은 소급 수정 불필요 (신규 생성분부터 적용)

---

## 7. Architecture Considerations

### 7.1 Project Level Selection

| Level | Characteristics | Recommended For | Selected |
|-------|-----------------|-----------------|:--------:|
| **Starter** | Simple structure | Static sites, portfolios | ☑ |
| **Dynamic** | Feature-based modules | Web apps, SaaS MVPs | ☐ |
| **Enterprise** | Strict layer separation | High-traffic systems | ☐ |

> 이 작업은 프롬프트 파일(Markdown) 내 문자열 치환이므로 Starter 수준으로 충분.

### 7.2 Key Architectural Decisions

| Decision | Selected | Rationale |
|----------|----------|-----------|
| 새 루트 경로명 | `minto-docs/` | 사용자 확정 (`minto-doc/` 아님) |
| 카테고리 하위 구조 | 기존 유지 (`01-thinking/` 등) | 내부 구조 변경 없이 루트만 교체 |
| 템플릿 이동 방식 | Do 단계에서 복사 | 기존 `docs/templates/` 파일 유지 + `minto-docs/templates/`에 복사 |

---

## 8. Convention Prerequisites

### 8.1 Existing Project Conventions

- [x] `.claude/` 하위 agents/skills Markdown 파일로 에이전트 정의
- [ ] `CLAUDE.md` 코딩 컨벤션 섹션 없음
- [ ] ESLint/Prettier 미적용 (프롬프트 기반 프로젝트)

### 8.2 Conventions to Define/Verify

| Category | Current State | To Define | Priority |
|----------|---------------|-----------|:--------:|
| 경로 네이밍 | `docs/` (혼재) | minto 산출물은 `minto-docs/`, PDCA는 `docs/` | High |

---

## 9. Next Steps

완료됨.

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-06-17 | Initial draft | 박수민 |
| 1.0 | 2026-06-17 | Archived (PDCA complete) | 박수민 |
