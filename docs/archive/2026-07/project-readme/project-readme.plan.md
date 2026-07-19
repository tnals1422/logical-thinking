---
tags: [pdca, plan, readme, onboarding, documentation]
feature: project-readme
created: 2026-07-19
status: draft
author: Struct Agent Team / Grok Build
project: logical-thinking (Struct Agent Team)
status: archived
archived: 2026-07-19
archivedTo: docs/archive/2026-07/project-readme/
---

# project-readme Planning Document

> **Summary**: 프로젝트 루트 `README.md`를 신설해 온보딩 진입점(소개·Quick Start·스킬·구조·문서 링크)을 단일화한다.
>
> **Project**: logical-thinking (Struct Agent Team)  
> **Author**: Struct Agent Team / Grok Build  
> **Date**: 2026-07-19  
> **Status**: Confirmed (Design: Option C Pragmatic)

---

## Executive Summary

| Perspective | Content |
|-------------|---------|
| **Problem** | 루트 `README.md`가 없어 신규 기여자·본인 재진입 시 프로젝트 목적·구조·첫 사용법이 분산된 문서(`AGENTS.md`, `struct-docs/usage/`)에 흩어져 있다. |
| **Solution** | 한국어·개조식 표준 온보딩 README를 루트에 작성하고, 상세 내용은 기존 문서에 **요약 + 링크**로 연결한다. Primary runtime은 Grok Build only. |
| **Function/UX Effect** | 5분 내 “무엇을 하는 프로젝트인지 + 첫 `/struct-*` 실행” 파악 가능. 스킬 6종·디렉터리 맵·주요 문서 경로가 한 화면에 정리된다. |
| **Core Value** | 저장소의 **단일 온보딩 SoT(루트 README)** 확보. 운영 문서(`AGENTS.md`)와 사용 가이드(`struct-docs/usage/`)의 역할을 분리·연결. |

---

## Context Anchor

> Auto-generated from Executive Summary. Propagated to Design/Do documents for context continuity.

| Key | Value |
|-----|-------|
| **WHY** | 루트 진입점 부재로 온보딩 비용·문서 분산 |
| **WHO** | 본인 재진입 + 협업 기여자 (Grok Build primary) |
| **RISK** | AGENTS/usage와 중복·불일치; 과도한 장문으로 유지보수 부담 |
| **SUCCESS** | 5분 온보딩 가능; 스킬 6종·구조·링크 완비; 상세는 링크로 위임 |
| **SCOPE** | 루트 README 1파일 신설 (코드/스킬 로직 변경 없음) |

---

## 1. Overview

### 1.1 Purpose

Struct Agent Team (Minto 기반 구조화 논리 에이전트 팀) 저장소의 **첫 읽기 문서**로 루트 `README.md`를 제공한다.

- 프로젝트 한 줄 정의 + 핵심 가치
- Grok Build 기준 Quick Start
- `/struct-*` 스킬 맵
- 디렉터리·산출물 위치
- 상세 문서로의 허브 링크

### 1.2 Background

| 현황 | 설명 |
|------|------|
| 루트 README | **없음** |
| 프로젝트 규칙 | `AGENTS.md` — 에이전트/아키텍처/운영 규칙 (상세·내부 지향) |
| 사용 가이드 | `struct-docs/usage/` — 커맨드별 상세 |
| Runtime 결정 | Grok-only · Parent-as-Orchestrator (`docs/decisions/20260715-host-target-grok-only.md`) |
| 네이티브 레이아웃 | `.grok/skills`, `.grok/agents` 완료 (archive: grok-native-layout) |

**문제 구조**

```text
신규 방문자 / 재진입
        │
        ▼
   [README 없음] ──► AGENTS.md (길다, 규칙 중심)
        │            usage/* (커맨드별 분산)
        │            archive/docs (이력 탐색 필요)
        ▼
   “어디서 시작하지?” 마찰
```

**해결 방향**

```text
README.md (얇은 허브)
  ├─ 소개 · Quick Start · 스킬 표
  ├─ 구조 맵 (한눈에)
  └─ 링크 → AGENTS.md / usage / decisions / archive
```

### 1.3 Related Documents

