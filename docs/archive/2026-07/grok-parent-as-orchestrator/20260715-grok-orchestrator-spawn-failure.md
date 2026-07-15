---
tags: [known-issue, grok, orchestrator, subagent, runtime, archived]
id: KI-STRUCT-ORCH-SPAWN-20260715
created: 2026-07-15
archived: 2026-07-15
status: closed
severity: high
archivedTo: docs/archive/2026-07/grok-parent-as-orchestrator/
platforms:
  - Grok Build (Windows)
  - logical-thingking / Struct Agent Team
related:
  - .claude/agents/orchestrator.md
  - .claude/skills/struct-*/SKILL.md
  - ./20260715-host-target-grok-only.md
  - ./grok-first-parent-as-orchestrator.design.md
  - ./grok-parent-as-orchestrator.report.md
resolution_direction: D  # Parent-as-Orchestrator (HOST-TARGET grok-only)
---

# Known Issue: Grok Build에서 `orchestrator` 서브에이전트 스폰 실패

## 1. 요약

| 항목 | 내용 |
|------|------|
| 증상 | `subagent_type: "orchestrator"` 호출 시 **자식 세션 생성 직전** 실패 |
| 실패 시점 | 에이전트 로직 진입 전 (tool_calls=0, duration≈0s) |
| 직접 원인 후보 | 에이전트 빌드 시 **도구 요구사항(requirements) 불충족** |
| 영향 범위 | `/struct-*` 스킬의 **1차 경로(Orchestrator 경유)** |
| 우회 | 각 스킬 **Fallback** — specialist 직접 호출 (`thinking`, `writing` 등) |
| 상태 | **Closed** (2026-07-15 · resolution D · V2/V3 pass) |

한 줄:

> Grok Build 환경에서 orchestrator 서브에이전트가 요구하는 백그라운드 태스크 제어 도구 세트가 충족되지 않아, **스폰 자체가 거부**된다. 스킬 Fallback으로 기능은 유지되나 Brief·Review Gate·Express Package 등 **오케스트레이션 전용 경로가 빠질 수 있다.**

---

## 2. 재현 정보

### 2.1 환경

| 항목 | 관측 값 (2026-07-15) |
|------|----------------------|
| Host OS | Windows |
| Shell | PowerShell |
| Workspace | `D:\workspace\logical-thingking` |
| Host runtime | Grok Build (xAI) — `spawn_subagent` / Task tool |
| 에이전트 정의 | `.claude/agents/orchestrator.md` (`tools: Agent, Read, Write`) |
| 스킬 경로 | `.claude/skills/struct-*/SKILL.md` → `subagent_type: "orchestrator"` |

### 2.2 재현 절차

1. 워크스페이스에서 `/struct-think` (또는 다른 `/struct-*`) 실행.
2. 스킬 지침대로 `spawn_subagent` / Agent 도구로  
   `subagent_type: "orchestrator"` 호출.
3. 즉시 실패 반환 (자식 대화·도구 호출 없음).

### 2.3 성공 대조 (동일 세션)

| `subagent_type` | 결과 | 비고 |
|-----------------|------|------|
| `orchestrator` | **실패** | Duration 0s, tool_calls 0 |
| `thinking` | **성공** | ~40s, Markdown 피라미드 반환 |

동일 호스트·동일 세션에서 specialist만 통과 → **입력 프롬프트/문서 문제가 아닌 에이전트 타입·도구 요구사항 문제**로 좁혀짐.

### 2.4 원본 오류 로그 (권위)

```text
Failed to spawn child session: Internal error: "agent building failed: tool error: Requirements unsatisfied: [
  RequirementError {
    tool: \"GrokBuild:kill_task\",
    message: \"unsatisfied requirements\",
    field_path: None,
    expected: None,
    bad_value: None,
    category: Some(\"requirements\")
  },
  RequirementError {
    tool: \"GrokBuild:get_task_output\",
    message: \"get_task_output requires a background-capable bash tool (GrokBuild:run_terminal_cmd or GrokBuildConcise:run_terminal_cmd with enabled_background=true), OpenCode:bash, or GrokBuild:task\",
    field_path: Some(\"tools\"),
    expected: Some(\"include a background-capable bash tool, OpenCode:bash, or GrokBuild:task\"),
    bad_value: None,
    category: Some(\"requirements\")
  },
  RequirementError {
    tool: \"GrokBuild:wait_tasks\",
    message: \"unsatisfied requirements\",
    field_path: None,
    expected: None,
    bad_value: None,
    category: Some(\"requirements\")
  }
]"
```

