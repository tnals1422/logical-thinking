---
tags: [pdca, plan, grok, native-layout, host-target]
feature: grok-native-layout
created: 2026-07-15
status: archived
archived: 2026-07-15
archivedTo: docs/archive/2026-07/grok-native-layout/
author: Struct Agent Team / Grok Build
project: logical-thingking (Struct Agent Team)
predecessor: docs/archive/2026-07/grok-parent-as-orchestrator/
decision: docs/decisions/20260715-host-target-grok-only.md
---

# grok-native-layout Planning Document

> **Summary**: Parent-as-Orchestrator 동작 전환 이후 잔존 Claude Code 레이아웃(`.claude/`, `CLAUDE.md`)을 Grok Build 네이티브(`.grok/`, `AGENTS.md`)로 Copy→Verify→Delete 이전한다.
>
> **Project**: logical-thingking (Struct Agent Team)  
> **Author**: Struct Agent Team / Grok Build  
> **Date**: 2026-07-15  
> **Status**: Confirmed (Design: Option C Pragmatic)

---

## Executive Summary

| Perspective | Content |
|-------------|---------|
| **Problem** | 런타임은 Grok-only·Parent-as-Orch로 동작하지만, 자산 경로는 CC 호환 레이아웃(`.claude/`, `CLAUDE.md`)에 남아 온보딩·문서·장기 유지보수에 CC 잔향이 남는다. |
| **Solution** | Full native: skills→`.grok/skills`, agents→`.grok/agents`, `AGENTS.md` 신설, active 경로 참조 갱신 후 `.claude`·`CLAUDE.md` 삭제 (Copy→Verify→Delete). |
| **Function/UX Effect** | `/struct-*` 동작·Brief/Review 게이트 불변. 슬래시 스킬·specialist 스폰은 네이티브 경로에서 동일하게 동작. 새 세션 온보딩이 Grok 용어 일관. |
| **Core Value** | `HOST-TARGET-2026-07` (`keep_cc_as_asset: false`)를 **레이아웃까지** 완성. 호환 스캐너 의존을 제거하고 Grok SoT를 단일화. |

---

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 동작 전환(PR0–PR4)은 끝났으나 물리 레이아웃이 CC 경로에 묶여 Grok-only 결정과 불일치 |
| **WHO** | 단독 운영자(Grok Build primary) · 에이전트 Parent/specialist · 향후 레포 기여자 |
| **RISK** | 이전 후 specialist/skill 미발견 → `/struct-*` 단절; 이중 로드(AGENTS+CLAUDE) 중복 컨텍스트 |
| **SUCCESS** | `.grok`+`AGENTS.md` only; V2/V3/V4 스모크 PASS; active 문서 `.claude` 참조 0; `.claude`·`CLAUDE.md` 삭제 |
| **SCOPE** | PR-N0 인벤토리 → PR-N1 복사·정규화 → PR-N2 경로 갱신 → PR-N3 검증 → PR-N4 삭제·문서 마무리 |

---

## 1. Overview

### 1.1 Purpose

Struct Agent Team의 **호스트 자산 레이아웃**을 Claude Code 호환 경로에서 **Grok Build 네이티브 경로**로 이전한다.  
로직(Parent-as-Orchestrator, specialist 본문, templates, reference)은 재사용하고, **배치 위치·프로젝트 규칙 파일명·active 문서 경로 표기**만 정리한다.

### 1.2 Background

| 시점 | 상태 |
|------|------|
| 2026-06 | CC 전제: `.claude/skills` + `.claude/agents` + `CLAUDE.md` |
| 2026-07-15 | `HOST-TARGET-2026-07` · `choice: grok-only` · Parent-as-Orchestrator 완료·archived |
| 동 사이클 non-goal | **당 스프린트에서 `.claude` 물리 삭제 금지** (잔류 + 스펙 재라벨) |
| Report Next | `.grok/` 네이티브 패키징 검토 (Low · if needed) |
| **본 사이클** | 사용자가 Full native + 이전 후 삭제를 **명시 선택** → non-goal 해제 |