| 문서 | 역할 |
|------|------|
| [AGENTS.md](../../../AGENTS.md) | 프로젝트 규칙 · 아키텍처 SoT (내용 출처) |
| [struct-docs/usage/index.md](../../../struct-docs/usage/index.md) | 커맨드 인덱스 (내용 출처) |
| [HOST-TARGET grok-only](../../decisions/20260715-host-target-grok-only.md) | Primary runtime 결정 |
| [Parent-as-Orch archive](../../archive/2026-07/grok-parent-as-orchestrator/) | 오케스트레이션 설계·report |
| [Grok native layout archive](../../archive/2026-07/grok-native-layout/) | 레이아웃 이전 이력 |
| [workflow.mmd](../../../struct-docs/usage/workflow.mmd) | 전체 워크플로우 다이어그램 |
| [templates/README.md](../../../struct-docs/templates/README.md) | 템플릿 선택 SoT |

**Checkpoint 기록**

| CP | 결과 |
|----|------|
| CP1 요구사항 확인 | 승인 — 표준 온보딩 README, 코드/스킬 변경 없음 |
| CP2 언어/톤 | 한국어 · 실무 개조식 |
| CP2 독자 | 본인 재진입 + 협업 기여자 |
| CP2 깊이 | 표준 온보딩 |
| CP2 Runtime | Grok Build only (CC는 archive 링크 수준) |

---

## 2. Scope

### 2.1 In Scope

- [ ] 루트 `README.md` **신규 생성** (단일 파일)
- [ ] 섹션 구성 (표준 온보딩):
  - [ ] 프로젝트 소개 (한 줄 + 3~5 bullet)
  - [ ] Quick Start (Grok Build 전제, 최소 전제조건 + 첫 커맨드 예시)
  - [ ] Skills 표 (`/struct-think` ~ `/struct-research` 6종)
  - [ ] 디렉터리/산출물 구조 (ASCII 트리 또는 표)
  - [ ] 워크플로 요약 (자주 쓰는 흐름 3~5개, usage 링크)
  - [ ] Runtime 노트 (Grok · Parent-as-Orchestrator, 상세는 decision/archive)
  - [ ] 문서 허브 링크 (AGENTS, usage, templates, decisions, known-issues, archive)
- [ ] 기존 문서와 **역할 분리** 문구 (README = 진입, AGENTS = 규칙, usage = 상세)
- [ ] 개조식 · 표 · 짧은 다이어그램 기본 스타일 적용

### 2.2 Out of Scope

- 애플리케이션/백엔드 코드 추가·수정
- `.grok/skills` · `.grok/agents` 로직 변경
- `AGENTS.md` 전면 재작성 (필요 시 “See also README” 한 줄 링크만 Design에서 선택 가능)
- 영문 전용/이중 언어 README
- 설치 스크립트, CI 배지, 스크린샷/데모 영상 제작
- Claude Code 동등 진입 경로 상세 가이드 (archive 링크만)
- Contributing 가이드·라이선스 정책 신설 (미요청)

---

## 3. Requirements

### 3.1 Functional Requirements

| ID | Requirement | Priority | Status |
|----|-------------|----------|--------|
| FR-01 | 루트에 `README.md`가 존재하고 GitHub/로컬에서 즉시 열람 가능 | High | Pending |
| FR-02 | 프로젝트 목적 1문장 + 핵심 가치 bullet 포함 | High | Pending |
| FR-03 | Quick Start: Grok Build 전제 + 최소 1개 실행 예시 (`/struct-think` 등) | High | Pending |
| FR-04 | 스킬 6종 표 (명령어 · 한 줄 설명 · 상세 링크) | High | Pending |
| FR-05 | 디렉터리 맵: `.grok/`, `struct-docs/`, `docs/`, `reference/` 등 | High | Pending |
| FR-06 | 자주 쓰는 워크플로 3개 이상 + `struct-docs/usage/` 링크 | Medium | Pending |
| FR-07 | Runtime: Grok-only · Parent-as-Orchestrator 요약 + decision/archive 링크 | Medium | Pending |
| FR-08 | 문서 허브 섹션: AGENTS, usage, templates, decisions, known-issues | Medium | Pending |
| FR-09 | 상세 장문 복제 금지 — 본문은 요약, 깊이는 링크로 위임 | High | Pending |
| FR-10 | 한국어 · 개조식(표/불릿) 톤 유지 | High | Pending |

### 3.2 Non-Functional Requirements