메타:

```text
Status: failed
Duration: 0.00s
Exit Code: 1
Tool calls: 0
Turns: 0
```

---

## 3. 원인 분석 (현재 가설)

### 3.1 확정에 가까운 사실

| # | 사실 | 근거 |
|---|------|------|
| F1 | 실패는 **런타임 실행 중**이 아니라 **에이전트 빌드/스폰** 단계 | `agent building failed`, tool_calls=0 |
| F2 | 불충족 도구는 `kill_task`, `get_task_output`, `wait_tasks` | 오류 본문 |
| F3 | `get_task_output`은 **백그라운드 bash/task 계열**을 전제로 요구 | `expected: include a background-capable bash tool...` |
| F4 | `thinking` 등 단순 도구 세트 에이전트는 스폰 가능 | 동일 세션 대조 |
| F5 | 스킬 Fallback 설계가 이미 존재 | 각 `SKILL.md` “Orchestrator 실패 시 specialist 직접” |

### 3.2 유력 가설 (미확정)

| ID | 가설 | 설명 |
|----|------|------|
| **H1** | **호스트 도구 그래프 불일치** | Grok Build가 자식 세션에 부여하는 도구 목록에 `kill_task`/`wait_tasks`/`get_task_output`이 있거나 암시적으로 요구되는데, 그 **requirements**(백그라운드 bash 등)가 자식 샌드박스에서 false |
| **H2** | **orchestrator 역할 → Agent/중첩 task 확장** | Claude Code 쪽 정의는 `tools: Agent, Read, Write` 이지만, Grok이 `orchestrator` 타입을 등록할 때 **중첩 subagent + 백그라운드 태스크 제어 도구**를 자동 주입하면서 요구사항 검사 실패 |
| **H3** | **이름 충돌 / 타입 매핑** | 시스템 내장 `orchestrator` 서브에이전트 타입과 프로젝트 `.claude/agents/orchestrator.md` 매핑이 어긋나, 의도보다 무거운 도구 세트로 빌드됨 |
| **H4** | **플랫폼 차이** | Claude Code CLI에서는 정상일 수 있음. 본 이슈는 **Grok Build에서 재현**된 것으로 범위 한정 문서화 |

### 3.3 기각·보류

| 가설 | 판정 | 이유 |
|------|------|------|
| 사용자 입력/문서 경로 오류 | 기각 | thinking 직접 호출 성공; 오류가 requirements |
| network/API key | 기각 | building 단계 로컬 도구 검사 |
| memory JSON 손상 | 기각 | orchestrator 진입 전 실패 |

### 3.4 개념 다이어그램

```text
[User] /struct-think
   │
   ▼
[Parent: Grok main agent]
   │  spawn_subagent(subagent_type="orchestrator")
   ▼
[Agent Builder] ── tools requirements check ──X── fail
   │                    │
   │                    ├─ kill_task          unsatisfied
   │                    ├─ get_task_output    needs bg bash/task
   │                    └─ wait_tasks         unsatisfied
   ▼
(never starts orchestrator prompt / tools)

[Fallback]
   spawn_subagent(subagent_type="thinking") ──► OK
```

---

## 4. 영향

### 4.1 기능 매트릭스

| 스킬 | 1차 경로 | Fallback | Orchestrator 실패 시 손실 가능 기능 |
|------|----------|----------|-------------------------------------|
| `/struct-think` | orchestrator → thinking | thinking 직접 | 모드 일괄 전파, memory 저장 자동화(수동 보완 가능) |
| `/struct-write` | orchestrator → writing | writing 직접 | **Brief 수집**, Deliverable Spec, Prior injection, **Review Gate**, Express 후속 |
| `/struct-solve` | orchestrator → problem-solving | problem-solving 직접 | 위와 유사 (Brief·Review) |
| `/struct-express` | orchestrator → expression | expression 직접 | 패키지 트리거, Review 연동 |
| `/struct-review` | orchestrator → review | review 직접 | 재생성 루프 제어 |
| `/struct-research` | orchestrator → research | research 직접 | research-first 전처리 연동 |

### 4.2 비즈니스/품질 영향

