---
tags: [pdca, design, grok, native-layout, host-target]
feature: grok-native-layout
created: 2026-07-15
status: archived
archived: 2026-07-15
archivedTo: docs/archive/2026-07/grok-native-layout/
author: Struct Agent Team / Grok Build
project: logical-thingking (Struct Agent Team)
architecture: Option C — Pragmatic
plan: docs/archive/2026-07/grok-native-layout/grok-native-layout.plan.md
---

# grok-native-layout Design Document

> **Summary**: Claude Code 레이아웃(`.claude/`, `CLAUDE.md`)을 Grok Build 네이티브(`.grok/`, `AGENTS.md`)로 Copy→Verify→Delete 이전하는 구현 설계 (Option C Pragmatic).
>
> **Project**: logical-thingking (Struct Agent Team)  
> **Author**: Struct Agent Team / Grok Build  
> **Date**: 2026-07-15  
> **Status**: Archived (2026-07-15)  
> **Planning Doc**: [grok-native-layout.plan.md](./grok-native-layout.plan.md)

### Pipeline References

| Phase | Document | Status |
|-------|----------|--------|
| Plan | [grok-native-layout.plan.md](./grok-native-layout.plan.md) | ✅ |
| Predecessor | [grok-parent-as-orchestrator archive](../grok-parent-as-orchestrator/) | ✅ archived |
| Decision | [HOST-TARGET grok-only stub](../../../decisions/20260715-host-target-grok-only.md) | ✅ |
| App schema/API/UI | N/A (에이전트 자산 패키징) | N/A |

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

### 1.1 Design Goals

1. **단일 SoT 레이아웃**: 프로젝트 skills/agents/rules가 Grok 네이티브 경로에만 존재
2. **동작 불변**: Parent-as-Orchestrator · specialist spawn · Brief/Review/memory 계약 유지
3. **안전 이전**: Copy 중 원본 유지 → 검증 게이트 → Delete (롤백 가능 구간 확보)
4. **최소 정규화**: 본문 로직 불변 · frontmatter는 Grok 인식에 필요한 최소 변경만
5. **Active-only 문서**: archive 역사 보존

### 1.2 Design Principles

- **Move placement, not logic** — specialist/orchestrator 본문은 path/frontmatter 외 변경 금지
- **Gate before destroy** — PR-N3 FAIL 시 PR-N4 금지
- **No dual final state** — 최종에 `CLAUDE.md` + `AGENTS.md` 또는 `.claude` + `.grok` 공존 금지
- **Dedup-aware interim** — Copy 구간 동명 skill은 Grok가 `.grok` 우선; 의도적 단기 상태
- **Spawn contract preserved** — `subagent_type` 문자열(`thinking` 등) 불변; orchestrator 스폰 금지 유지

---

## 2. Architecture Options

### 2.0 Architecture Comparison

| Criteria | Option A: Minimal | Option B: Clean | **Option C: Pragmatic** |
|----------|:-----------------:|:---------------:|:-----------------------:|
| **Approach** | 복사 + 경로 치환만 | Grok agent 스키마 전면 + orch 분리 + 광범위 docs | Full native + frontmatter 최소 정규화 + 고정 active 목록 |
| **New Files** | ~14 (copy) + AGENTS | ~14 + process 분리 + config 가능 | ~14 (copy) + AGENTS |
| **Modified (active)** | SKILL·usage 최소 | SKILL·usage·docs/struct-* 전량 | SKILL·usage·active ref·stub |
| **Frontmatter** | 거의 유지 (`model: opus` 잔존) | `model: inherit`, `prompt_mode`, `permission_mode` 등 | `name`/`description` 유지 · `model`/`tools` 제거 |
| **orchestrator** | agents/ 유지 | reference 또는 process/ 분리 | **agents/ 유지 + spawn 금지 배너** |
| **Complexity** | Low | High | Medium |
| **Maintainability** | Medium (CC 잔향) | High | High |
| **Effort** | Low | High | Medium |
| **Risk** | Low–Med (혼동 잔존) | Med (스폰/스키마 회귀) | **Low (검증 게이트)** |
| **Recommendation** | 핫픽스 | 장기 플랫폼 정화 | **Default — 선택됨** |

