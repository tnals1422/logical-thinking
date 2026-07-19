# Archive Index — 2026-07

| Item | Status | Archived | Documents |
|------|--------|----------|-----------|
| CC entrypath (Claude Code 2-hop orch spawn) | archived | 2026-07-15 | [cc-entrypath-notes.md](./cc-entrypath-notes.md) |
| **grok-parent-as-orchestrator** (decision + KI + design + report) | **archived** | 2026-07-15 | [folder](./grok-parent-as-orchestrator/) |
| **grok-native-layout** (plan + design + analysis + report) | **archived** | 2026-07-15 | [folder](./grok-native-layout/) |
| **project-readme** (plan + design + analysis + report) | **archived** | 2026-07-19 | [folder](./project-readme/) |

## grok-parent-as-orchestrator 묶음

| 문서 | 파일 |
|------|------|
| Decision | [20260715-host-target-grok-only.md](./grok-parent-as-orchestrator/20260715-host-target-grok-only.md) |
| Known Issue | [20260715-grok-orchestrator-spawn-failure.md](./grok-parent-as-orchestrator/20260715-grok-orchestrator-spawn-failure.md) |
| Design L1 | [grok-first-parent-as-orchestrator.design.md](./grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md) |
| PDCA Report | [grok-parent-as-orchestrator.report.md](./grok-parent-as-orchestrator/grok-parent-as-orchestrator.report.md) |
| README | [README.md](./grok-parent-as-orchestrator/README.md) |

## grok-native-layout 묶음

| 문서 | 파일 |
|------|------|
| Plan | [grok-native-layout.plan.md](./grok-native-layout/grok-native-layout.plan.md) |
| Design | [grok-native-layout.design.md](./grok-native-layout/grok-native-layout.design.md) |
| Analysis | [grok-native-layout.analysis.md](./grok-native-layout/grok-native-layout.analysis.md) |
| PDCA Report | [grok-native-layout.report.md](./grok-native-layout/grok-native-layout.report.md) |
| README | [README.md](./grok-native-layout/README.md) |

**결과**: Match ~97% · FR 10/10 · `.grok` + `AGENTS.md` · `.claude`/`CLAUDE.md` 삭제

## project-readme 묶음

| 문서 | 파일 |
|------|------|
| Plan | [project-readme.plan.md](./project-readme/project-readme.plan.md) |
| Design | [project-readme.design.md](./project-readme/project-readme.design.md) |
| Analysis | [project-readme.analysis.md](./project-readme/project-readme.analysis.md) |
| PDCA Report | [project-readme.report.md](./project-readme/project-readme.report.md) |
| README | [README.md](./project-readme/README.md) |

**결과**: Match **100%** · FR 10/10 · 루트 `README.md` 온보딩 허브 · AGENTS 1줄 상호 링크 · `.grok/` 미변경

`project-readme` Plan/Design/Analysis/Report: **stub 없음**. canonical은 본 묶음 폴더만.  
운영 산출물(`README.md`, `AGENTS.md` 패치)은 루트에 **유지**.

## Stubs (최소화 — decision / KI only)

| Original | Points to |
|----------|-----------|
| `docs/decisions/20260715-host-target-grok-only.md` | Decision in parent-as-orch bundle |
| `docs/known-issues/20260715-grok-orchestrator-spawn-failure.md` | KI in parent-as-orch bundle |

`grok-native-layout` Plan/Design/Analysis/Report: **stub 없음**. canonical은 본 묶음 폴더만.

## Active (운영)

- `README.md` (온보딩 허브) · `AGENTS.md` · `struct-docs/usage/*` · `.grok/skills/*` · `.grok/agents/*` (orchestrator = process SoT)
- Known-issues index: [../known-issues/README.md](../known-issues/README.md)
- Decisions index: [../decisions/README.md](../decisions/README.md)

Primary runtime: **Grok Build** · Parent-as-Orchestrator · **native layout `.grok/`**.