- **think만**: 품질 거의 유지 (Fallback 충분). 상위 에이전트가 파일·memory 저장을 대신하면 됨.
- **write/solve/express**: Orchestrator Phase 1/4/5 로직이 빠지면 **제출 품질 게이트 약화** 가능.
- 사용자가 “구조 팀이 안 돈다”로 오인하기 쉬움 → 본 문서로 **플랫폼 이슈**임을 명시.

---

## 5. 현재 우회 (Workaround)

### 5.1 스킬 내장 Fallback (권장 운영)

각 `SKILL.md` 패턴:

1. `orchestrator` 호출
2. 실패 시 `subagent_type: "{specialist}"` 직접 호출
3. 상위 에이전트가:
   - 결과 Markdown 사용자 출력
   - `struct-docs/0x-*/` 저장
   - `.struct-memory.json` 갱신

### 5.2 2026-07-15 실사용 사례

| 항목 | 내용 |
|------|------|
| 트리거 | `/struct-think` + edgeif IMG_CLCT_DTM 장애 문서 |
| orchestrator | 실패 (본 이슈) |
| Fallback | `thinking` autonomous |
| 산출물 | `struct-docs/01-thinking/20260715-edgeif-imgclctdtm-precision-rca-thinking.md` |
| memory | `previousThoughts` 갱신 완료 |

### 5.3 수동 체크리스트 (write 계열 Fallback 시)

Orchestrator 없이 write/solve/express 할 때 상위 에이전트가 보완할 항목:

- [ ] Mode (collaborative/autonomous) 명시
- [ ] Brief 3항 (purpose / audience / deliverable-type) 수집·주입
- [ ] Deliverable Spec (template + logic pattern)
- [ ] Prior thinking 파일 Read → `## Previous Thinking Pyramid`
- [ ] 저장 경로 `struct-docs/...` + frontmatter
- [ ] `.struct-memory.json` `briefs` / `previousDocuments` 등 갱신
- [ ] (write 후) Review Gate 별도 `/struct-review` 호출 권고

---

## 6. 해결 방향 (향후 작업용)

### 6.1 조사 체크리스트

| # | 조사 | 방법 | 기대 산출 |
|---|------|------|-----------|
| I1 | Claude Code CLI에서 `orchestrator` 스폰 여부 | 동일 워크스페이스 CC로 `/struct-think` | 플랫폼 한정 여부 |
| I2 | Grok 내장 `orchestrator` 타입 tools 목록 | Grok agent registry / spawn 에러 상세 / 문서 | H2·H3 검증 |
| I3 | 프로젝트 agent frontmatter `tools` 해석 여부 | Grok가 `.claude/agents/*.md`를 읽는지 | 정의 불일치 여부 |
| I4 | `get_task_output` 의존 제거 가능 여부 | orchestrator가 실제로 kill/wait/get을 쓰는지 코드·프롬프트 대조 | 최소 도구 세트 설계 |
| I5 | specialist-only 라우터 모드 | Grok 전용 스킬 분기: orchestrator 스킵 | 단기 안정 운영 |
| I6 | 중첩 spawn 금지 정책 | parent만 task 호출, orchestrator 로직을 parent에 인라인 | 아키텍처 대안 |

### 6.2 해결 옵션 (후보)

| 옵션 | 내용 | 장점 | 단점 | 권장 시점 |
|------|------|------|------|-----------|
| **A. 플랫폼 수정** | Grok 자식 세션에 bg bash + task 제어 도구 requirements 충족 | 기존 스킬/에이전트 무수정 | Grok/플랫폼 측 작업 필요 | 근본 해결 |
| **B. orchestrator 도구 축소** | Agent 중첩·task wait 없이 Read/Write + specialist 1회 호출만 | 요구사항 단순 | 동시 multi-agent 약화 | A 전 중간 |
| **C. Grok 전용 스킬** | `SKILL.md`에서 Grok 감지 시 specialist 직행 | 즉시 안정 | 이중 유지보수 | 단기 |
| **D. Parent-as-Orchestrator** | 메인 에이전트가 orchestrator.md 프로세스를 인라인 수행 | spawn 1단계 감소 | 프롬프트 비대화 | C와 병행 가능 |
| **E. 이름/타입 분리** | `struct-orchestrator` 등 별도 등록, 시스템 orchestrator와 분리 | H3 회피 | 등록 파이프라인 필요 | H3 확정 시 |