**Selected**: **Option C — Pragmatic**  
**Rationale**: Plan 확정 범위(Full native · Copy→Verify→Delete · AGENTS only · minimal frontmatter · active only)와 일치. Clean의 스키마 전면 재작성·orch 분리는 본 사이클 비목표(로직 재작성 금지)와 충돌. Minimal은 CC frontmatter 잔향으로 온보딩 혼동 유지.

> 상세 설계 전부는 Option C 기준.

### 2.1 Component Diagram (Target)

```text
┌──────────────────────────────────────────────────────────────┐
│  Grok Build Parent (main)                                     │
│  Rules: AGENTS.md                                             │
└───────────────────────────┬──────────────────────────────────┘
                            │ invokes
                            ▼
┌──────────────────────────────────────────────────────────────┐
│  .grok/skills/struct-*/SKILL.md                               │
│  Parent-as-Orch instructions (no orchestrator spawn)          │
└───────────────────────────┬──────────────────────────────────┘
                            │ Read process + spawn specialist
              ┌─────────────┴─────────────┐
              ▼                           ▼
┌──────────────────────────┐   ┌──────────────────────────────┐
│ .grok/agents/            │   │ .grok/agents/{thinking,…}.md │
│ orchestrator.md          │   │ spawn_subagent only          │
│ (SoT · DO NOT spawn)     │   │ name = subagent_type         │
└──────────────────────────┘   └──────────────┬───────────────┘
                                              │ artifacts
                                              ▼
                               struct-docs/ + .struct-memory.json
                               reference/ · templates/ (unchanged roles)
```

### 2.2 Data Flow (Migration)

```text
[PR-N0] Inventory freeze
    │
    ▼
[PR-N1] Copy .claude/{skills,agents} → .grok/{skills,agents}
        + frontmatter normalize on .grok copies only
    │
    ▼
[PR-N1c] Write AGENTS.md (from CLAUDE + .grok paths)
         (.claude + CLAUDE still exist — dual interim OK)
    │
    ▼
[PR-N2] Rewrite paths in active files (.grok skills, usage, refs, stubs)
    │
    ▼
[PR-N3] Smoke S1–S7  ──FAIL──► STOP (no delete; fix; re-verify)
    │ PASS
    ▼
[PR-N3b] Optional checkpoint git commit (user approval)
    │
    ▼
[PR-N4] Delete .claude/ + CLAUDE.md
        Final rg hygiene
```

### 2.3 Dependencies

| Component | Depends On | Purpose |
|-----------|-----------|---------|
| `.grok/skills/*` | Grok skill scanner (repo `.grok/skills`) | `/struct-*` 진입 |
| `.grok/agents/*` | Grok agent discovery | `spawn_subagent` types |
| Parent process | `orchestrator.md` + `reference/*` | Brief/Spec/Prior/Review Gate |
| AGENTS.md | Grok project rules loader | 세션 온보딩 |
| Smoke S3–S5 | Parent-as-Orch 불변 계약 | 기능 회귀 방지 |
| Delete gate | S1–S7 all PASS | 안전 삭제 |

---

## 3. Data Model (Asset Manifest)

> DB entity 아님. **이전 자산 매니페스트**가 본 feature의 "data model".

### 3.1 Entity: SkillPackage

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| name | string | yes | `struct-think` … `struct-research` |
| source | path | yes | `.claude/skills/{name}/SKILL.md` |
| target | path | yes | `.grok/skills/{name}/SKILL.md` |
| pathRewrites | string[] | yes | 본문 `.claude/agents` → `.grok/agents` |

### 3.2 Entity: AgentDefinition

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| name | string | yes | `thinking` … `research`, `orchestrator` |
| spawnable | bool | yes | orchestrator=false; others=true |
| source | path | yes | `.claude/agents/{name}.md` |
| target | path | yes | `.grok/agents/{name}.md` |
| frontmatterPolicy | enum | yes | `minimal-normalize` |

