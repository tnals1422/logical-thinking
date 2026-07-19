---
tags: [pdca, design, readme, onboarding, documentation]
feature: project-readme
created: 2026-07-19
status: draft
author: Struct Agent Team / Grok Build
project: logical-thinking (Struct Agent Team)
architecture: Option C — Pragmatic Standard Hub
plan: docs/01-plan/features/project-readme.plan.md
status: archived
archived: 2026-07-19
archivedTo: docs/archive/2026-07/project-readme/
---

# project-readme Design Document

> **Summary**: 루트 `README.md`를 표준 온보딩 허브로 설계한다 (Option C). 상세는 AGENTS/usage/decisions에 링크로 위임하고, AGENTS 상단에 README 상호 링크 1줄을 추가한다.
>
> **Project**: logical-thinking (Struct Agent Team)  
> **Author**: Struct Agent Team / Grok Build  
> **Date**: 2026-07-19  
> **Status**: Draft (Checkpoint 3: Option C)  
> **Planning Doc**: [project-readme.plan.md](../../01-plan/features/project-readme.plan.md)

### Pipeline References

| Phase | Document | Status |
|-------|----------|--------|
| Plan | [project-readme.plan.md](../../01-plan/features/project-readme.plan.md) | ✅ |
| Phase 1 Schema | — | N/A (문서 전용) |
| Phase 2 Conventions | AGENTS.md 문서 규칙 | ✅ 재사용 |
| Phase 3–4 Mockup/API | — | N/A |
| Decision | [HOST-TARGET grok-only](../../decisions/20260715-host-target-grok-only.md) | ✅ |

---

## Context Anchor

> Copied from Plan. Design→Do handoff continuity.

| Key | Value |
|-----|-------|
| **WHY** | 루트 진입점 부재로 온보딩 비용·문서 분산 |
| **WHO** | 본인 재진입 + 협업 기여자 (Grok Build primary) |
| **RISK** | AGENTS/usage와 중복·불일치; 과도한 장문으로 유지보수 부담 |
| **SUCCESS** | 5분 온보딩 가능; 스킬 6종·구조·링크 완비; 상세는 링크로 위임 |
| **SCOPE** | 루트 README 1파일 신설 + AGENTS 1줄 상호 링크 (코드/스킬 로직 변경 없음) |

---

## 1. Overview

### 1.1 Design Goals

1. **단일 온보딩 진입점**: 클론/재진입 시 첫 문서로 루트 `README.md` 제공
2. **허브+링크 SoT 분리**: README=요약 진입, AGENTS=규칙, usage=커맨드 상세
3. **Plan FR-01~10 전량 구현 가능**: 섹션 스펙·체크리스트로 Do 단계 모호성 제거
4. **Grok-only Runtime 서술**: HOST-TARGET 결정과 일치, CC 상세 비포함
5. **낮은 유지보수 부담**: 장문 복제 금지, 표·개조식, 동기화 포인트 최소화

### 1.2 Design Principles

- **Hub, not encyclopedia** — 깊이 있는 설명은 기존 문서 링크
- **Source of truth mapping** — 각 섹션의 primary source를 명시 (Do 시 대조)
- **One screen value** — 상단에서 소개 + 스킬 표까지 도달
- **Bidirectional discovery** — README ↔ AGENTS 최소 상호 링크
- **No code touch** — `.grok/skills|agents` 불변

---

## 2. Architecture Options

### 2.0 Architecture Comparison

| Criteria | Option A: Minimal | Option B: Clean 확장 | **Option C: Pragmatic** |
|----------|:-----------------:|:--------------------:|:-----------------------:|
| **Approach** | 소개·스킬·링크만 | 상세형 + 문서 체계 확장 | 표준 온보딩 허브 + 링크 |
| **New Files** | 1 (`README.md`) | 2~4+ | 1 (`README.md`) |
| **Modified Files** | 0 | 다수 가능 | 1 (`AGENTS.md` 1줄) |
| **Complexity** | Low | High | Medium |
| **Maintainability** | Medium | High (동기화↑) | High |
| **Effort** | Low | High | Medium |
| **Risk** | 정보 부족 | 범위 팽창 | **Low (Plan 정합)** |
| **Recommendation** | 초안/핫픽스 | 장기 문서 개편 | **Default — 선택됨** |