**Grok 로더 사실 (user-guide 기준)**

| 자산 | CC/호환 경로 | Grok 네이티브 | 현재 레포 |
|------|--------------|---------------|-----------|
| Project rules | `CLAUDE.md` (호환 로드) | `AGENTS.md` | `CLAUDE.md` only · `AGENTS.md` 없음 |
| Skills | `./.claude/skills/` (compat, High) | `./.grok/skills/` (Highest) | `.claude/skills/struct-*` |
| Agents | 문서상 공식 SoT = `.grok/agents/` | `.grok/agents/` | `.claude/agents/*` (V2–V4에서 스폰 실증) |
| Project config | `.claude/settings.local.json` (CC) | `.grok/config.toml` (선택) | CC permissions only |
| Rules dir | `.claude/rules/` | `.grok/rules/` | 없음 |

**선행 사이클과의 관계**

```text
grok-parent-as-orchestrator (archived)
  = 오케스트레이션 계층 이동 (동작)
       │
       ▼
grok-native-layout (본 Plan)
  = 자산 레이아웃 네이티브화 (배치·온보딩)
```

### 1.3 Related Documents

| 문서 | 역할 |
|------|------|
| [HOST-TARGET grok-only](../../../decisions/20260715-host-target-grok-only.md) | Primary runtime 결정 |
| [Parent-as-Orch report (archive)](../grok-parent-as-orchestrator/grok-parent-as-orchestrator.report.md) | 동작 전환 완료 · 본 작업 carry-over |
| [CC entrypath notes](../cc-entrypath-notes.md) | 잔류 자산 정책 (본 사이클로 갱신 대상 아님 — archive) |
| [L1 design archive](../grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md) | Parent 인라인 프로세스 |
| Grok user-guide | `08-skills.md` · `12-project-rules.md` · `16-subagents.md` · `05-configuration.md` |

---

## 2. Scope

### 2.1 In Scope

- [ ] **PR-N0** 잔존 인벤토리 고정 (파일 목록·참조 맵·삭제 후보)
- [ ] **PR-N1** Copy: `.claude/skills/*` → `.grok/skills/*`, `.claude/agents/*` → `.grok/agents/*`
- [ ] **PR-N1b** Frontmatter 최소 정규화 (`name`/`description` 유지; CC-only `model: opus|sonnet` 제거 또는 Grok 비의존; `tools` 정리)
- [ ] **PR-N1c** `CLAUDE.md` → `AGENTS.md` (내용 이전 + 경로 표기를 `.grok/` 기준으로 갱신)
- [ ] **PR-N2** Active 문서 경로 일괄 갱신 (usage, active reference, 스킬 본문 내부 경로, decisions/known-issues 운영 요약 링크)
- [ ] **PR-N3** Verify: skill 발견 · specialist spawn · V2 think / V3 write / V4 review 스모크
- [ ] **PR-N4** Delete: `.claude/` 전체 · `CLAUDE.md` 제거 (검증 PASS 후)
- [ ] SKILL 내부 `orchestrator.md` / specialist 경로 문자열을 `.grok/agents/` 로 교체
- [ ] `settings.local.json` · `agent-memory/` 는 CC 전용 → 이전 없이 삭제 묶음에 포함

### 2.2 Out of Scope

| 항목 | 이유 |
|------|------|
| specialist / orchestrator **본문 로직 재작성** | 선행 사이클 non-goal 유지 · 본 사이클은 배치 |
| Parent-as-Orchestrator 재설계 | 이미 archived · 재사용 |
| write 후 auto Review Gate 강화 | 별 feature (선행 report Medium backlog) |
| `docs/archive/**` · `reference/archive/**` 본문 치환 | 역사 보존 (Active only) |
| Grok 플랫폼 requirements / depth 변경 요청 | non_goal |
| bkit 플러그인·MCP 레이아웃 변경 | 별 축 |
| `sumin-grok-assets` compose로 외부 유형 주입 | 본 레포 Struct 자산 이전과 무관 (충돌 시 사용 금지) |
| dual-host / CC 재지원 경로 | `keep_cc_as_asset: false` |