### 3.3 Entity: ProjectRules

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| source | path | yes | `CLAUDE.md` |
| target | path | yes | `AGENTS.md` |
| dualFinal | bool | yes | **false** (최종 공존 금지) |

### 3.4 Entity Relationships

```text
[AGENTS.md] ──describes──► [.grok/skills/*] ──Read──► [.grok/agents/orchestrator.md]
                                    │
                                    └──spawn──► [.grok/agents/{specialist}.md]
                                                      │
                                                      └──write──► [struct-docs/*]
                                                                  [.struct-memory.json]
```

### 3.5 Delete-only Assets (no target)

| Path | Reason |
|------|--------|
| `.claude/settings.local.json` | CC permissions only |
| `.claude/agent-memory/**` | CC agent memory |
| `.claude/` after empty | directory removal |

---

## 4. API / Contract Specification

> HTTP API 없음. **런타임 계약**을 API 자리에 둔다.

### 4.1 Skill Discovery Contract

| Item | Spec |
|------|------|
| Location | `.grok/skills/struct-*/SKILL.md` |
| Frontmatter keep | `description`, `user-invocable: true` (existing) |
| Forbidden | primary `subagent_type: "orchestrator"` |
| Required body | Parent-as-Orch steps; process path `.grok/agents/orchestrator.md` |
| Specialist only | think→thinking, write→writing, solve→problem-solving, express→expression, review→review, research→research |

### 4.2 Agent Spawn Contract

| `subagent_type` | File | Spawn? |
|-----------------|------|:------:|
| thinking | `.grok/agents/thinking.md` | ✅ |
| writing | `.grok/agents/writing.md` | ✅ |
| problem-solving | `.grok/agents/problem-solving.md` | ✅ |
| expression | `.grok/agents/expression.md` | ✅ |
| review | `.grok/agents/review.md` | ✅ |
| research | `.grok/agents/research.md` | ✅ |
| orchestrator | `.grok/agents/orchestrator.md` | ❌ primary 금지 |

### 4.3 Frontmatter Normalization Schema (Option C)

#### Before (CC)

```yaml
---
name: thinking
model: opus          # CC model alias — remove
description: Minto 피라미드 원칙 기반 사고 정리 엔진 (v3 - Quality First)
tools: Read, Write   # CC tool list — remove
---
```

#### After (Grok minimal)

```yaml
---
name: thinking
description: Minto 피라미드 원칙 기반 사고 정리 엔진 (v3 - Quality First)
---
```

#### Rules

| Field | Action | Rationale |
|-------|--------|-----------|
| `name` | **Keep** (required) | `subagent_type` 매칭 |
| `description` | **Keep** | 카탈로그·선택 힌트 |
| `model: opus\|sonnet` | **Remove** | Grok parent model inherit; CC alias 혼동 방지 |
| `tools: …` | **Remove** | Grok는 capability_mode/agent toolset 사용; CC 도구명 무의미 |
| `prompt_mode` / `permission_mode` / `agents_md` | **Do not add** (Option C) | Clean(B) 범위; 최소 변경 원칙 |
| Body markdown | **Unchanged** except path strings if any | 로직 재작성 금지 |

#### Per-agent current models (for strip list)

| Agent | Current model | Action |
|-------|---------------|--------|
| thinking | opus | remove |
| writing | opus | remove |
| problem-solving | opus | remove |
| expression | sonnet | remove |
| review | sonnet | remove |
| research | sonnet | remove |
| orchestrator | sonnet | remove |

#### orchestrator.md special

- Frontmatter: same minimal schema (`name`, `description` only)
- Body: **keep** Runtime banner; update any internal path mentions only if present
- Do **not** make spawnable; banner remains authoritative

### 4.4 AGENTS.md Contract

| Item | Spec |
|------|------|
| Source content | `CLAUDE.md` full body as baseline |
| Structure tree | `.claude/` → `.grok/skills` + `.grok/agents` |
| Sync lines | `.claude/agents/writing.md` → `.grok/agents/writing.md` |
| Architecture | Parent-as-Orch 문단 유지; archive 링크 유지 |
| Dual load | Final state: **AGENTS only** (CLAUDE deleted) |

