# Struct Agent Team (Minto 기반)

구조화된 논리(피라미드 원칙) 기반 에이전트 팀.

## 사용 가능한 스킬

| 명령어 | 설명 |
|--------|------|
| `/struct-think` | 아이디어를 구조화된 논리로 정리 |
| `/struct-write` | SCQA 기반 보고서·문서 생성 |
| `/struct-solve` | 문제를 구조화된 논리로 분석하고 해결안 도출 |
| `/struct-express` | 슬라이드·메모·스토리 + Express Package (1:5 Executive Brief) |
| `/struct-review` | Fidelity + Deliverable Quality 게이트 (4대 실패 유형 DT1~DT3) |
| `/struct-research` | 출처·균형·다중 출처 검증 (Phase 6) |

## 파일 구조

```
.claude/
├── skills/          # 사용자 호출 스킬 (struct-think/write/solve/express/review/research)
└── agents/          # 에이전트 정의 (orchestrator, thinking, writing, problem-solving, expression, review, research)
struct-docs/
├── 01-thinking/     # /struct-think 결과물
├── 02-writing/      # /struct-write 결과물
├── 03-solving/      # /struct-solve 결과물
├── 04-expressing/   # /struct-express 결과물
├── 05-reviewing/    # /struct-review 결과물
└── 06-researching/  # /struct-research Source Validation 결과물
.struct-memory.json   # 세션 간 컨텍스트 저장
```

## 아키텍처

스킬 → Orchestrator Agent → 전문 에이전트 (2단계 호출)

각 전문 에이전트는 Minto Thinking 로직을 내장하여 독립 실행 가능.

**Deliverable Brief (Phase 1, 2026-06)**: Orchestrator는 struct-write/solve/express 호출 시 **Brief**(목적·수요자·유형)를 수집하고 **Deliverable Spec**(템플릿·논리패턴)을 확정한 뒤 Specialist에 주입한다. Collaborative는 필수 3항목 확인, Autonomous는 추론+폴백. 스키마: `reference/deliverable-brief-schema.md`. 설계: `docs/struct-deliverable-system.design.md`.

**Writing Pipeline (Phase 3, 2026-06)**: struct-write 시 writing agent가 **W1 Skeleton → W2 Draft → W3 Audience Pass → W4 External Face** 순으로 본문 작성. `submissionTarget: true`(기본) 시 W4에서 Working 섹션 제거 후 제출본 저장 (`draftStage: submission-ready`, `submissionReady: true`). `submissionTarget: false` 시 W3까지만 (`audience-revised`). 상세: `writing.md`, `reference/submission-ready-checklist.md`, `struct-docs/usage/write.md`.

**Deliverable Review (Phase 4, 2026-06)**: struct-review 시 **Deliverable Quality** 게이트 — Brief·유형·4대 실패 유형(F1~F4), DT1(건의·틀·**logicSectionsFilled**)·DT2·DT3. D7 fail 시 W2 재생성. **DT-Submission** (Wave 2): `submissionTarget: true` 시 ST1~ST6·`submissionReady`; ST3 fail → W4. express는 `submissionReady: pass` 후. write 기본 verification: `both`. 상세: `review.md`, `reference/submission-ready-checklist.md`, `struct-docs/usage/review.md`.

**Express Package (Phase 5, 2026-06)**: `summary-detail: split-1-5` write + Review pass 후 **Executive Brief(1:5)**·Attachments Index·(meeting-material 시) Slide Deck. `/struct-express brief|package {경로}`. 상세: `expression.md`, `struct-docs/usage/express.md`.

**Source Validation (Phase 6, 2026-06)**: `/struct-research` — 사용자 제공 자료의 출처·균형·다중 출처 체크리스트(S1~S5). `research-first: true` write/solve 전처리. review `source-quality`. 상세: `research.md`, `reference/source-validation-checklist.md`.

**Thinking Reuse (2026-06)**: Brief·Spec 확정 후 relevant prior thinking을 Read하여 `## Previous Thinking Pyramid`를 주입. writing은 Pyramid Consumption, solve는 Problem Framing에 활용.

**두 모드 운영 원칙 (참여형 vs 자율형)**
- 사용자가 모드를 지정하지 않으면 기본은 **참여형(Collaborative)**.
- Orchestrator는 모드를 가장 먼저 판단하여 specialists에게 전달.
- 참여형: 중간 확인, 판단 근거 공개, 사용자 승인 루프 필수.
- 자율형: 전체 자동 실행 + 최소 요약 반환.
- 두 모드 모두 시각 자료(표·다이어그램) + 개조식 표현을 default로 사용.

## Default Output Style (기본 출력 스타일)

**모든 산출물의 기본 기조** (변경 요청 전까지 유지):

- **개조식 표현 우선**: bullet points, sub-bullets, numbered lists 중심. 긴 문단은 최소화.
- **직관적 시각 자료 적극 사용**: 표(tables), ASCII diagram, Mermaid syntax (피라미드·흐름·구조 시각화)
- **한눈에 파악 가능**하게 계층, 관계, 우선순위를 명확히 표현

**변경 규칙**
- 사용자가 "narrative로", "문단식으로", "더 부드럽게", "도표 없이" 등 요청하면 즉시 반영
- 구체적인 스타일 세부사항은 사용자가 점진적으로 적용 요청하면서 발전시킨다.
- Option 1 (참여형) 모드에서는 검토 단계에서 자연스럽게 조정
- Option 2 (자율형) 모드에서도 기본 스타일 유지 + 후속 변경 지원

이 스타일은 "직관적으로 이해하기 쉽게"라는 원칙을 반영한다. 구체적 규칙은 실제 사용 피드백을 통해 점진적으로 정교화한다.

## 템플릿 및 유지보수

- `struct-docs/templates/` 는 `deliverables/` · `patterns/` · `express/` · `shared/` 4축 디렉터리. 파일명은 영문 kebab-case (예: `patterns/incident-causal-pattern.md`)
- 템플릿/아키텍처 변경 시 `struct-docs/usage/*.md`, `.claude/agents/writing.md`, `templates/README.md` 동기화
- `struct-docs/templates/README.md` 가 템플릿 선택의 source of truth (`useWhen`, pattern 키)
- struct-docs/usage/workflow.mmd 에 전체 워크플로우 문서화
- 파일 도구 호출 시 상대 경로 우선
