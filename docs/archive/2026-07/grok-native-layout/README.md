# Archive: grok-native-layout

PDCA 묶음 (2026-07-15). **CC 레이아웃 → Grok Build 네이티브 패키징** (Option C Pragmatic).

| 문서 | 파일 | 역할 |
|------|------|------|
| **Plan** | [grok-native-layout.plan.md](./grok-native-layout.plan.md) | Full native · Copy→Verify→Delete |
| **Design** | [grok-native-layout.design.md](./grok-native-layout.design.md) | Option C · M0–M9 · frontmatter 스키마 |
| **Analysis** | [grok-native-layout.analysis.md](./grok-native-layout.analysis.md) | Gap · Match ~97% · Checkpoint 5 accept |
| **Report** | [grok-native-layout.report.md](./grok-native-layout.report.md) | PDCA 완료 보고 |

## 관련 (같은 월 archive)

| 문서 | 경로 |
|------|------|
| Predecessor (동작 전환) | [../grok-parent-as-orchestrator/](../grok-parent-as-orchestrator/) |
| CC entrypath notes | [../cc-entrypath-notes.md](../cc-entrypath-notes.md) |
| HOST-TARGET stub | [../../../decisions/20260715-host-target-grok-only.md](../../../decisions/20260715-host-target-grok-only.md) |

## 원 위치

| Original | 상태 |
|----------|------|
| `docs/01-plan/features/grok-native-layout.plan.md` | **이동됨** — stub 없음 |
| `docs/02-design/features/…design.md` | **이동됨** — stub 없음 |
| `docs/03-analysis/features/…analysis.md` | **이동됨** — stub 없음 |
| `docs/04-report/features/…report.md` | **이동됨** — stub 없음 |

## 결과 (운영 자산 — 본 archive 밖)

| 자산 | 경로 |
|------|------|
| Project rules | `AGENTS.md` |
| Skills | `.grok/skills/struct-*/` |
| Agents | `.grok/agents/` (orchestrator = process SoT) |
| Smoke artifacts | `struct-docs/01|02|05-…/20260715-grok-native-layout-*` |

## 운영 정책 (요약)

- Primary runtime: **Grok Build**
- 레이아웃 SoT: **`.grok/` + `AGENTS.md`** (`.claude` / `CLAUDE.md` 없음)
- Parent-as-Orchestrator · specialist only spawn (predecessor)

일상 온보딩: `AGENTS.md`, `struct-docs/usage/index.md`