### 4.5 Path Rewrite Map

| From | To |
|------|----|
| `.claude/agents/` | `.grok/agents/` |
| `.claude/skills/` | `.grok/skills/` |
| `CLAUDE.md` (as ops summary pointer) | `AGENTS.md` |

**Do not rewrite inside**: `docs/archive/**`, `reference/archive/**`, Plan/Design historical quotes of as-is (those docs themselves are not rewrite targets for archive).

---

## 5. UI/UX Design

N/A — no product UI. Operator UX is slash skills + markdown artifacts.

### 5.1 Operator Flow

```text
/struct-think|write|…  →  same as today
Onboarding: open AGENTS.md (not CLAUDE.md)
```

### 5.2 Smoke Artifact Naming

| Check | Artifact pattern |
|-------|------------------|
| S3 V2 think | `struct-docs/01-thinking/20260715-grok-native-layout-think-path.md` |
| S4 V3 write | `struct-docs/02-writing/20260715-grok-native-layout-write-path.md` |
| S5 V4 review | `struct-docs/05-reviewing/20260715-grok-native-layout-write-path-review.md` |

Date may shift if Do runs another day → use actual `YYYYMMDD` of smoke day.

---

## 6. Error Handling / Rollback

### 6.1 Failure Codes (migration)

| Code | Meaning | Handling |
|------|---------|----------|
| M-COPY-FAIL | Copy incomplete | Do not rewrite paths; fix copy; retry N1 |
| M-SPAWN-FAIL | specialist not found after copy | Keep `.claude`; diagnose agent discovery; no delete |
| M-SKILL-MISS | `/struct-*` not listed | Check `.grok/skills` path/name; no delete |
| M-SMOKE-FAIL | S3–S5 fail | Fix content/paths; re-run smoke; no delete |
| M-HYGIENE-FAIL | active `.claude` refs remain after N2 | Complete rewrites; re-rg; no delete until clean *or* only planned interim if pre-delete |
| M-DELETE-PARTIAL | delete interrupted | Restore from git/copy residual; re-run N4 carefully |

### 6.2 Rollback Procedures

#### Before PR-N4 (preferred rollback)

1. Delete `.grok/` entirely (if broken)
2. Delete `AGENTS.md` if created
3. Keep `.claude/` + `CLAUDE.md` — **previous working state**
4. Revert active path rewrites via git if already applied to usage/reference

#### After PR-N4

1. Restore from git commit (checkpoint at N3b) **or**
2. Re-copy from that commit's tree
3. Do **not** attempt ad-hoc recreate without inventory

### 6.3 Hard Gates

```text
PR-N4 Delete ALLOWED only if:
  ✅ S1 Skill discovery
  ✅ S2 Agent spawn (thinking + writing minimum; full set preferred)
  ✅ S3 V2 think PASS
  ✅ S4 V3 write PASS
  ✅ S5 V4 review PASS
  ✅ S6 No orchestrator child spawn
  ✅ S7 Path hygiene: planned final paths exist; CLAUDE still ok until delete step
  ✅ User approval for destructive delete (and optional commit)
```

---

## 7. Security Considerations

- [x] No secrets in migrated files (skills/agents are prompts)
- [x] Destructive delete requires explicit gate + user confirm in Do
- [x] Do not commit intermediate dual-state without intent
- [x] `settings.local.json` dropped (CC allow-list); no Grok equivalent required for Struct

---

## 8. Test Plan

### 8.1 Test Scope

| Type | Target | Method | Phase |
|------|--------|--------|-------|
| S1 Discovery | skills under `.grok/skills` | Invoke/list `/struct-*` | Do / Check |
| S2 Spawn | agent types | `spawn_subagent` thinking, writing | Do / Check |
| S3 V2 | think E2E | file + memory | Do / Check |
| S4 V3 | write E2E | file + Brief path + memory | Do / Check |
| S5 V4 | review E2E | review file | Do / Check |
| S6 Contract | no orch spawn | skill instructions + runtime | Do / Check |
| S7 Hygiene | paths | `rg` with archive exclude | Do / Check |
| Structural | file set | list dirs vs manifest | Do / Check |

