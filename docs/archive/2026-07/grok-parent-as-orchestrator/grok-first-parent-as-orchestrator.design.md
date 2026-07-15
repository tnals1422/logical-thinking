---
tags: [design, grok, orchestrator, parent-as-orchestrator, l1, archived]
id: DESIGN-GROK-L1-PAO-20260715
created: 2026-07-15
archived: 2026-07-15
status: archived
report: ./grok-parent-as-orchestrator.report.md
decision: ./20260715-host-target-grok-only.md
related_issue: ./20260715-grok-orchestrator-spawn-failure.md
archivedTo: docs/archive/2026-07/grok-parent-as-orchestrator/
---

# Design: Grok-first Parent-as-Orchestrator (L1)

> **Archived 2026-07-15** — Decision · KI · Design · Report 한 폴더 묶음. 운영 요약: `CLAUDE.md`, skills, usage.

## 0. 목적 · 범위

| 항목 | 내용 |
|------|------|
| **목적** | Grok Build에서 Struct `/struct-*` 가 **품질 게이트(Brief·Spec·Prior·Review·Express·memory)** 를 유지한 채 동작 |
| **결정** | [HOST-TARGET-2026-07](./20260715-host-target-grok-only.md) — `choice: grok-only` |
| **해결 옵션** | Known Issue 옵션 **D** (Parent-as-Orchestrator). 옵션 C(orch spawn 스킵)와 동일 계열을 **정식 경로**로 승격 |
| **비목표** | Grok 플랫폼 tool requirements 수정(A); CC 이중 스킬 장기 유지; specialist 본문 전량 재작성; `.claude` 즉시 물리 삭제 |

### 성공 기준 (L1)

1. Primary 경로에 `subagent_type: "orchestrator"` **없음**
2. Parent가 Mode · Brief · Spec · Prior · 저장 · memory를 수행
3. Specialist는 기존 타입으로 1회(또는 Review/Express 시 추가) spawn
4. `/struct-think` → `01-thinking` + `previousThoughts`
5. `/struct-write` → Brief 수집/추론 + `02-writing` + `previousDocuments` / `briefs` (최소)
6. write 후 Review Gate는 parent가 조건에 따라 `review` spawn 제어 (기존 R1~R4 의미 유지)

---

## 1. 문제 요약

```text
[기존 CC 가정]
  Skill → spawn(orchestrator) → spawn(specialist)
                    │
                    X  Grok: agent building failed
                       (kill_task / get_task_output / wait_tasks)

[L1 목표]
  Skill Instructions = Parent 가 orchestrator.md 프로세스 수행
                    → spawn(specialist only)
```

| 실패 지점 | L1 대응 |
|-----------|---------|
| orchestrator 자식 빌드 requirements | **스폰하지 않음** |
| Fallback만 쓰면 Brief/Review 손실 | Parent가 **인라인으로 수행** |
| 이중 호스트 유지보수 | **Grok-only** — 분기 스킬 장기 유지 안 함 |

---

## 2. 목표 아키텍처

### 2.1 런타임 다이어그램

```text
[User]  /struct-think | write | solve | express | review | research
              │
              ▼
┌─────────────────────────────────────────────────────────────┐
│  Parent (Grok main agent)  =  Orchestrator role (inline)    │
│  · Mode 감지                                                 │
│  · .struct-memory.json R/W                                   │
│  · Brief / Deliverable Spec (write·solve·express)            │
│  · Prior Thinking Read + inject                              │
│  · research-first → spawn(research) 후 Source Validation     │
│  · spawn(specialist)                                         │
│  · 결과 저장 struct-docs/0x-*/                               │
│  · memory 갱신                                               │
│  · Review Gate → spawn(review) + force_rework 루프           │
│  · Express Package 후속 → spawn(expression)                  │
└─────────────────────────────────────────────────────────────┘
              │ spawn_subagent(subagent_type=…)
              ▼
   thinking | writing | problem-solving | expression | review | research
              │
              ▼
   Markdown 반환 → Parent가 파일·memory 확정
```

### 2.2 책임 분리