### 2.3 Confirmed Strategy (사용자 확정 2026-07-15)

| 축 | 선택 |
|----|------|
| 물리 이전 범위 | **Full native** (skills + agents + AGENTS.md) |
| `.claude` 최종 | **이전 후 삭제** |
| 이전 방식 | **Copy → Verify → Delete** |
| CLAUDE.md | **AGENTS.md로 이전 후 삭제** (stub 없음) |
| Frontmatter | **경로 이전 + 최소 정규화** |
| 문서 깊이 | **Active only** |

---

## 3. Requirements

### 3.1 Functional Requirements

| ID | Requirement | Priority | Status |
|----|-------------|----------|--------|
| FR-01 | 6× `struct-*` SKILL이 `.grok/skills/struct-*/SKILL.md` 에 존재하고 Grok가 슬래시/자동 호출로 발견 | High | Pending |
| FR-02 | 6 specialist + orchestrator(프로세스 SoT)가 `.grok/agents/*.md` 에 존재; Parent가 specialist `spawn_subagent` 가능 | High | Pending |
| FR-03 | 루트 `AGENTS.md` 가 아키텍처·스킬·파일 구조의 **유일한** 프로젝트 규칙 SoT | High | Pending |
| FR-04 | Active 문서(usage, active reference, AGENTS, SKILL 본문)에 `.claude/` · `CLAUDE.md` 경로 참조 **0건** | High | Pending |
| FR-05 | `/struct-think` → `struct-docs/01-thinking/` + memory 갱신 (V2 상당) | High | Pending |
| FR-06 | `/struct-write` → Brief/Spec + `02-writing/` + memory (V3 상당) | High | Pending |
| FR-07 | `/struct-review` → fidelity/DQ 동작 (V4 상당) | High | Pending |
| FR-08 | 검증 PASS 후 `.claude/` 디렉터리 및 `CLAUDE.md` **물리 삭제** | High | Pending |
| FR-09 | `orchestrator.md` 는 계속 **스폰 금지 · Parent 프로세스 스펙** (배너 유지, 경로만 변경) | High | Pending |
| FR-10 | archive 문서 본문은 수정하지 않음 (필요 시 다음 사이클 supersede 노트) | Medium | Pending |

### 3.2 Non-Functional Requirements

| Category | Criteria | Measurement Method |
|----------|----------|-------------------|
| Safety | Copy 단계에서는 원본 유지 · Delete는 검증 후만 | PR-N1 완료 시점에 `.claude` 존재 확인 |
| Rollback | Delete 전 git commit 또는 사용자가 복구 가능한 상태 | PR-N3 직전 checkpoint commit 권장 |
| Idempotency | 재실행 시 덮어쓰기 정책 명시 (기존 `.grok` 비어 있음 전제) | 시작 시 `.grok` 부재 검증 |
| Context hygiene | `AGENTS.md`와 `CLAUDE.md` 동시 존재 구간 최소화 | Delete 단계에서 CLAUDE 제거 |
| Docs consistency | Active 경로 표기 단일 | `rg "\.claude/" --glob '!docs/archive/**' --glob '!reference/archive/**'` |
| Compatibility independence | Claude skills/agents compat 꺼도 동작 | (선택) 검증 시 가정 문서화; 강제 설정 변경은 비목표 |

### 3.3 Inventory (As-Is → To-Be)

#### 3.3.1 이동 대상