### 8.2 L1 Structural Checks (file existence)

| # | Check | Expected |
|---|-------|----------|
| 1 | `.grok/skills/struct-*/SKILL.md` | 6 files |
| 2 | `.grok/agents/*.md` | 7 files (6 specialist + orchestrator) |
| 3 | `AGENTS.md` | exists |
| 4 | `CLAUDE.md` after N4 | absent |
| 5 | `.claude/` after N4 | absent |

### 8.3 L2 Functional Smoke

| # | Action | Expected |
|---|--------|----------|
| 1 | `/struct-think` short topic | S3 artifact + memory entry |
| 2 | `/struct-write` short (autonomous ok) | S4 artifact; Parent Brief if required |
| 3 | `/struct-review` on write artifact | S5 artifact; fidelity section present |
| 4 | Confirm no orchestrator spawn in traces | S6 |

### 8.4 L3 Hygiene

```powershell
# After N4 — active tree must have zero .claude path refs
# Exclude archives and this feature's plan/design historical tables if needed;
# preferred: zero hits outside docs/archive and reference/archive

rg "\.claude/" --glob "!docs/archive/**" --glob "!reference/archive/**" --glob "!docs/01-plan/**" --glob "!docs/02-design/**"
rg "CLAUDE\.md" --glob "!docs/archive/**" --glob "!reference/archive/**" --glob "!docs/01-plan/**" --glob "!docs/02-design/**"
```

**Note**: Plan/Design themselves document as-is paths — exclude them from zero-hit gate **or** accept hits only in those PDCA docs.  
**Production gate (strict)**: zero hits in:

- `AGENTS.md`
- `.grok/**`
- `struct-docs/usage/**`
- `reference/*.md` (not archive)
- `docs/decisions/**`
- `docs/known-issues/**`

### 8.5 Seed Data

N/A — use existing templates/reference; smoke inputs can be short synthetic topics about "native layout verification".

---

## 9. Clean Architecture (Asset Layers)

### 9.1 Layer Structure

| Layer | Responsibility | Location |
|-------|----------------|----------|
| **Entry** | User-invocable skills | `.grok/skills/` |
| **Orchestration process** | Parent inlined SoT | `.grok/agents/orchestrator.md` |
| **Specialists** | Domain work | `.grok/agents/{thinking…}.md` |
| **Contracts** | Shared rules | `reference/*` |
| **Templates** | Deliverable shapes | `struct-docs/templates/` |
| **Artifacts** | Outputs | `struct-docs/0x-*/` |
| **Memory** | Session continuity | `.struct-memory.json` |
| **Project rules** | Always-on onboarding | `AGENTS.md` |

### 9.2 Dependency Rules

```text
Entry (skills) → Process (orchestrator.md) → Specialists
                → reference/* , templates/*
Specialists → reference/* (shared contract)
Project rules (AGENTS) → documents layout; does not replace skills
```

### 9.3 This Feature's Layer Assignment

| Component | Layer | Location |
|-----------|-------|----------|
| struct-* SKILL | Entry | `.grok/skills/struct-*/` |
| orchestrator.md | Process SoT | `.grok/agents/orchestrator.md` |
| specialists | Specialists | `.grok/agents/*.md` |
| AGENTS.md | Project rules | repo root |
| Path rewrites | Docs hygiene | active files list §11 |

---

## 10. Coding / Content Conventions

### 10.1 Naming

| Target | Rule | Example |
|--------|------|---------|
| Skill dirs | kebab `struct-*` | `struct-think` |
| Agent files | kebab matching spawn name | `problem-solving.md` |
| Smoke artifacts | `YYYYMMDD-grok-native-layout-*-path.md` | think/write/review |

### 10.2 Edit Rules During Migration

| Allowed | Forbidden |
|---------|-----------|
| Copy files | Rewrite specialist process logic |
| Strip `model`/`tools` frontmatter | Change `name` values |
| Path string `.claude`→`.grok` | Rewrite `docs/archive/**` |
| AGENTS from CLAUDE + path updates | Leave final dual CLAUDE+AGENTS |
| Delete after gates | Delete before S1–S6 pass |

