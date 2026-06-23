# Struct Agent Team (Minto 기반)

구조화된 논리(피라미드 원칙) 기반 에이전트 팀.

## 사용 가능한 스킬

| 명령어 | 설명 |
|--------|------|
| `/struct-think` | 아이디어를 구조화된 논리로 정리 |
| `/struct-write` | SCQA 기반 보고서·문서 생성 |
| `/struct-solve` | 문제를 구조화된 논리로 분석하고 해결안 도출 |
| `/struct-express` | 발표 슬라이드, 메모, 스토리텔링으로 변환 |
| `/struct-review` | write/solve/express 결과물의 Pyramid Consumption Fidelity 최종 검증 (게이트) |

## 파일 구조

```
.claude/
├── skills/          # 사용자 호출 스킬 (/struct-think, /struct-write, /struct-solve, /struct-express)
└── agents/          # 에이전트 정의 (orchestrator, thinking, writing, problem-solving, expression, review)
struct-docs/
├── 01-thinking/     # /struct-think 결과물
├── 02-writing/      # /struct-write 결과물
├── 03-solving/      # /struct-solve 결과물
├── 04-expressing/   # /struct-express 결과물
└── 05-reviewing/    # /struct-review Fidelity 보고서 결과물
.struct-memory.json   # 세션 간 컨텍스트 저장
```

## 아키텍처

스킬 → Orchestrator Agent → 전문 에이전트 (2단계 호출)

각 전문 에이전트는 Minto Thinking 로직을 내장하여 독립 실행 가능.

**Thinking Reuse 개선 (2026-06)**: Orchestrator는 struct-write/solve/express 호출 시 relevant prior thinking 파일을 Read하여 구조화된 `## Previous Thinking Pyramid`를 Context에 주입한다. writing은 Pyramid Consumption 규칙으로, solve는 Problem Framing 용도로 prior를 활용한다. standalone 호출은 기존 동작 유지.

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

- `struct-docs/templates/` 파일명은 영문 kebab-case만 사용 (예: `structure-event-response-pattern.md`)
- 템플릿/아키텍처 변경 시 `struct-docs/usage/*.md`, `.claude/agents/writing.md`, `templates/README.md` 동기화
- `struct-docs/templates/README.md` 가 템플릿 선택의 source of truth (`useWhen`, pattern 키)
- struct-docs/usage/workflow.mmd 에 전체 워크플로우 문서화
- 파일 도구 호출 시 상대 경로 우선