| As-Is | To-Be | 비고 |
|-------|-------|------|
| `.claude/skills/struct-think/SKILL.md` | `.grok/skills/struct-think/SKILL.md` | + 내부 경로 문자열 |
| `.claude/skills/struct-write/SKILL.md` | `.grok/skills/struct-write/SKILL.md` | 동일 |
| `.claude/skills/struct-solve/SKILL.md` | `.grok/skills/struct-solve/SKILL.md` | 동일 |
| `.claude/skills/struct-express/SKILL.md` | `.grok/skills/struct-express/SKILL.md` | 동일 |
| `.claude/skills/struct-review/SKILL.md` | `.grok/skills/struct-review/SKILL.md` | 동일 |
| `.claude/skills/struct-research/SKILL.md` | `.grok/skills/struct-research/SKILL.md` | 동일 |
| `.claude/agents/thinking.md` | `.grok/agents/thinking.md` | frontmatter 최소 정규화 |
| `.claude/agents/writing.md` | `.grok/agents/writing.md` | 동일 |
| `.claude/agents/problem-solving.md` | `.grok/agents/problem-solving.md` | 동일 |
| `.claude/agents/expression.md` | `.grok/agents/expression.md` | 동일 |
| `.claude/agents/review.md` | `.grok/agents/review.md` | 동일 |
| `.claude/agents/research.md` | `.grok/agents/research.md` | 동일 |
| `.claude/agents/orchestrator.md` | `.grok/agents/orchestrator.md` | **spawn 금지 배너 유지** |
| `CLAUDE.md` | `AGENTS.md` | 구조 트리·경로 표기 갱신 후 CLAUDE 삭제 |

#### 3.3.2 삭제만 (이전 없음)

| Path | Reason |
|------|--------|
| `.claude/settings.local.json` | CC permissions 전용 |
| `.claude/agent-memory/` | CC agent memory |
| `.claude/` (empty after move) | 디렉터리 자체 제거 |

#### 3.3.3 불변 (이전 대상 아님)

| Path | Reason |
|------|--------|
| `struct-docs/**` 산출·templates | 콘텐츠 SoT 유지 |
| `reference/**` (본문 로직) | 경로 문자열만 active에서 갱신 |
| `.struct-memory.json` | 세션 메모리 계약 유지 |
| `docs/archive/**` | 역사 보존 |

#### 3.3.4 Active 경로 갱신 후보 (In Scope)

| Area | Examples |
|------|----------|
| 프로젝트 규칙 | `AGENTS.md` (from CLAUDE) |
| Usage | `struct-docs/usage/index.md`, `write.md`, `review.md`, (필요 시 think/solve/express/research) |
| Workflow | `struct-docs/usage/workflow.mmd` 주석/라벨 |
| SKILL 본문 | 6× `.grok/skills/...` 내부 `.claude/agents` → `.grok/agents` |
| Active reference | `reference/agent-shared-contract.md`, `reference/deliverable-brief-schema.md` |
| 운영 링크 | `docs/decisions/*` stub 요약, `docs/known-issues/README.md` |
| 활성 설계 문서 (선택·권장) | `docs/struct-deliverable-system.design.md`, `docs/struct-usage-scenarios.md` 등 **비-archive** 문서의 경로 표기 |

**제외**: `docs/archive/**`, `reference/archive/**`

---

## 4. Success Criteria

### 4.1 Definition of Done

- [ ] `.grok/skills/struct-*/SKILL.md` 6개 존재
- [ ] `.grok/agents/{thinking,writing,problem-solving,expression,review,research,orchestrator}.md` 7개 존재
- [ ] 루트 `AGENTS.md` 존재 · `CLAUDE.md` 부재
- [ ] `.claude/` 디렉터리 부재
- [ ] Active 경로 `rg` 기준 `.claude/` 참조 0 (제외 글로브: archive)
- [ ] V2 think · V3 write · V4 review 스모크 PASS (신규 산출물 또는 재실행 로그)
- [ ] Parent-as-Orch 불변: primary에서 `orchestrator` spawn 없음
- [ ] Plan/Design 대비 구현 추적 가능 (본 문서 FR 체크)