### 10.3 File Encoding

- Preserve UTF-8 (skills/agents contain Korean)
- Prefer file tools over shell for content edit on Windows
- Avoid re-encoding BOM issues; if BOM present on source, keep or normalize consistently

---

## 11. Active Path Rewrite Inventory (Fixed List)

### 11.1 Must rewrite (in scope)

| # | File | Current hits (approx) | Action |
|---|------|----------------------|--------|
| 1 | `.grok/skills/struct-think/SKILL.md` | 2 (after copy) | `.claude/agents` → `.grok/agents` |
| 2 | `.grok/skills/struct-write/SKILL.md` | 3 | same |
| 3 | `.grok/skills/struct-solve/SKILL.md` | 1 | same |
| 4 | `.grok/skills/struct-express/SKILL.md` | 1 | same |
| 5 | `.grok/skills/struct-review/SKILL.md` | 0 known | verify only |
| 6 | `.grok/skills/struct-research/SKILL.md` | 0 known | verify only |
| 7 | `struct-docs/usage/index.md` | 1 | orch path |
| 8 | `struct-docs/usage/write.md` | 1 | writing path |
| 9 | `struct-docs/usage/review.md` | 2 | review + orch |
| 10 | `reference/agent-shared-contract.md` | 1 | intro line |
| 11 | `reference/deliverable-brief-schema.md` | 1 | review path |
| 12 | `docs/decisions/20260715-host-target-grok-only.md` | 1 | CLAUDE→AGENTS |
| 13 | `docs/known-issues/README.md` | 1 | CLAUDE→AGENTS |
| 14 | `AGENTS.md` | n/a | written with correct paths |
| 15 | `.grok/agents/*` | verify body for `.claude` strings | rewrite if any |

### 11.2 Explicitly out of scope (do not bulk-edit)

| Path | Reason |
|------|--------|
| `docs/archive/**` | history |
| `reference/archive/**` | history |
| `docs/struct-deliverable-system.design.md` | non-ops design history (Option C: not expanded) |
| `docs/struct-template-role-unification-plan.md` | same |
| `docs/struct-usage-scenarios.md` | same |
| `docs/01-plan/features/grok-native-layout.plan.md` | PDCA as-is documentation |
| `docs/02-design/features/grok-native-layout.design.md` | this file |

### 11.3 Optional post-cycle (backlog)

- One-line supersede note on `docs/archive/2026-07/cc-entrypath-notes.md` (not required for DoD)
- Path fix on historical `docs/struct-*.md` if operators still open them daily

---

## 12. Implementation Guide

### 12.1 Module Map

| Module | ID | Contents | Depends |
|--------|-----|----------|---------|
| Inventory | M0 | Disk vs plan inventory; baseline rg | — |
| Copy skills | M1 | 6 skill dirs to `.grok/skills` | M0 |
| Copy agents | M2 | 7 agent files to `.grok/agents` | M0 |
| Normalize FM | M3 | Strip model/tools on `.grok/agents` only | M2 |
| AGENTS | M4 | Write `AGENTS.md` from CLAUDE + paths | M1–M3 |
| Path rewrite | M5 | §11.1 files | M1–M4 |
| Smoke | M6 | S1–S7 | M5 |
| Checkpoint | M7 | Optional git commit | M6 PASS |
| Delete | M8 | Remove `.claude`, `CLAUDE.md` | M6 PASS + approve |
| Final hygiene | M9 | Strict rg on active set | M8 |

### 12.2 Implementation Order

1. M0 Inventory  
2. M1 + M2 Copy (parallel ok)  
3. M3 Frontmatter  
4. M4 AGENTS.md  
5. M5 Path rewrite  
6. M6 Smoke (stop on fail)  
7. M7 Checkpoint commit (recommended)  
8. M8 Delete  
9. M9 Final hygiene  

### 12.3 Session Guide

#### Module Map (for `--scope`)