**Selected**: **Option C — Pragmatic Standard Hub**  
**Rationale**: Plan CP2(표준 온보딩)·FR 전 범위·Out of Scope와 일치. Minimal은 5분 온보딩 SUCCESS 미달 위험. Clean은 CONTRIBUTING/다파일 확장으로 Out of Scope 침범.

> 이하 상세 설계는 전부 Option C 기준.

### 2.1 Component Diagram (Document Roles)

```text
┌─────────────────────────────────────────────────────────────┐
│  README.md (root) — Onboarding Hub                          │
│  소개 · Quick Start · Skills · 구조 · 흐름 · Runtime · 허브   │
└────────────┬───────────────────────────────┬────────────────┘
             │ summary + link                │ summary + link
             ▼                               ▼
┌────────────────────────┐     ┌──────────────────────────────┐
│ AGENTS.md              │     │ struct-docs/usage/*          │
│ 규칙 · 아키텍처 SoT    │     │ 커맨드별 상세 가이드          │
│ (+ 상단 README 1줄)    │     │ index.md 스킬 표 출처        │
└────────────┬───────────┘     └──────────────┬───────────────┘
             │                                │
             ▼                                ▼
┌────────────────────────┐     ┌──────────────────────────────┐
│ docs/decisions/        │     │ docs/archive/ · known-issues │
│ Runtime 결정           │     │ 이력 · KI (읽기 전용 링크)   │
└────────────────────────┘     └──────────────────────────────┘
```

### 2.2 Reader Flow

```text
Clone / Re-open repo
    │
    ▼
README.md
    ├─(5min)──► 목적 이해 + 스킬 1개 선택
    ├─ Quick Start ──► Grok에서 /struct-think 시도
    ├─ 상세 규칙 필요 ──► AGENTS.md
    ├─ 커맨드 상세 ──► struct-docs/usage/{cmd}.md
    └─ Runtime 정책 ──► docs/decisions/… + archive
```

### 2.3 Dependencies

| Component | Depends On | Purpose |
|-----------|-----------|---------|
| README 소개/구조 | `AGENTS.md` | 문구·트리 출처 |
| README 스킬 표 | `AGENTS.md` + `struct-docs/usage/index.md` | 명령·설명 일치 |
| README 워크플로 | `struct-docs/usage/index.md` | 자주 쓰는 흐름 |
| README Runtime | `docs/decisions/20260715-host-target-grok-only.md` | Grok-only 근거 |
| AGENTS 1줄 링크 | `README.md` 존재 | 상호 발견성 |

---

## 3. Content Model (문서 데이터 모델)

> 앱 Entity/DB 없음. README **섹션 스키마**로 대체.

### 3.1 Section Schema

| ID | Section (H2) | Required | Max depth | Primary source | FR |
|----|--------------|:--------:|-----------|----------------|-----|
| S0 | Title + one-liner | Yes | H1 only | 신규 작성 | FR-02 |
| S1 | 이 프로젝트는? | Yes | H2 + bullets | AGENTS 요약 | FR-02 |
| S2 | 문서 역할 (README vs AGENTS vs usage) | Yes | H2 + 표 3행 | 신규 | FR-09 |
| S3 | Quick Start | Yes | H2 + 번호 목록 | 신규 (Grok 전제) | FR-03 |
| S4 | 스킬 (Commands) | Yes | H2 + 표 6행 | AGENTS + usage/index | FR-04 |
| S5 | 디렉터리 구조 | Yes | H2 + ASCII tree | AGENTS 파일 구조 | FR-05 |
| S6 | 자주 쓰는 흐름 | Yes | H2 + code/list ≥3 | usage/index | FR-06 |
| S7 | Runtime & 아키텍처 요약 | Yes | H2 + bullets + links | AGENTS + decision | FR-07 |
| S8 | 문서 허브 | Yes | H2 + 표 | 경로 고정 | FR-08 |
| S9 | 유지보수 메모 | Yes | H2 + 1~3 bullets | 신규 | Risk 완화 |

### 3.2 Section Relationships

```text
S0 Title
 └── S1 소개 ──► S2 역할 분리
              └── S3 Quick Start ──► S4 Skills
                                  └── S5 Structure
                                  └── S6 Flows
                                  └── S7 Runtime
                                  └── S8 Hub
                                  └── S9 Maintain
```

### 3.3 Link Catalog (상대 경로, 루트 기준)