| Category | Criteria | Measurement Method |
|----------|----------|-------------------|
| 가독성 | 스크롤 없이 상단 1화면에서 소개+스킬 표 파악 가능 (대략 80~150줄 목표, hard limit 아님) | 목차/섹션 길이 리뷰 |
| 유지보수성 | AGENTS/usage 변경 시 README는 링크·표 헤더 수준 동기화만 필요 | Impact 목록 대비 |
| 일관성 | 경로·커맨드명이 AGENTS.md / usage/index.md 와 일치 | 수동 diff 체크리스트 |
| 접근성 | 마크다운 표준, 이모지 과다 사용 지양, 표·헤딩 계층 명확 | 렌더 미리보기 |
| 범위 준수 | 코드/스킬 파일 0 변경 (README ± optional AGENTS 1줄) | git status |

---

## 4. Success Criteria

### 4.1 Definition of Done

- [ ] `README.md`가 저장소 루트에 커밋 가능한 상태로 존재
- [ ] FR-01 ~ FR-10 충족 (Design 체크리스트로 검증)
- [ ] 스킬 6종·주요 경로가 AGENTS.md / usage/index.md 와 불일치 없음
- [ ] Out of Scope 항목 미포함 확인
- [ ] Plan → Design → Do 문서 체인 완료 후 Check(문서 gap) 통과

### 4.2 Quality Criteria

- [ ] 5분 온보딩 시나리오: 소개 이해 → 스킬 1개 선택 → 상세 문서 링크 도달
- [ ] “중복 장문” 없음: 동일 내용이 AGENTS와 문단 단위로 복제되지 않음
- [ ] 깨진 상대 경로 링크 0
- [ ] 린트/빌드 해당 없음 (문서 전용 feature) — 링크·구조 리뷰로 대체

### 4.3 Acceptance Scenarios

| # | 시나리오 | 기대 결과 |
|---|---------|-----------|
| A1 | 기여자가 클론 후 README만 읽음 | 프로젝트 목적·스킬 목록·다음 읽을 문서 파악 |
| A2 | 재진입자가 “think → write” 흐름을 찾음 | 워크플로 섹션 또는 usage 링크에서 확인 |
| A3 | Runtime 정책을 확인 | Grok-only 요약 + decision 링크 |
| A4 | 상세 규칙이 필요 | AGENTS.md 링크로 이동 (README에 규칙 전문 없음) |

---

## 5. Risks and Mitigation

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| AGENTS/usage와 내용 불일치 (스킬 설명·경로) | High | Medium | 작성 시 AGENTS + usage/index를 **단일 출처**로 대조; Do 체크리스트에 경로 검증 |
| README가 과도하게 길어져 유지보수 부담 | Medium | Medium | FR-09: 요약+링크 원칙; Design에서 섹션 길이 가이드 |
| 상세 복제로 AGENTS 역할 모호 | Medium | Low | 역할 분리 문구 명시; 규칙 본문은 AGENTS 전용 |
| Runtime/CC 정보 혼재 | Medium | Low | Grok-only 고정; CC는 archive 한 줄 |
| 향후 스킬 추가 시 README 미갱신 | Low | High | “스킬 표는 AGENTS/usage와 동기화” 유지 메모 1줄 |

---

## 6. Impact Analysis

### 6.1 Changed Resources

| Resource | Type | Change Description |
|----------|------|--------------------|
| `README.md` (root) | Doc (new) | 온보딩 허브 문서 신설 |
| `AGENTS.md` | Doc (optional) | Design 선택: 상단에 README 링크 1줄 추가 가능 (기본 권장: yes, minimal) |
| `.grok/*`, skills, agents | Code/Config | **변경 없음** |
| `struct-docs/usage/*` | Doc | **변경 없음** (링크 대상) |

### 6.2 Current Consumers