| Scope key | Modules | Deliverable |
|-----------|---------|-------------|
| `module-1` | M0–M4 | `.grok/**` + `AGENTS.md`; `.claude` still present |
| `module-2` | M5–M6 | paths clean (active); smoke PASS |
| `module-3` | M7–M9 | delete + final hygiene |

#### Recommended Session Plan

| Session | Command | Scope | Notes |
|---------|---------|-------|-------|
| S1 | `/pdca do grok-native-layout --scope module-1` | Copy + AGENTS | No delete |
| S2 | `/pdca do grok-native-layout --scope module-2` | Rewrite + smoke | Gate for delete |
| S3 | `/pdca do grok-native-layout --scope module-3` | Delete | User confirm destructive |

**Single-session alternative**: full M0–M9 if operator available for continuous smoke (~0.5–1d).

### 12.4 Concrete Commands (Windows PowerShell)

```powershell
# M0
Test-Path .grok; Test-Path AGENTS.md
Get-ChildItem .claude\skills -Recurse -Filter SKILL.md
Get-ChildItem .claude\agents

# M1–M2
New-Item -ItemType Directory -Force -Path .grok\skills, .grok\agents | Out-Null
Copy-Item -Recurse .claude\skills\* .grok\skills\
Copy-Item .claude\agents\*.md .grok\agents\

# M3–M5: use editor/file tools (not bulk sed) for UTF-8 Korean safety

# M8 (only after M6 PASS)
Remove-Item -Recurse -Force .claude
Remove-Item -Force CLAUDE.md
```

### 12.5 AGENTS.md transformation checklist

- [ ] Copy CLAUDE body
- [ ] Title can remain "Struct Agent Team (Minto 기반)"
- [ ] File structure block → `.grok/skills`, `.grok/agents`
- [ ] Maintenance line: `.grok/agents/writing.md`
- [ ] No remaining `.claude` or `CLAUDE.md` self-reference as active path
- [ ] Keep Parent-as-Orch + archive links

### 12.6 Design Decision Record (locked)

| # | Decision | Choice |
|---|----------|--------|
| D1 | Architecture | **Option C Pragmatic** |
| D2 | Frontmatter | Remove `model`, `tools`; keep `name`, `description` |
| D3 | orchestrator location | `.grok/agents/orchestrator.md` (not spawn) |
| D4 | Active docs list | §11.1 fixed; struct-* historical designs **out** |
| D5 | Smoke names | `YYYYMMDD-grok-native-layout-*-path.md` |
| D6 | Checkpoint commit | Recommended before delete; user approves |
| D7 | Plan/Design rg exclude | Allowed; strict gate on ops paths |
| D8 | No `.grok/config.toml` | Not required for Struct migration |
| D9 | No symlink strategy | Pure copy |

---

## 13. Open Items Closed from Plan §9

| Plan Q | Design answer |
|--------|----------------|
| Q1 tools field | **Remove** (not keep-with-comment) |
| Q2 docs/struct-* | **Out of scope** (Option C; backlog §11.3) |
| Q3 git commit | **Recommended** at M7; not auto without user |
| Q4 smoke names | Confirmed §5.2 |
| Q5 orchestrator location | **agents/** confirmed |

---

## 14. Success Criteria Traceability

| Plan FR | Design coverage |
|---------|-----------------|
| FR-01 skills in `.grok` | M1, S1 |
| FR-02 agents + spawn | M2–M3, S2 |
| FR-03 AGENTS only | M4, M8 |
| FR-04 active path 0 | M5, S7, M9 |
| FR-05–07 smokes | M6 S3–S5 |
| FR-08 delete | M8 gate |
| FR-09 orch no spawn | M3 banner + S6 |
| FR-10 no archive edit | §11.2 |

---

## 15. Next Phase

```text
/pdca do grok-native-layout
# or
/pdca do grok-native-layout --scope module-1
```

Do phase must:

1. Re-read **this Design fully**
2. Show Context Anchor + Module Map
3. **Checkpoint 4** — implementation approval before edits
4. Execute M0… per scope
5. Never run M8 without M6 PASS + user confirm

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-07-15 | Design draft — Option C Pragmatic selected | Struct Agent Team / Grok |