| Label | Path | Used in |
|-------|------|---------|
| 프로젝트 규칙 | `AGENTS.md` | S2, S7, S8 |
| 커맨드 인덱스 | `struct-docs/usage/index.md` | S4, S6, S8 |
| think 상세 | `struct-docs/usage/think.md` | S4 |
| write 상세 | `struct-docs/usage/write.md` | S4 |
| solve 상세 | `struct-docs/usage/solve.md` | S4 |
| express 상세 | `struct-docs/usage/express.md` | S4 |
| review 상세 | `struct-docs/usage/review.md` | S4 |
| research 상세 | `struct-docs/usage/research.md` | S4 |
| workflow | `struct-docs/usage/workflow.mmd` | S6, S8 |
| templates | `struct-docs/templates/README.md` | S8 |
| Grok-only decision | `docs/decisions/20260715-host-target-grok-only.md` | S7, S8 |
| Parent-as-Orch archive | `docs/archive/2026-07/grok-parent-as-orchestrator/` | S7, S8 |
| Native layout archive | `docs/archive/2026-07/grok-native-layout/` | S8 (optional) |
| known-issues | `docs/known-issues/` | S8 |
| reference | `reference/README.md` | S8 |

---

## 4. API Specification

**N/A** — HTTP API 없음. 문서 전용 feature.

---

## 5. Document UX Design

### 5.1 README Layout (세로 스크롤)

```text
┌────────────────────────────────────────────┐
│ # Struct Agent Team                        │
│ one-liner                                  │
├────────────────────────────────────────────┤
│ ## 이 프로젝트는?  (3~5 bullets)            │
│ ## 문서 역할  (3-row table)                 │
├────────────────────────────────────────────┤  ← 1화면 목표 경계
│ ## Quick Start                             │
│ ## 스킬 (Commands)  (6-row table)          │
├────────────────────────────────────────────┤
│ ## 디렉터리 구조  (ASCII)                   │
│ ## 자주 쓰는 흐름                           │
│ ## Runtime & 아키텍처 요약                  │
│ ## 문서 허브                                │
│ ## 유지보수 메모                            │
└────────────────────────────────────────────┘
```

### 5.2 User Flow

```text
README open → 소개 이해 → 스킬 표에서 명령 선택
    → usage 상세 또는 Grok에서 /struct-* 실행
    → 규칙 필요 시 AGENTS.md
```

### 5.3 Component List (문서 블록)

| Block | Location | Responsibility |
|-------|----------|----------------|
| Title/one-liner | README S0 | 브랜드·한 줄 정의 |
| Skills table | README S4 | 6 커맨드 맵 |
| Dir tree | README S5 | 산출물 위치 |
| Hub table | README S8 | 심화 문서 라우팅 |
| AGENTS backlink | AGENTS.md top | README 발견성 |

### 5.4 README Content Checklist (Gap Detector용)

> Do 완료 시 각 항목 존재 여부 검증. 문서 feature의 Functional checklist.

#### S0 Title

- [ ] H1: 프로젝트명 포함 (`Struct Agent Team` 또는 동등)
- [ ] one-liner: Minto/피라미드/구조화 논리 중 핵심 키워드 포함

#### S1 이 프로젝트는?

- [ ] Bullet ≥ 3 (에이전트 팀, `/struct-*` 스킬, 산출물 경로 중 최소 언급)
- [ ] “무엇을 하는가”가 한 화면 내 파악 가능

#### S2 문서 역할

- [ ] 표 또는 목록: README / AGENTS / usage 3역할 구분
- [ ] README가 요약 진입점임을 명시

#### S3 Quick Start

- [ ] 전제: **Grok Build** 명시
- [ ] 단계형 목록 ≥ 2 (예: 레포 열기 → 스킬 실행)
- [ ] 실행 예시 커맨드 ≥ 1 (`/struct-think …` 등)

#### S4 스킬

- [ ] 6행: think, write, solve, express, review, research
- [ ] 각 행: 명령어 · 한 줄 설명 · 상세 링크(usage)
- [ ] 명령어 철자가 AGENTS.md와 동일

#### S5 디렉터리 구조

- [ ] `.grok/skills`, `.grok/agents` 언급
- [ ] `struct-docs/01-thinking` ~ `06-researching` 또는 동등 맵
- [ ] `docs/`, `reference/` 중 1개 이상 언급

#### S6 자주 쓰는 흐름

