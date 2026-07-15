---
tags: [archive, claude-code, entrypath, grok, parent-as-orchestrator]
id: ARCHIVE-CC-ENTRYPATH-20260715
created: 2026-07-15
status: archived
decision: docs/archive/2026-07/grok-parent-as-orchestrator/20260715-host-target-grok-only.md
related:
  - docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md
  - docs/archive/2026-07/grok-parent-as-orchestrator/
---

# Archive: Claude Code 진입 경로 (CC Entrypath Notes)

> **목적**: Struct Agent Team이 **Grok-only · Parent-as-Orchestrator** 로 전환된 뒤,  
> 과거 **Claude Code(CC) 전제 진입 경로**를 기록·보관한다.  
> **물리 삭제하지 않은 자산**과 **더 이상 primary가 아닌 패턴**을 구분한다.

---

## 1. 요약

| 항목 | 내용 |
|------|------|
| 전환일 | 2026-07-15 |
| Primary runtime | **Grok Build** |
| 결정 | `HOST-TARGET-2026-07` · `choice: grok-only` · `keep_cc_as_asset: false` |
| 해법 | Parent-as-Orchestrator (KI resolution **D**) |
| 검증 | V2 think · V3 write · V4 review PASS |
| 본 노트 의미 | CC **지원 타깃 종료** 기록. 코드/에이전트 본문 일괄 삭제 **아님** |

**한 줄**

> 예전: `Skill → spawn(orchestrator) → specialist` (CC 2단).  
> 지금: `Skill → Parent 인라인(orchestrator.md) → spawn(specialist only)` (Grok).

---

## 2. 아카이브 대상 (진입 패턴)

다음 **진입·호출 패턴**은 primary에서 **폐기(비권장)** 되었다.

| 패턴 | 구 위치 | 상태 |
|------|---------|------|
| 스킬 1차 `subagent_type: "orchestrator"` | `.claude/skills/struct-*/SKILL.md` | **제거됨** (PR1) |
| Orchestrator 실패 시 specialist Fallback을 primary처럼 사용 | 동일 SKILL Fallback 절 | **Primary로 승격·통합** 후 Fallback 문구 삭제 |
| “Orchestrator Agent 자식 세션” 전제 온보딩 | usage, CLAUDE, 시나리오 | **Parent 인라인** 으로 재서술 (PR2) |
| KI Closed 조건 = Grok에서 orch 자식 스폰 성공 | known-issue 원 DoD | **폐기** → Parent+specialist DoD |

### 2.1 구 호출 시퀀스 (참고용)

```text
[User] /struct-write
   │
   ▼
[Skill] Agent(subagent_type="orchestrator")
   │
   ▼
[Orchestrator 자식] Brief / Spec / Prior / memory
   │
   ▼
[Orchestrator] Agent(subagent_type="writing")
   │
   ▼
[writing] Markdown
   │
   ▼
[Orchestrator] 저장 + memory + Review Gate
```

### 2.2 현 호출 시퀀스 (primary)

```text
[User] /struct-write
   │
   ▼
[Skill Instructions] Parent = Grok main
   │  Read orchestrator.md + reference/*
   │  Brief / Spec / Prior / memory
   ▼
[Parent] spawn_subagent(subagent_type="writing")  // only
   │
   ▼
[writing] Markdown 반환
   │
   ▼
[Parent] 저장 + memory + (조건 시) review / express
```

**Grok 제약**: 서브에이전트 중첩 깊이 1 → 구 2단 스폰은 구조적으로 불리.

---

## 3. 잔류 자산 (삭제 금지)

`keep_cc_as_asset: false` 는 **CC를 제품 타깃으로 유지하지 않음**이지, 아래 파일 삭제를 의미하지 않는다.

| 경로 | 역할 (전환 후) |
|------|----------------|
| `.claude/agents/orchestrator.md` | **Parent 프로세스 스펙 (SoT)** — 스폰 대상 아님 |
| `.claude/agents/{thinking,writing,problem-solving,expression,review,research}.md` | Specialist 정의 (Grok spawn 대상) |
| `.claude/skills/struct-*/SKILL.md` | Parent 진입 Instructions (Grok primary) |
| `reference/*` | Brief, Review Gate, Express, checklists |
| `struct-docs/templates/*` | 산출 템플릿 |
| `struct-docs/0x-*/` · `.struct-memory.json` | 세션·산출 자산 |

Grok는 `.claude/skills` · `.claude/agents` 를 Claude 호환으로 로드할 수 있다.  
네이티브 이전(`.grok/agents` 등)은 **별 epic** — 본 archive 범위 밖.

---

## 4. 문서·구현 이전 맵

| 단계 | 산출 | 상태 |
|------|------|------|
| A0 | L1 설계 · HOST-TARGET 결정 | done |
| A1 | 6× SKILL Parent-as-Orch | done |
| A2 | CLAUDE · usage · workflow.mmd | done |
| A3 | KI closed (D) · V2/V3 | done (+ V4) |
| **A4** | **본 노트** | **done** |

### 4.1 활성 문서 (canonical)

| 주제 | 경로 |
|------|------|
| 결정 · KI · design · report | `docs/archive/2026-07/grok-parent-as-orchestrator/` |
| Usage | `struct-docs/usage/index.md` · `workflow.mmd` |
| 아키텍처 요약 | `CLAUDE.md` |

### 4.2 역사 문서 (2026-06 archive — 참고만)

| 경로 | 주의 |
|------|------|
| `docs/archive/2026-06/minto-agent-team/*` | **2단계 호출 (Skill→Orch→Agent)** 설계 당시 문서. **primary 아님** |
| 동 폴더 plan/design/report | CC Agent 도구·sonnet orchestrator 전제 문장 포함 가능 |

역사 문서를 수정하지 않는다. 동작 기준은 §4.1 만 따른다.

---

## 5. 복구 가이드 (비상 · 비권장)

CC full pipeline을 다시 primary로 두려면 (결정 번복 시):

1. `HOST-TARGET` 재결정 (`dual` 또는 `cc-primary-…`)
2. 스킬에 `spawn(orchestrator)` 경로 재도입 여부 검토  
   - 단, **Grok에서는 depth=1 · orch 빌드 requirements** 로 실패 가능 → Grok dual 시 Parent 경로 유지 필수
3. specialist 본문·templates는 그대로 재사용

**권장하지 않음** (6개월 Grok-only 결정과 충돌).

---

## 6. 명시적 Non-goals (본 archive)

- `.claude/` 디렉터리 물리 삭제
- specialist / templates / reference 이전 또는 폐기
- Grok 플랫폼 `kill_task` requirements 수정 요청
- 2026-06 archive PDCA 문서 전량 재작성

---

## 7. 변경 이력

| 날짜 | 내용 |
|------|------|
| 2026-07-15 | PR4 A4: CC entrypath notes 최초 작성. Primary=Grok Parent-as-Orch 확정 후. |
