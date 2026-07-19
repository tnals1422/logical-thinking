# Struct Agent Team

Minto 피라미드 원칙 기반의 구조화 논리 에이전트 팀 (Grok Build primary).

## 이 프로젝트는?

- 아이디어·문제·보고서를 **구조화된 논리(피라미드 원칙)** 로 정리하는 에이전트 팀입니다.
- `/struct-think` · `write` · `solve` · `express` · `review` · `research` **6개 스킬**로 사고 → 문서 → 검수 흐름을 지원합니다.
- 산출물은 `struct-docs/` 아래에 스킬별로 저장됩니다.
- 기본 모드는 **참여형(Collaborative)** 이며, 산출물 스타일은 **개조식 + 표 + 다이어그램**입니다.

## 문서 역할

| 문서 | 역할 |
|------|------|
| **README** (본 문서) | 온보딩·한눈에 보기 (요약 진입점) |
| **[AGENTS.md](AGENTS.md)** | 프로젝트 규칙·아키텍처 SoT |
| **[struct-docs/usage/](struct-docs/usage/index.md)** | 커맨드별 상세 사용법 |

## Quick Start

Primary runtime: **Grok Build**

1. 이 레포를 Grok Build 워크스페이스로 엽니다.
2. 스킬 목록은 아래 표 또는 [커맨드 인덱스](struct-docs/usage/index.md)를 확인합니다.
3. 예: `/struct-think {정리할 주제}` 실행 → 결과는 `struct-docs/01-thinking/` 에 저장됩니다.
4. 보고서가 필요하면 `/struct-write {주제}` → (선택) `/struct-review` 로 품질 게이트를 겁니다.

## 스킬 (Commands)

| 명령어 | 설명 | 상세 |
|--------|------|------|
| `/struct-think` | 아이디어를 구조화된 논리(피라미드)로 정리 | [think.md](struct-docs/usage/think.md) |
| `/struct-write` | SCQA·Brief 기반 보고서·문서 생성 | [write.md](struct-docs/usage/write.md) |
| `/struct-solve` | 문제를 구조화 분석하고 해결안 도출 | [solve.md](struct-docs/usage/solve.md) |
| `/struct-express` | 슬라이드·메모·스토리 + Express Package | [express.md](struct-docs/usage/express.md) |
| `/struct-review` | Fidelity + Deliverable Quality 게이트 | [review.md](struct-docs/usage/review.md) |
| `/struct-research` | 출처·균형·다중 출처 검증 | [research.md](struct-docs/usage/research.md) |

전체 인덱스: [struct-docs/usage/index.md](struct-docs/usage/index.md)

## 디렉터리 구조

```text
.grok/
├── skills/          # /struct-* 스킬
└── agents/          # specialist + orchestrator(프로세스 SoT, spawn 금지)
struct-docs/
├── 01-thinking/     # /struct-think 결과
├── 02-writing/      # /struct-write 결과
├── 03-solving/      # /struct-solve 결과
├── 04-expressing/   # /struct-express 결과
├── 05-reviewing/    # /struct-review 결과
├── 06-researching/  # /struct-research 결과
├── templates/       # deliverables · patterns · express · shared
└── usage/           # 커맨드 사용 가이드
docs/                # PDCA · decisions · archive · known-issues
reference/           # 체크리스트 · 스키마 · 품질 루브릭
AGENTS.md            # 프로젝트 규칙
README.md            # 온보딩 허브 (본 문서)
.struct-memory.json  # 세션 컨텍스트
```

## 자주 쓰는 흐름

```text
/struct-think {주제}  →  /struct-write {주제}           # 생각 정리 → 보고서
/struct-solve {문제}  →  /struct-express slide {주제}  # 문제 분석 → 발표
/struct-research → /struct-write → /struct-review      # 출처 검증 → 문서 → 게이트
/struct-think → /struct-write → /struct-review         # 고품질 문서 (마지막 게이트)
```

다이어그램: [workflow.mmd](struct-docs/usage/workflow.mmd) · 상세: [usage/index.md](struct-docs/usage/index.md)

## Runtime & 아키텍처 요약

- **Primary runtime**: Grok Build
- **오케스트레이션**: Parent-as-Orchestrator — Parent가 Brief · Spec · Review Gate · 저장을 수행. `subagent_type: "orchestrator"` 자식 스폰은 **금지**. specialist만 spawn.
- **자산 경로**: skills/agents → `.grok/`
- **결정·이력**: [HOST-TARGET grok-only](docs/decisions/20260715-host-target-grok-only.md) · [Parent-as-Orchestrator archive](docs/archive/2026-07/grok-parent-as-orchestrator/)

규칙·아키텍처 전문: [AGENTS.md](AGENTS.md)

## 문서 허브

| 문서 | 설명 |
|------|------|
| [AGENTS.md](AGENTS.md) | 규칙·아키텍처 SoT |
| [usage/index.md](struct-docs/usage/index.md) | 커맨드 인덱스 |
| [templates/README.md](struct-docs/templates/README.md) | 템플릿 선택 SoT |
| [workflow.mmd](struct-docs/usage/workflow.mmd) | 워크플로 다이어그램 |
| [HOST-TARGET decision](docs/decisions/20260715-host-target-grok-only.md) | Runtime 결정 |
| [Parent-as-Orch archive](docs/archive/2026-07/grok-parent-as-orchestrator/) | 오케스트레이션 이력 |
| [Native layout archive](docs/archive/2026-07/grok-native-layout/) | `.grok` 네이티브 레이아웃 이력 |
| [known-issues](docs/known-issues/) | 알려진 이슈 |
| [reference/](reference/README.md) | 품질·Brief·체크리스트 |

## 유지보수 메모

- 스킬 표·설명 변경 시: `AGENTS.md`와 `struct-docs/usage/index.md`를 **먼저** 수정한 뒤 본 README 표를 맞춥니다.
- 장문 규칙·파이프라인·Phase 상세는 README에 넣지 말고 해당 문서 링크로 위임합니다.