- [ ] 흐름 ≥ 3 (usage/index와 정합)
- [ ] `struct-docs/usage/index.md` 또는 workflow 링크

#### S7 Runtime

- [ ] Primary: Grok Build
- [ ] Parent-as-Orchestrator 요약 1~3줄
- [ ] decision 및/또는 archive 링크
- [ ] CC 동등 설치 가이드 없음

#### S8 문서 허브

- [ ] AGENTS, usage, templates, decisions, known-issues 링크
- [ ] 상대 경로, 깨진 링크 0

#### S9 유지보수

- [ ] 스킬 표 동기화 출처(AGENTS/usage) 메모 ≥ 1

#### AGENTS.md touch

- [ ] 파일 상단(제목 직후 권장)에 README 링크 1줄
- [ ] 기존 규칙 본문 의미 변경 없음

### 5.5 Section Copy Spec (Do 작성 가이드)

#### S0 — Title

```markdown
# Struct Agent Team

Minto 피라미드 원칙 기반의 구조화 논리 에이전트 팀 (Grok Build primary).
```

#### S1 — 이 프로젝트는?

필수 bullet 토픽 (문장은 Do에서 자연스럽게 작성, 의미 유지):

1. 아이디어·문제·보고서를 **구조화된 논리**로 정리
2. `/struct-think|write|solve|express|review|research` 6 스킬
3. 산출물은 `struct-docs/` 아래에 스킬별 저장
4. (권장) 참여형/자율형 모드 · 개조식 기본 스타일 한 줄

#### S2 — 문서 역할

