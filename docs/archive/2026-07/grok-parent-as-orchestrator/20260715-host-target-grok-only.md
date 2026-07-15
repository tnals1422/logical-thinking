---
tags: [decision, host-target, grok, runtime, archived]
id: HOST-TARGET-2026-07
created: 2026-07-15
archived: 2026-07-15
status: confirmed
choice: grok-only
archivedTo: docs/archive/2026-07/grok-parent-as-orchestrator/
related:
  - ./20260715-grok-orchestrator-spawn-failure.md
  - ./grok-first-parent-as-orchestrator.design.md
  - ./grok-parent-as-orchestrator.report.md
  - .claude/agents/orchestrator.md
  - CLAUDE.md
---

# Decision: Primary Runtime = Grok Build (Grok-only)

## 1. 확정 문장

> Struct Agent Team의 **Primary runtime은 Grok Build**이다.  
> 자식 `orchestrator` 스폰은 **제품 primary 경로로 쓰지 않는다**.  
> **Parent(메인 에이전트)가** Brief · Deliverable Spec · Prior · Review Gate · memory/저장을 수행하고,  
> **specialist만** `spawn_subagent` 한다.  
> Claude Code full pipeline은 6개월 범위 **비목표**. CC 전용 진입 경로는 **archive 일정**으로 정리한다.

## 2. 결정 기록 (YAML)

```yaml
decision_id: HOST-TARGET-2026-07
decided_at: 2026-07-15
status: confirmed
choice: grok-only

primary_runtime_6mo: grok-build
quality_gates_required: true
shared_repo_users_runtimes: "solo"
keep_cc_as_asset: false

score_A: 5
score_B: 0

D1: Y
D2: N
D3: Y
D4: N
D5: N

rationale: |
  6개월 Grok만 쓸 예정. CC full pipeline 계획 없음.
  orchestrator 자식 스폰 없이 Grok 단일 경로로 품질 게이트를 복구한다.

architecture_default:
  entry: struct-* skills on Grok Build
  orchestration: parent-as-orchestrator
  specialist_spawn: thinking | writing | problem-solving | expression | review | research
  forbidden_on_primary: spawn subagent_type=orchestrator

next_work:
  - Grok-first 오케스트레이션 설계 (L1) 문서화
  - struct-* 스킬 default 경로를 parent-orch + specialist 로 변경
  - 문서 Primary runtime = Grok (CLAUDE.md, usage, known-issue)
  - CC 경로 archive 일정 (당장은 물리 삭제 금지; 비활성/비권장)

non_goals:
  - 이중 스킬 장기 유지
  - Grok 플랫폼 requirements 수정 대기
  - specialist 본문 전량 재작성 (로직 재사용)
  - 당 스프린트에서 .claude 물리 삭제

implications_for_KI-STRUCT-ORCH-SPAWN-20260715:
  resolution_direction: D  # Parent-as-Orchestrator
  close_policy: |
    "Grok에서 orchestrator 자식 스폰 성공"은 Closed 조건에서 제외.
    primary가 parent+specialist로 think/write 품질 게이트 동작 시 Closed 가능.
```

## 3. `keep_cc_as_asset: false` 해석

| 함의 | 비함의 |
|------|--------|
| CC를 지원 타깃·온보딩 기준으로 유지하지 않음 | 즉시 `.claude/` 전체 삭제 |
| 스킬 primary = Grok parent 오케스트레이션 | specialist Role/Process 본문 폐기 |
| KI Closed를 CC orch 스폰 성공에 묶지 않음 | `reference/*`, `templates/*` 삭제 |

**재사용 대상**: `.claude/agents/{thinking,writing,problem-solving,expression,review,research}.md` 의 역할·프로세스  
**폐기/archive 대상**: `skill → spawn(orchestrator)` 를 전제로 한 진입 경로  
**대체**: Parent = `orchestrator.md` 프로세스 인라인 (상세: L1 설계)

## 4. 관련 문서

| 문서 | 역할 |
|------|------|
| [L1 설계](./grok-first-parent-as-orchestrator.design.md) | Parent-as-Orchestrator 상세 |
| [PDCA Report](./grok-parent-as-orchestrator.report.md) | 완료 보고서 |
| [KI orchestrator spawn](./20260715-grok-orchestrator-spawn-failure.md) | 원 이슈 · 해결 방향 D |
| [CC entrypath archive](../cc-entrypath-notes.md) | 구 CC 2단 진입 경로 보관 노트 (PR4) |
| `CLAUDE.md` | 아키텍처 요약 (active) |

## 5. 변경 이력

| 날짜 | 내용 |
|------|------|
| 2026-07-15 | 사용자 작성 YAML 확정 · 본 문서 저장 |
| 2026-07-15 | PR4 archive 링크 추가 (`docs/archive/2026-07/cc-entrypath-notes.md`) |
| 2026-07-15 | PDCA design/report → 본 폴더 |
| 2026-07-15 | Decision + KI 본 폴더로 합쳐 아카이빙 (묶음 완성) |