### 4.2 Quality Criteria

- [ ] specialist 본문 의미 변경 없음 (diff ≈ path + frontmatter only)
- [ ] Copy 구간에서 원본 손상 없음
- [ ] Delete 전 검증 실패 시 삭제 금지 (게이트)
- [ ] 이중 규칙 파일 장기 공존 없음 (최종 상태)

### 4.3 Smoke Checklist (PR-N3)

| ID | Check | Pass 조건 |
|----|-------|-----------|
| S1 | Skill discovery | `/struct-think` 등 스킬 목록·호출 가능 |
| S2 | Agent spawn | `subagent_type: thinking` (및 write 경로 writing) 성공 |
| S3 | V2 | think 파일 + `.struct-memory.json` previousThoughts |
| S4 | V3 | write 파일 + Brief 메타 (필요 시) + memory |
| S5 | V4 | review 파일 · fidelity 게이트 실행 |
| S6 | No orch spawn | 스킬 실행 중 orchestrator 자식 스폰 0 |
| S7 | Path hygiene | active `.claude` 참조 0 · CLAUDE 부재 |

---

## 5. Risks and Mitigation

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| R1. `.grok/agents` 미로드로 specialist spawn 실패 | High | Medium | Copy 후 S2 즉시 검증; 실패 시 Delete 금지·`.claude` 유지 롤백 |
| R2. skills 우선순위 충돌 (`.claude`+`.grok` 공존 구간) | Medium | High (Copy 중) | 공존은 의도적 단기; Verify 후 즉시 Delete; 동일 name dedupe 이해 |
| R3. CLAUDE+AGENTS 이중 로드로 지시 중복 | Medium | High (중간 상태) | AGENTS 작성 직후 CLAUDE 삭제 단계를 가깝게; 최종 stub 없음 |
| R4. Frontmatter 정규화로 에이전트 미인식 | High | Low–Med | 최소 변경; `name`/`description` 보존; 정규화 전/후 spawn 비교 |
| R5. Active 문서 누락 참조 | Low | Medium | `rg` 체크리스트 S7; 누락은 follow-up 패치 |
| R6. archive 문서 과잉 수정 | Low | Low | Out of scope 강제; 스크립트 치환 시 글로브 제외 |
| R7. Delete 후 미커밋 유실 | Medium | Low | PR-N3 직전 checkpoint commit 권장 (사용자 승인 후) |
| R8. `model: opus` 등 잔존으로 혼란 | Low | Medium | 최소 정규화로 제거; Grok 기본 모델 상속 |

---

## 6. Impact Analysis

### 6.1 Changed Resources

| Resource | Type | Change Description |
|----------|------|--------------------|
| `.claude/skills/*` | Skill package | Copy → `.grok/skills/*` 후 삭제 |
| `.claude/agents/*` | Agent definition | Copy → `.grok/agents/*` 후 삭제 |
| `CLAUDE.md` | Project rules | `AGENTS.md`로 이전 후 삭제 |
| Active docs / SKILL paths | Documentation | `.claude` → `.grok` 문자열 |
| `.claude/settings.local.json` | CC config | 삭제 |
| `.claude/agent-memory/` | CC memory | 삭제 |

### 6.2 Current Consumers

