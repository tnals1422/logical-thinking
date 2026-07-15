# Archive: grok-parent-as-orchestrator

PDCA + runtime decision 묶음 (2026-07-15). **한 폴더 = 한 사이클의 전체 맥락.**

| 문서 | 파일 | 역할 |
|------|------|------|
| **Decision** | [20260715-host-target-grok-only.md](./20260715-host-target-grok-only.md) | HOST-TARGET grok-only |
| **Known Issue** | [20260715-grok-orchestrator-spawn-failure.md](./20260715-grok-orchestrator-spawn-failure.md) | KI closed · resolution D |
| **Design (L1)** | [grok-first-parent-as-orchestrator.design.md](./grok-first-parent-as-orchestrator.design.md) | Parent-as-Orchestrator |
| **Report** | [grok-parent-as-orchestrator.report.md](./grok-parent-as-orchestrator.report.md) | PDCA 완료 보고 |

## 관련 (같은 월 archive, 별 파일)

| 문서 | 경로 |
|------|------|
| CC entrypath notes | [../cc-entrypath-notes.md](../cc-entrypath-notes.md) |

## 원 위치 stubs (최소화)

| Original | 상태 |
|----------|------|
| `docs/decisions/20260715-host-target-grok-only.md` | **stub 유지** (운영 한 줄 + 리다이렉트) |
| `docs/known-issues/20260715-grok-orchestrator-spawn-failure.md` | **stub 유지** (closed 요약 + 리다이렉트) |
| `docs/02-design/…design.md` | **stub 없음** — canonical만 본 폴더 |
| `docs/04-report/features/…report.md` | **stub 없음** — canonical만 본 폴더 |

## 운영 정책 (요약 — 상세는 Decision)

- Primary runtime: **Grok Build**
- Parent-as-Orchestrator · specialist only spawn
- `subagent_type: "orchestrator"` primary **금지**

일상 온보딩: `CLAUDE.md`, `struct-docs/usage/index.md`