| Resource | Operation | Code Path | Impact |
|----------|-----------|-----------|--------|
| (없음) README | READ | GitHub UI, 로컬 열람, 온보딩 | None → New |
| AGENTS.md | READ | Grok project rules 로드 | Optional 1줄 링크 시 무해 |
| usage/* | READ | 사용자·에이전트 참조 | README에서 유입 증가 가능 (긍정적) |
| decisions/archive | READ | 문서 탐색 | 링크 추가, 내용 변경 없음 |

### 6.3 Verification

- [ ] 기존 스킬 실행 경로 불변 확인 (문서 전용 변경)
- [ ] AGENTS 수정 시 규칙 본문 의미 변경 없음
- [ ] 상대 링크 렌더 확인 (GitHub 기준 경로)

---

## 7. Architecture Considerations

### 7.1 Project Level Selection

| Level | Characteristics | Recommended For | Selected |
|-------|-----------------|-----------------|:--------:|
| **Starter** | 단순 문서/정적 산출 | 단일 README 문서 작업 | ☑ |
| **Dynamic** | Feature 모듈 + BaaS | — | ☐ |
| **Enterprise** | 계층 분리·마이크로서비스 | — | ☐ |

> 본 feature는 **문서 전용 Starter급 산출물**이다. 앱 아키텍처 변경 없음.

### 7.2 Key Architectural Decisions

| Decision | Options | Selected | Rationale |
|----------|---------|----------|-----------|
| 문서 역할 모델 | 단일 거대 README / 허브+링크 / AGENTS 대체 | **허브+링크** | 유지보수·SoT 분리 |
| 언어 | KO / EN / 이중 | **한국어** | 프로젝트 문서화 규칙 |
| Runtime 서술 | Grok only / Grok+CC 병기 | **Grok only** | HOST-TARGET 결정 일치 |
| 깊이 | 최소 / 표준 / 상세 | **표준 온보딩** | CP2 확정 |
| AGENTS 수정 | 없음 / 1줄 링크 | Design에서 확정 (Plan 권장: 1줄) | 양방향 발견성 |
| 다이어그램 | Mermaid / ASCII / 없음 | **ASCII (+ 선택적 Mermaid 링크)** | 렌더 호환·개조식 |

### 7.3 Content Architecture (문서 골격)

```text
README.md
├── # Title + one-liner
├── ## 이 프로젝트는?
├── ## Quick Start          (Grok Build)
├── ## 스킬 (Commands)
├── ## 디렉터리 구조
├── ## 자주 쓰는 흐름
├── ## Runtime & 아키텍처 요약
├── ## 문서 허브
└── ## 유지보수 메모 (짧게)
```

**출처 매핑**

| README 섹션 | Primary source |
|-------------|----------------|
| 소개·아키텍처 요약 | AGENTS.md |
| 스킬 표 | AGENTS.md + usage/index.md |
| 구조 트리 | AGENTS.md |
| 워크플로 | usage/index.md + workflow.mmd |
| Runtime | decisions + archive 2026-07 |

### 7.4 Clean Architecture Approach

```
Selected Level: Starter (documentation-only)

Folder Structure Preview:
┌─────────────────────────────────────────────────────┐
│ CREATE:  README.md                                  │
│ OPTIONAL: AGENTS.md (1-line "See README" link)      │
│ UNCHANGED: .grok/, struct-docs/, docs/, reference/  │
└─────────────────────────────────────────────────────┘
```

---

## 8. Convention Prerequisites

### 8.1 Existing Project Conventions

- [x] `AGENTS.md` has coding/project conventions
- [ ] `docs/01-plan/conventions.md` exists (Phase 2 output) — N/A for this feature
- [ ] `CONVENTIONS.md` at project root — N/A
- [ ] ESLint / Prettier / TypeScript — N/A (문서 전용)
- [x] 문서화 언어: 한국어 (AGENTS / Claude 규칙)
- [x] 기본 출력 스타일: 개조식 + 표 + 다이어그램

### 8.2 Conventions to Define/Verify

| Category | Current State | To Define | Priority |
|----------|---------------|-----------|:--------:|
| **Naming** | 영문 kebab feature 명 | `project-readme` 유지 | High |
| **Folder structure** | docs/01-plan/features/ | Plan 경로 준수 | High |
| **README 링크 스타일** | 상대 경로 | 루트 기준 상대 경로 통일 | High |
| **역할 분리** | 암묵적 | README vs AGENTS vs usage 문구 | High |
| **이모지** | AGENTS 일부 사용 | README는 절제 (표 위주) | Low |

### 8.3 Environment Variables Needed

| Variable | Purpose | Scope | To Be Created |
|----------|---------|-------|:-------------:|
| — | 문서 전용 feature | — | ☐ |

### 8.4 Pipeline Integration

| Phase | Status | Document Location | Command |
|-------|:------:|-------------------|---------|
| Phase 1 (Schema) | N/A | — | — |
| Phase 2 (Convention) | N/A | — | — |

문서 전용 feature로 9-phase app pipeline 비적용.

---

## 9. Next Steps

1. [ ] `/pdca design project-readme` — README 섹션 스펙·목차·링크 표·작성 가이드
2. [ ] Design에서 3안(최소 골격 / 표준 / 상세 확장) 중 표준 확정
3. [ ] `/pdca do project-readme` — `README.md` 작성
4. [ ] `/pdca analyze project-readme` — FR·링크·일치성 gap 점검
5. [ ] 필요 시 iterate → report → archive

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-07-19 | Initial draft (CP1–2 confirmed) | Struct Agent Team / Grok Build |