| Resource | Operation | Code Path | Impact |
|----------|-----------|-----------|--------|
| Skills | LOAD | Grok skill scanner (`.claude` → `.grok`) | 경로 이전 필수 |
| Agents | SPAWN | Parent SKILL → `spawn_subagent(subagent_type=…)` | agent 파일 위치 |
| orchestrator.md | READ | Parent 인라인 Read | SKILL/usage 경로 문자열 |
| Project rules | LOAD | Grok rules discovery | CLAUDE→AGENTS |
| usage/* | READ | 인간·에이전트 온보딩 | 경로 표기 |
| reference/* | READ | writing/review 계약 | 일부 경로 표기 |
| archive/* | READ | 역사 | **변경 없음** |
| struct-docs templates | READ | write 파이프라인 | **변경 없음** |
| .struct-memory.json | R/W | Parent memory | **계약 유지** |

### 6.3 Verification

- [ ] All consumers listed above verified after Copy
- [ ] No dual CLAUDE+AGENTS in final state
- [ ] No skill name collision after Delete
- [ ] Spawn types still: thinking, writing, problem-solving, expression, review, research
- [ ] orchestrator remains non-primary-spawn

---

## 7. Architecture Considerations

### 7.1 Project Level Selection

| Level | Selected | Note |
|-------|:--------:|------|
| **Starter** (레이아웃 이전 관점) | ☑ | 앱 코드 레벨이 아니라 **에이전트 자산 패키징** 작업. 최소 구조 변경. |

### 7.2 Key Architectural Decisions

| Decision | Options | Selected | Rationale |
|----------|---------|----------|-----------|
| Layout target | keep `.claude` / dual / full `.grok` | **full `.grok`** | 사용자 확정 · HOST-TARGET 정합 |
| Migration mode | big-bang / symlink / copy-verify-delete | **copy-verify-delete** | 롤백·검증 게이트 |
| Rules file | CLAUDE only / dual / AGENTS only | **AGENTS only** | 이중 로드 방지 |
| Frontmatter | keep / minimal / rewrite | **minimal** | 스폰 안정성 |
| Doc rewrite | all-repo / active / none | **active only** | 역사 보존 |
| orchestrator location | agents/ vs rules/ vs reference/ | **`.grok/agents/orchestrator.md`** | 기존 SoT 연속; spawn 배너로 구분 |
| settings.local | migrate / drop | **drop** | Grok 미사용 |

### 7.3 Target Layout

```text
logical-thingking/
├── AGENTS.md                 # Project rules (was CLAUDE.md)
├── .grok/
│   ├── skills/
│   │   ├── struct-think/SKILL.md
│   │   ├── struct-write/SKILL.md
│   │   ├── struct-solve/SKILL.md
│   │   ├── struct-express/SKILL.md
│   │   ├── struct-review/SKILL.md
│   │   └── struct-research/SKILL.md
│   └── agents/
│       ├── orchestrator.md   # Process SoT — DO NOT spawn
│       ├── thinking.md
│       ├── writing.md
│       ├── problem-solving.md
│       ├── expression.md
│       ├── review.md
│       └── research.md
├── struct-docs/              # unchanged roles
├── reference/                # path strings updated in active files only
├── .struct-memory.json
└── (no .claude/, no CLAUDE.md)
```

### 7.4 Runtime Flow (불변)

```text
/struct-* skill (.grok/skills)
    → Parent inlines orchestrator.md process (.grok/agents)
    → spawn specialist only (.grok/agents/*)
    → save struct-docs + memory
```

---

## 8. Implementation Plan (PR breakdown)

### PR-N0 — Inventory freeze

- [ ] 본 Plan 인벤토리 표와 디스크 목록 diff
- [ ] active `.claude` 참조 `rg` 베이스라인 저장 (주석 또는 본 절에 카운트)

### PR-N1 — Copy + minimal normalize

- [ ] `mkdir` `.grok/skills`, `.grok/agents`
- [ ] 6 skills · 7 agents 복사
- [ ] Frontmatter: `model: opus|sonnet` 제거 또는 주석; `tools: Agent, Read, Write` → Grok 관점에서 무시 가능 시 유지 또는 단순화 (Design에서 확정)
- [ ] orchestrator 배너 경로 문구만 갱신 준비

### PR-N1c — AGENTS.md

- [ ] CLAUDE 내용 기반 AGENTS 작성
- [ ] 파일 구조 블록을 `.grok/` 기준으로 교체
- [ ] 동기화 문구 (writing.md 등) 경로 갱신

### PR-N2 — Path rewrite (active)

- [ ] 6× SKILL 내부 경로
- [ ] usage/*
- [ ] active reference 언급
- [ ] decisions/known-issues 운영 한 줄
- [ ] (권장) 비-archive `docs/struct-*.md` 경로 표기

### PR-N3 — Verify

- [ ] S1–S7 체크리스트
- [ ] 실패 시 Delete 금지 · 갭 기록 후 재검증

### PR-N4 — Delete + close

- [ ] `.claude/` 삭제
- [ ] `CLAUDE.md` 삭제
- [ ] 최종 `rg` 확인
- [ ] (선택) checkpoint commit
- [ ] Design/Do 대비 Report 준비

**권장 세션 분할**

| Session | Modules |
|---------|---------|
| S1 | PR-N0 + PR-N1 + PR-N1c (복사·AGENTS, 아직 삭제 금지) |
| S2 | PR-N2 + PR-N3 (경로·검증) |
| S3 | PR-N4 (삭제·마무리) — **PR-N3 PASS 필수** |

---

## 9. Open Questions → Design

| # | Question | Plan default | Design에서 확정 |
|---|----------|--------------|-----------------|
| Q1 | agent `tools` 필드 완전 삭제 vs 유지? | 유지하되 CC 도구명 주석 | frontmatter 스키마 샘플 |
| Q2 | `docs/struct-*.md` 비-archive 설계 문서를 active에 포함할 범위 | 권장 포함 | 파일 리스트 고정 |
| Q3 | Delete 직전 필수 git commit 여부 | 권장 (사용자 승인) | Do 체크포인트 |
| Q4 | 스모크용 신규 산출물 파일명 규칙 | `20260715-grok-native-layout-*-path.md` | 확정 |
| Q5 | orchestrator를 agents 밖에 둘지 | agents 유지 | 대안 비교 1페이지면 충분 |

---

## 10. Dependencies & Prerequisites

| Dep | Status |
|-----|--------|
| grok-parent-as-orchestrator archived | ✅ Done |
| HOST-TARGET grok-only | ✅ Confirmed |
| Grok Build skill/agent discovery | ✅ Documented + prior V* proof on `.claude` paths |
| Empty `.grok/` at start | ✅ (2026-07-15 조사: 부재) |
| No existing `AGENTS.md` | ✅ |

---

## 11. Effort Estimate

| Phase | Effort |
|-------|--------|
| Plan (본 문서) | 0.25d |
| Design | 0.5d |
| Do (copy+paths+verify+delete) | 0.5–1d |
| Check/Report | 0.25d |
| **Total** | **~1.5–2d** (단일 운영자, 스모크 포함) |

---

## 12. Decision Record (Plan phase)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Feature name | `grok-native-layout` | 사용자 확정 |
| Scope | Full native + delete `.claude` | `keep_cc_as_asset: false` 완성 |
| Migration | Copy→Verify→Delete | 안전 게이트 |
| Rules | AGENTS only | 중복 로드 방지 |
| Frontmatter | Minimal normalize | 스폰 안정성 |
| Docs | Active only | 역사 왜곡 방지 |
| Logic rewrite | Out of scope | 선행 사이클과 분리 |

**Decision Deviation from prior non-goal**: 선행 사이클의 “`.claude` 물리 삭제 금지”는 **당시 스프린트 한정**이었음. 본 사이클에서 사용자가 명시적으로 해제.

---

## 13. Next Phase

```text
/pdca design grok-native-layout
```

Design에서 다룰 것:

1. Frontmatter 정규화 정확한 스키마 (before/after 예시)
2. 파일 단위 작업 체크리스트 (Module Map / Session Guide)
3. 롤백 절차 (Delete 전/후)
4. 검증 산출물 경로·명령 시퀀스
5. Active 문서 파일 목록 확정

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-07-15 | Draft Plan — 사용자 확정 범위 반영 | Struct Agent Team / Grok |