| 문서 | 역할 |
|------|------|
| **README** (본 문서) | 온보딩·한눈에 보기 |
| **AGENTS.md** | 프로젝트 규칙·아키텍처 SoT |
| **struct-docs/usage/** | 커맨드별 상세 사용법 |

#### S3 — Quick Start

권장 단계:

1. 이 레포를 Grok Build 워크스페이스로 연다.
2. 스킬 목록은 아래 표 또는 `struct-docs/usage/index.md`를 본다.
3. 예: `/struct-think {정리할 주제}` 실행 → `struct-docs/01-thinking/` 결과 확인.
4. 보고서가 필요하면 `/struct-write` → (선택) `/struct-review`.

전제 문구 필수: “Primary runtime: **Grok Build**”.

#### S4 — 스킬 표 (고정 내용)

| 명령어 | 설명 | 상세 |
|--------|------|------|
| `/struct-think` | 아이디어를 구조화된 논리(피라미드)로 정리 | [think.md](struct-docs/usage/think.md) |
| `/struct-write` | SCQA·Brief 기반 보고서·문서 생성 | [write.md](struct-docs/usage/write.md) |
| `/struct-solve` | 문제를 구조화 분석하고 해결안 도출 | [solve.md](struct-docs/usage/solve.md) |
| `/struct-express` | 슬라이드·메모·스토리 + Express Package | [express.md](struct-docs/usage/express.md) |
| `/struct-review` | Fidelity + Deliverable Quality 게이트 | [review.md](struct-docs/usage/review.md) |
| `/struct-research` | 출처·균형·다중 출처 검증 | [research.md](struct-docs/usage/research.md) |

#### S5 — 디렉터리 구조 (고정 골격)

```text
.grok/
├── skills/          # /struct-* 스킬
└── agents/          # specialist + orchestrator(프로세스 SoT, spawn 금지)
struct-docs/
├── 01-thinking/ … 06-researching/
├── templates/
└── usage/
docs/                # PDCA · decisions · archive · known-issues
reference/           # 체크리스트 · 스키마 · 품질 루브릭
AGENTS.md            # 프로젝트 규칙
README.md            # 온보딩 허브 (본 문서)
.struct-memory.json  # 세션 컨텍스트
```

#### S6 — 자주 쓰는 흐름 (최소 3)

usage/index에서 채택:

1. think → write  
2. solve → express slide  
3. research → write → review  
4. (권장 4번째) think → write → review  

#### S7 — Runtime 요약 포인트

- Primary: Grok Build  
- Parent-as-Orchestrator: Parent가 Brief/Spec/Review Gate/저장 수행; `orchestrator` 자식 스폰 **금지**  
- skills/agents 경로: `.grok/`  
- 링크: decision stub + parent-as-orch archive  

#### S8 — 문서 허브 표

| 문서 | 설명 |
|------|------|
| [AGENTS.md](AGENTS.md) | 규칙·아키텍처 |
| [usage/index.md](struct-docs/usage/index.md) | 커맨드 인덱스 |
| [templates/README.md](struct-docs/templates/README.md) | 템플릿 선택 SoT |
| [workflow.mmd](struct-docs/usage/workflow.mmd) | 워크플로 다이어그램 |
| [HOST-TARGET decision](docs/decisions/20260715-host-target-grok-only.md) | Runtime 결정 |
| [Parent-as-Orch archive](docs/archive/2026-07/grok-parent-as-orchestrator/) | 오케스트레이션 이력 |
| [known-issues](docs/known-issues/) | 알려진 이슈 |
| [reference/](reference/README.md) | 품질·Brief·체크리스트 |

#### S9 — 유지보수 메모

- 스킬 표 변경 시: `AGENTS.md` + `struct-docs/usage/index.md`를 먼저 고치고 README 표를 맞출 것  
- 장문 규칙/파이프라인 설명은 README에 추가하지 말 것 (링크)

#### AGENTS.md patch (module-2)

제목 `# Struct Agent Team …` 직후:

```markdown
> 온보딩·한눈에 보기: [README.md](README.md)
```

---

## 6. Error Handling (문서 품질 실패 모드)

| Code | 증상 | 원인 | 대응 |
|------|------|------|------|
| DOC-DUP | README가 AGENTS 장문 복제 | 허브 원칙 위반 | 요약 축소 + 링크 |
| DOC-LINK | 상대 경로 404 | 오타·이동 | §3.3 카탈로그 대조 |
| DOC-DRIFT | 스킬명/설명 불일치 | 출처 미대조 | AGENTS/usage 재동기화 |
| DOC-SCOPE | CC 설치·코드 변경 포함 | Out of Scope | 삭제 |
| DOC-EMPTY | 필수 섹션 누락 | §5.4 미충족 | 섹션 보충 |

---

## 7. Security Considerations

문서 전용 — 런타임 보안 체크리스트 대부분 N/A.

- [x] 시크릿·토큰·개인정보 README 미포함
- [x] 외부 추적 스크립트/이미지 없음
- [ ] (해당 시) 비공개 경로·내부 URL 노출 금지 — Do 시 확인

---

## 8. Test Plan (문서 검증)

> 코드 테스트 대신 **정적 문서 검증**. Check 단계에서 실행.

### 8.1 Test Scope

| Type | Target | Tool | Phase |
|------|--------|------|-------|
| L1 Structural | 파일 존재, 섹션 H2 목록 | 파일 시스템 + 헤딩 파싱 | Check |
| L2 Functional | §5.4 Content Checklist | 수동/체크리스트 | Check |
| L3 Acceptance | Plan A1~A4 시나리오 | 수동 5분 리딩 | Check |
| L-link | §3.3 전 링크 대상 존재 | `test -f` / 스크립트 | Do+Check |

### 8.2 L1 Structural Scenarios

| # | Check | Expected |
|---|-------|----------|
| 1 | `README.md` at repo root | exists |
| 2 | H2 목록에 S1~S9 대응 섹션 | 8+ H2 (S2 포함 시) |
| 3 | AGENTS.md contains `README.md` link | yes |
| 4 | No changes under `.grok/` | git clean for those paths |

### 8.3 L2 Functional Scenarios

| # | Check | Expected |
|---|-------|----------|
| 1 | Skills table 6 rows | exact 6 commands |
| 2 | Command strings match AGENTS | exact match |
| 3 | Quick Start mentions Grok Build | present |
| 4 | Runtime has decision link | present |
| 5 | No English-only primary body | Korean primary |
| 6 | Role separation table present | README/AGENTS/usage |

### 8.4 L3 Acceptance (Plan A1~A4)

| # | Scenario | Success |
|---|----------|---------|
| A1 | README only | 목적·스킬·다음 문서 파악 |
| A2 | think→write 탐색 | S6 또는 usage 링크 |
| A3 | Runtime | Grok-only + decision 링크 |
| A4 | 규칙 필요 | AGENTS로 이동, README에 규칙 전문 없음 |

### 8.5 Seed Data

N/A

---

## 9. Clean Architecture (문서 레이어)

### 9.1 Layer Structure

| Layer | Responsibility | Location |
|-------|---------------|----------|
| **Entry** | 온보딩 허브 | `README.md` |
| **Rules** | 프로젝트 규칙 SoT | `AGENTS.md` |
| **Guides** | 커맨드 상세 | `struct-docs/usage/` |
| **History** | 결정·아카이브 | `docs/decisions/`, `docs/archive/` |
| **Reference** | 스키마·체크리스트 | `reference/` |

### 9.2 Dependency Rules

```text
README  ──reads──►  AGENTS, usage, decisions, archive, reference
AGENTS  ──link──►   README (1줄 only)
usage/decisions     ──not modified──  by this feature
```

### 9.3 This Feature's Layer Assignment

| Artifact | Layer | Path |
|----------|-------|------|
| README.md | Entry | `/README.md` |
| AGENTS 1-line | Rules (minimal) | `/AGENTS.md` |

---

## 10. Coding Convention Reference

### 10.1 This Feature's Conventions

| Item | Convention |
|------|------------|
| Language | 한국어 |
| Style | 개조식 · 표 · ASCII 트리 (장문 문단 최소화) |
| Links | 루트 기준 상대 경로 |
| Emoji | 절제 (표/헤딩 우선) |
| Headings | AT-N 순서 유지, H1 1개 |
| Target length | 대략 80~150줄 (가이드, hard limit 아님) |
| FR-09 | 장문 복제 금지 |

### 10.2 Naming

| Target | Rule | Example |
|--------|------|---------|
| Feature | kebab-case | `project-readme` |
| Plan/Design paths | `docs/0x-*/features/{feature}.*` | 본 문서 |
| Skill commands | `/struct-{verb}` | `/struct-think` |

---

## 11. Implementation Guide

### 11.1 File Structure

```text
/ (repo root)
├── README.md          # CREATE — full hub content
└── AGENTS.md          # MODIFY — one backlink line only