| 역할 | 담당 | 도구 가정 (Grok) |
|------|------|------------------|
| **Parent** | 라우팅·Brief·Spec·Prior·Gate·저장·memory | Read, Write, spawn_subagent (specialist만) |
| **Specialist** | 도메인 산출 (피라미드, 보고서 본문 등) | 기존 정의 (Read/Write 등; 중첩 orchestrator 불필요) |
| **orchestrator.md** | **프로세스 스펙(SoT)** — 실행 주체는 Parent | 스폰 대상 아님 (primary) |
| **reference/** | Review Gate, Express Package, Brief schema 상세 | Parent가 Read하여 준수 |

### 2.3 금지 · 허용

| 규칙 | 내용 |
|------|------|
| **금지 (primary)** | `spawn_subagent(subagent_type: "orchestrator")` |
| **허용** | specialist 타입 스폰; Parent 인라인 오케스트레이션 |
| **비상** | specialist 직행만 가능한 경우 → 스킬에 “품질 게이트 축소” 경고 + write 시 수동 Brief 체크리스트 |
| **archive** | CC 전제 “skill → orchestrator 자식” 문구 · 문서; 일정은 §7 |

---

## 3. Parent 프로세스 (커맨드별)

권위 소스: `.claude/agents/orchestrator.md` + `reference/*`.  
아래는 **Grok Parent가 수행할 순서 계약**이다.

### 3.1 공통 진입 (모든 `/struct-*`)

| Step | 동작 |
|------|------|
| P0 | 커맨드 식별 (think/write/solve/express/review/research) |
| P1 | Mode: collaborative(기본) \| autonomous |
| P2 | `.struct-memory.json` 로드 (없으면 기본 구조 생성 — orchestrator.md §2) |
| P3 | 커맨드별 분기 (§3.2~) |
| P4 | 사용자 출력 (개조식·표 기본 스타일) |

### 3.2 think

```text
P2 → spawn(thinking) → 저장 01-thinking → previousThoughts 갱신 → 출력
```

- Brief/Spec **없음**
- Prior 강제 없음 (memory 요약 Context 선택)

### 3.3 write (품질 게이트 핵심)

```text
P2
 → [RV0] research-first? → spawn(research) → Source Validation 블록
 → [B0–B2] Brief 수집/추론 (Collaborative: 3항 미확정 시 Specialist 보류·질문)
 → [DS] Deliverable Spec (template Read, logic pattern)
 → [Prior] previousThoughts 유사도 → Read → Previous Thinking Pyramid
 → spawn(writing) + Brief/Spec/Prior/Mode/Source Validation
 → 저장 02-writing
 → previousDocuments + briefs 갱신
 → [R] Review Gate? → spawn(review) → force_rework 시 writing 재호출
 → [EP] Express Package? → spawn(expression)
 → 출력
```

상세 위임:

| 단계 | 상세 문서 |
|------|-----------|
| Brief / Spec | `reference/deliverable-brief-schema.md` |
| Prior | `orchestrator.md` Thinking Reuse 절 |
| Review | `reference/orchestrator-review-gate.md` |
| Express | `reference/orchestrator-express-package.md` |
| Writing stages | writing agent + `reference/writing-pipeline.md` |

### 3.4 solve

write와 동일 계열 Brief/Spec/Prior; specialist = `problem-solving`; 저장 `03-solving`; memory `previousAnalysis`.  
Review Gate 조건은 review-gate 문서 준수.

### 3.5 express

- 단독 변환(slide/memo/story) 또는 packageMode(brief/package)
- Brief/Spec 적용 (package·보고서 연계 시 Full Report Source)
- specialist = `expression`; 저장 `04-expressing`

### 3.6 review

- Target 경로 결정 → Brief 복원 → spawn(review)
- 저장 `05-reviewing`; `previousReviews`
- force_rework 시 해당 specialist 재호출은 **Parent만** 수행

### 3.7 research

- spawn(research); 저장 `06-researching`; `previousResearch`
- write/solve 전처리로 호출될 때 Source Validation 블록 생성

### 3.8 Specialist 프롬프트 최소 골격

```text
## Task
{command} 수행

## User Input
{...}

## Mode
{collaborative | autonomous}

## Brief (Report Operations)     # write/solve/express
## Deliverable Spec              # write/solve/express
## Previous Thinking Pyramid     # 해당 시
## Source Validation             # 해당 시
## Review Feedback               # 재생성 시
## Context (from .struct-memory.json)
## Output Requirements
- Markdown, Frontmatter, 경로 규칙 (orchestrator.md §3 준수)
```

---

## 4. 스킬 계층 변경

### 4.1 원칙

| Before | After (Grok-only primary) |
|--------|---------------------------|
| 1) spawn orchestrator 2) Fallback specialist | **1) Parent가 오케스트레이션 Instructions 수행** 2) spawn specialist 3) 저장·memory (Parent) |
| Fallback = 품질 축소 비상 | 비상 경로는 유지하되 **primary와 동일 체크리스트를 Parent가 최대한 수행** |

### 4.2 파일별 변경 요약

| 스킬 | 변경 |
|------|------|
| `struct-think/SKILL.md` | orch spawn 제거. Mode → spawn thinking → 저장·memory 명시 |
| `struct-write/SKILL.md` | Brief/Spec/Prior/Review/Express 단계를 Instructions에 인라인 (또는 “orchestrator.md § Process 를 Parent가 수행” 단일 위임 문구 + 체크리스트) |
| `struct-solve/SKILL.md` | write와 동일 패턴, specialist=problem-solving |
| `struct-express/SKILL.md` | package/brief 분기 + expression spawn; orch 제거 |
| `struct-review/SKILL.md` | Parent가 R1 준비 후 review spawn |
| `struct-research/SKILL.md` | research spawn + 저장·memory |

### 4.3 SKILL.md 권장 구조 (공통)

```markdown
## Primary runtime
Grok Build. Parent-as-Orchestrator. Do NOT spawn subagent_type orchestrator.

## Instructions
1. Read `.claude/agents/orchestrator.md` Process for this command (and reference/* as needed)
2. Execute Parent steps for {command}
3. spawn_subagent(subagent_type: "{specialist}") with built Context
4. Save under struct-docs/... and update .struct-memory.json
5. User-facing output (bullets/tables default)

## Emergency only
If specialist spawn fails: report error; do not silently skip Brief on write.
```

### 4.4 `orchestrator.md` 역할 변경 (문서만, L1)

| 항목 | 조치 |
|------|------|
| frontmatter `name: orchestrator` | 유지 가능 — **스폰 타입 등록 목적이 아님**을 문서 상단에 명시 |
| 본문 Process | **Parent 실행 스펙** 으로 재라벨 ( "Grok Parent MUST follow" ) |
| tools: Agent, Read, Write | 역사적 CC 표기; Grok에서는 Parent 도구 세트로 해석 |

구현 PR에서 `orchestrator.md` 상단에 배너 추가 권장:

```markdown
> **Runtime (2026-07)**: Grok-only. This file is the **process spec for the Parent agent**.
> Do not spawn `subagent_type: "orchestrator"` as the primary path.
```

---

## 5. 산출물 · 경로 · memory (변경 없음)

기존 계약 유지:

| Command | 디렉터리 | memory 키 |
|---------|----------|-----------|
| think | `struct-docs/01-thinking/` | `previousThoughts` |
| write | `struct-docs/02-writing/` | `previousDocuments`, `briefs` |
| solve | `struct-docs/03-solving/` | `previousAnalysis` |
| express | `struct-docs/04-expressing/` | `previousDocuments` (유형별) |
| review | `struct-docs/05-reviewing/` | `previousReviews` |
| research | `struct-docs/06-researching/` | `previousResearch` |

파일명: `YYYYMMDD-{kebab-title}.md` (+ research/review 관례 접미사 기존 유지).

---

## 6. Known Issue (KI) Closed 정책 재정의

원 문서 DoD는 “orchestrator 자식 스폰 성공”을 포함.  
**HOST-TARGET grok-only** 하에서는 다음으로 대체한다.

### 6.1 신규 DoD (제안)

| # | 조건 |
|---|------|
| N1 | Primary 경로에 orchestrator spawn **없음** (스킬·CLAUDE 명시) |
| N2 | `/struct-think` → 파일 + `previousThoughts` (Parent 경로) |
| N3 | `/struct-write` → Brief(또는 autonomous 폴백) + 파일 + memory |
| N4 | write 후 Review를 옵션/조건에 따라 Parent가 트리거 가능 (최소 1시나리오 문서화 또는 실행) |
| N5 | KI `status: closed` + `resolution_option: D` + 본 결정·설계 링크 |
| N6 | specialist spawn 실패 시 에러 가시성 (조용한 품질 게이트 스킵 금지) |

### 6.2 검증 시나리오 (수정)

| ID | 시나리오 | 기대 |
|----|----------|------|
| V1' | Parent ping: skill think 한 줄 | thinking spawn 성공; **orch spawn 시도 없음** — **PASS 2026-07-15** (V2와 동일 실행) |
| V2 | `/struct-think` | `01-thinking` + memory — **PASS 2026-07-15** → `20260715-grok-parent-as-orchestrator-think-path.md` |
| V3 | `/struct-write` autonomous 최소 Brief | `02-writing` + briefs/documents — **PASS 2026-07-15** → `20260715-grok-parent-as-orchestrator-write-path.md` |
| V4 | (선택) review 명시 | `05-reviewing` — **PASS 2026-07-15** → `20260715-grok-parent-as-orchestrator-write-path-review.md` (fidelity pass; ST4 Parent W4 fix) |
| ~~V1 orch spawn~~ | **폐기** | 제품 경로 아님 |

---

## 7. CC 경로 Archive 일정

`keep_cc_as_asset: false` — **지원 타깃 종료**, 즉시 삭제는 비권장.

| Phase | 시점 | 내용 |
|-------|------|------|
| **A0** | L1 문서 (본 설계) | Primary = Grok 명시 |
| **A1** | 스킬 패치 PR | orch spawn 문구 제거; Parent Instructions |
| **A2** | 문서 패치 | CLAUDE.md, usage/index, workflow 주석 |
| **A3** | KI close | resolution D, 신규 DoD |
| **A4** | 2026-07-15 | `docs/archive/2026-07/cc-entrypath-notes.md` — **done**. `.claude/agents/orchestrator.md` 는 **프로세스 스펙으로 잔류** |

물리 삭제 후보 (A4 이후 재검토):

- 문서 중 “Claude Code 세션 전제” 온보딩 문장
- (삭제 금지 권장) specialist md, templates, reference

---

## 8. 구현 계획 (PR 단위)

| PR | 내용 | 의존 | 상태 |
|----|------|------|------|
| **PR0** | 본 결정 + 본 설계 커밋 (문서 only) | — | done |
| **PR1** | 6× `struct-*/SKILL.md` Parent-as-Orch | PR0 | done |
| **PR2** | `orchestrator.md` 배너 + CLAUDE + usage + workflow.mmd | PR1 | **done** (2026-07-15) |
| **PR3** | KI DoD 갱신·close (V2/V3) | PR1–2 | done (V4 optional also pass) |
| **PR4** | archive 노트 · workflow는 PR2에 흡수 | PR3 | **done** (`docs/archive/2026-07/cc-entrypath-notes.md`) |

### PR1 구현 체크리스트 (스킬)

- [x] 모든 스킬: “Do NOT spawn orchestrator”
- [x] write/solve/express: Brief 3항 Collaborative 질문 템플릿 링크
- [x] write: Prior / Review / Express 훅 또는 orchestrator.md 위임 명시
- [x] 저장 경로·memory 키 명시 (구 Fallback 절을 Primary로 승격·통합)
- [x] Emergency: specialist 실패 시 동작
- [x] `orchestrator.md` Grok-only 배너 (PR2 일부 선반영)

---

## 9. 리스크 · 완화

| 리스크 | 영향 | 완화 |
|--------|------|------|
| Parent 컨텍스트 비대 (orch 전체 인라인) | 토큰·누락 | 스킬은 **짧은 체크리스트 + orchestrator.md/reference Read** |
| Review 루프 미구현 | 제출 품질 | PR1에 write 후 “review 권고”; PR1.1에서 Gate 자동화 |
| specialist가 Write로 이중 저장 | 경로 불일치 | Parent 최종 저장 권한 명시; specialist는 본문 반환 우선 |
| Grok에 agent md 미로드 | 프로세스 무시 | 스킬이 Read 경로를 **절대/상대 고정**으로 지시 |
| 사용자가 습관적으로 orch 기대 | 혼란 | CLAUDE · usage · skill 상단 한 줄 고지 |

---

## 10. 관련 파일 맵

| 경로 | L1에서의 역할 |
|------|----------------|
| `./20260715-host-target-grok-only.md` | 결정 (본 묶음) |
| `./grok-first-parent-as-orchestrator.design.md` | 본 설계 |
| `./20260715-grok-orchestrator-spawn-failure.md` | KI closed |
| `./grok-parent-as-orchestrator.report.md` | PDCA report |
| `.claude/agents/orchestrator.md` | Parent 프로세스 스펙 |
| `.claude/agents/{thinking,…}.md` | Specialist (스폰 대상) |
| `.claude/skills/struct-*/SKILL.md` | Parent 진입점 (변경 핵심) |
| `reference/deliverable-brief-schema.md` | Brief/Spec |
| `reference/orchestrator-review-gate.md` | Review |
| `reference/orchestrator-express-package.md` | Express Package |
| `CLAUDE.md` / `struct-docs/usage/*` | 사용자·에이전트 요약 |

---

## 11. 변경 이력

| 날짜 | 내용 |
|------|------|
| 2026-07-15 | 초안. HOST-TARGET grok-only 결정 반영. 옵션 D L1. |
| 2026-07-15 | 구조 유지(Struct agents/skills) 확정. PR1: 6× struct-*/SKILL.md Parent-as-Orch + orchestrator 배너. |
| 2026-07-15 | V2 think + V3 write PASS. KI closed. status=verified. |
| 2026-07-15 | V4 review PASS (spawn review only; ST4 force → Parent 1-edit). |
| 2026-07-15 | PR2: usage/* · workflow.mmd · scenarios 배너 · brief-schema 문구 Parent-as-Orch 정렬. |
| 2026-07-15 | PR4: `docs/archive/2026-07/cc-entrypath-notes.md` + _INDEX. A4 done. |
| 2026-07-15 | PDCA Report: `docs/04-report/features/grok-parent-as-orchestrator.report.md`. status=completed. |
| 2026-07-15 | **Archived** → `docs/archive/2026-07/grok-parent-as-orchestrator/` (design+report). |
| 2026-07-15 | Decision + KI 동일 폴더로 합쳐 아카이빙 (묶음 완성). |