### 6.3 완료 정의 (DoD)

#### 6.3.1 원 DoD (폐기 — CC/orch 스폰 성공 전제)

~~1. Grok에서 orchestrator 자식 스폰 성공~~ — **HOST-TARGET grok-only 로 제품 경로에서 제외**

#### 6.3.2 신규 DoD (2026-07-15, resolution **D**)

결정: `./20260715-host-target-grok-only.md`  
설계: `./grok-first-parent-as-orchestrator.design.md`

다음을 **모두** 만족하면 Closed:

1. [x] Primary 경로에 `subagent_type: "orchestrator"` **없음** (6× SKILL.md + orchestrator.md 배너 + CLAUDE)
2. [x] `/struct-think` → Parent 경로로 `01-thinking` + `previousThoughts` **(V2 2026-07-15 pass)**
3. [x] `/struct-write` → Brief(또는 autonomous 폴백) + `02-writing` + memory (`briefs` / `previousDocuments`) **(V3 2026-07-15 pass)**
4. [x] write 후 Review Gate를 Parent가 조건에 따라 트리거 가능 (SKILL·review-gate 문서화)
5. [x] 본 문서 `status: closed` + `resolution_option: D` + 결정·설계 링크
6. [x] specialist spawn 실패 시 에러 가시성 (각 SKILL Emergency 절)

### 6.4 검증 시나리오 (수정 후)

```text
V1' Parent 경로: /struct-think — orch spawn 시도 없음 + thinking 성공
V2  /struct-think 한 줄 주제 → 01-thinking 파일 + memory
V3  /struct-write autonomous 최소 Brief → 02-writing 파일 + briefs
V4  (선택) /struct-review 또는 write 후 Gate → 05-reviewing
(폐기) V1 spawn orchestrator only — 제품 primary 아님
```

---

## 7. 관련 파일

| 경로 | 역할 |
|------|------|
| `.claude/agents/orchestrator.md` | 오케스트레이터 정의 (`tools: Agent, Read, Write`) |
| `.claude/agents/thinking.md` 등 | specialist — 현재 Grok에서 스폰 가능 |
| `.claude/skills/struct-*/SKILL.md` | orchestrator 1차 + Fallback |
| `reference/orchestrator-review-gate.md` | Review Gate 상세 |
| `reference/orchestrator-express-package.md` | Express 패키지 후속 |
| `reference/deliverable-brief-schema.md` | Brief 스키마 |
| `struct-docs/usage/index.md` | 사용 인덱스 (본 이슈 링크) |
| `CLAUDE.md` | 아키텍처 요약 (본 이슈 링크) |

---

## 8. 변경 이력

| 날짜 | 내용 |
|------|------|
| 2026-07-15 | 최초 문서화. Grok Build `/struct-think` 중 재현. Fallback으로 think 완료. status=open |
| 2026-07-15 | HOST-TARGET grok-only 확정. 해결 방향 **D**. DoD를 Parent-as-Orch 기준으로 재정의. 설계 링크 추가. status=open (구현 전) |
| 2026-07-15 | PR1 스킬 패치. V2 think + V3 write PASS. status=**closed** (resolution D) |
| 2026-07-15 | Decision·KI·Design·Report 동일 폴더로 합쳐 아카이빙 |

---

## 9. 담당 메모 (해결 시 채울 필드)

```yaml
resolved_at: 2026-07-15
resolved_by: parent-as-orchestrator-l1
resolution_option: D
resolution_summary: >
  HOST-TARGET grok-only: do not spawn orchestrator child;
  Parent executes orchestrator.md process; specialist-only spawn.
  Skills patched; V2 think + V3 write verified.
  See ./20260715-host-target-grok-only.md
  and ./grok-first-parent-as-orchestrator.design.md
  and ./grok-parent-as-orchestrator.report.md
verification: |
  V1'/V2 PASS 2026-07-15: thinking only; 01-thinking/20260715-grok-parent-as-orchestrator-think-path.md
  V3 PASS 2026-07-15: writing only + Brief/Spec/Prior; 02-writing/20260715-grok-parent-as-orchestrator-write-path.md
  V4 PASS 2026-07-15: review only; 05-reviewing/...-write-path-review.md;
  fidelity pass; ST4 force_rework → Parent W4 author row (no orch spawn)
follow_ups: []
```