# UNCHANGED
.grok/
struct-docs/
docs/                  # except this design/plan already present
reference/
```

### 11.2 Implementation Order

1. [ ] **module-1**: `README.md` 초안 — S0~S9 전부 (§5.5 스펙 준수)
2. [ ] **module-1b**: §3.3 링크 대상 존재 여부 일괄 검증 + 수정
3. [ ] **module-2**: `AGENTS.md` 상단 README 1줄 추가
4. [ ] **module-3**: §5.4 체크리스트 자가 검증 + Plan FR 매핑 확인
5. [ ] (Do 종료) git status로 `.grok/` 미변경 확인

### 11.3 Session Guide

#### Module Map

| Module | Scope Key | Description | Estimated Turns |
|--------|-----------|-------------|----------------:|
| README 본문 | `module-1` | S0~S9 작성 + 링크 검증 | 15–25 |
| AGENTS 상호 링크 | `module-2` | 1줄 패치 | 3–5 |
| Self-check | `module-3` | §5.4 + FR + Out of Scope | 5–10 |

#### Recommended Session Plan

| Session | Phase | Scope | Turns |
|---------|-------|-------|------:|
| 완료분 | Plan + Design | 전체 | (done) |
| Next | Do | `--scope module-1` 또는 full | 20–30 |
| Same or next | Do | `module-2` + `module-3` | 10–15 |
| Follow | Check | `/pdca analyze project-readme` | 15–25 |

**Do 명령 예시**

```bash
/pdca do project-readme
# 또는
/pdca do project-readme --scope module-1
```

### 11.4 Do-phase Writing Rules

1. §5.5 고정 표/트리를 우선 반영한 뒤 문장만 다듬을 것  
2. AGENTS 장문(Writing Pipeline, Phase 상세 등)을 README에 복사하지 말 것  
3. 스킬 설명 문구는 AGENTS 표와 의미 동등 (표현 소폭 축약 허용)  
4. 완료 전: `for f in …; do test -f "$f" || echo MISSING; done` 로 링크 검증  

### 11.5 FR Traceability

| FR | Design coverage |
|----|-----------------|
| FR-01 | §11.1 CREATE README |
| FR-02 | S0, S1 |
| FR-03 | S3 |
| FR-04 | S4 + §5.5 table |
| FR-05 | S5 tree |
| FR-06 | S6 |
| FR-07 | S7 |
| FR-08 | S8 + §3.3 |
| FR-09 | Principles + S2 + S9 |
| FR-10 | §10.1 |

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-07-19 | Initial design — Option C selected (CP3) | Struct Agent Team / Grok Build |
