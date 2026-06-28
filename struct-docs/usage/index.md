---
tags: [struct, usage, index]
created: 2026-06-17
type: cheatsheet
---
# Struct Agent Team — 커맨드 인덱스

| 커맨드 | 설명 | 상세 |
|--------|------|------|
| `/struct-think {주제}` | 아이디어 → 구조화된 논리(피라미드) | [think.md](think.md) |
| `/struct-write {주제}` | Brief 기반 보고서/문서 생성 | [write.md](write.md) |
| `/struct-solve {문제}` | 가설 기반 문제 분석 + 해결안 | [solve.md](solve.md) |
| `/struct-express [brief\|package\|slide\|memo\|story] {텍스트}` | 패키지·슬라이드·메모·스토리 | [express.md](express.md) |
| `/struct-review` | Fidelity + Deliverable Quality 게이트 | [review.md](review.md) |
| `/struct-research {자료}` | 출처·균형·다중 출처 검증 | [research.md](research.md) |

## Brief (Phase 1)
write · solve · express 호출 시 Orchestrator가 **purpose / audience / deliverable-type** Brief를 수집합니다.
- 상세: `reference/deliverable-brief-schema.md`
- 설계: `docs/struct-deliverable-system.design.md`

## Writing Pipeline (Phase 3)
`/struct-write` 시 writing agent가 **W1 뼈대 → W2 초안 → W3 수요자 관점 수정** 순으로 본문 작성.
- 상세: [write.md](write.md) § Writing 3단계

## Deliverable Review (Phase 4)
`/struct-review` 시 **Deliverable Quality** — 4대 실패 유형(F1~F4), DT1(건의·틀) 검증. write 후 기본 `both`.
- 상세: [review.md](review.md)

## Express Package (Phase 5)
`split-1-5` write + Review pass → **Executive Brief**·첨부목차 (자동/확인). `brief` / `package` 명령 지원.
- 상세: [express.md](express.md)

## Source Validation (Phase 6)
`/struct-research` 또는 `research-first:true` write 전 — S1~S5 출처·균형 체크리스트.
- 상세: [research.md](research.md)

## 자주 쓰는 흐름
```
/struct-think {주제}  →  /struct-write {주제}        # 생각 정리 → Brief 보고서
/struct-solve {문제}  →  /struct-express slide {주제} # 문제 분석 → 발표
/struct-express memo {내용}                    # 빠른 메모
/struct-research → /struct-write → /struct-review  # 출처 검증 → 보고서 → 게이트
/struct-think → /struct-write → /struct-review  # 고품질 문서 (마지막 게이트)
/struct-think → /struct-solve → /struct-review  # 문제 해결 후 Fidelity 확인
```

전체 워크플로우는 [workflow.mmd](workflow.mmd) 참조.

## 저장 위치
`/struct-think` → `struct-docs/01-thinking/` | `/struct-write` → `struct-docs/02-writing/`
`/struct-solve` → `struct-docs/03-solving/`  | `/struct-express` → `struct-docs/04-expressing/`
`/struct-review` → `struct-docs/05-reviewing/`
`/struct-research` → `struct-docs/06-researching/`

> 템플릿 파일은 `struct-docs/templates/` 에 영문 파일명으로 관리됩니다. (자세한 내용은 templates/README.md)

## 모드 (두 가지 운영 방식)
- **참여형 (Collaborative, 기본)**: "자세히 같이 해줘", "천천히 검토하면서", "내가 참여할게" → 중간 확인 + 설명 많음
- **자율형 (Autonomous)**: "빠르게", "초안만", "한 번에" → 전체 자동 + 최소 요약

모드 미지정 시 참여형. 자연어로 전달하거나 `mode: collaborative|autonomous`.

## 에이전트 구조
Orchestrator(sonnet) → Thinking/Writing/ProblemSolving(opus) + Expression(sonnet) + Review(sonnet)

Review는 write/solve/express **후**의 Fidelity 게이트이며, Orchestrator가 재생성 루프를 제어한다.

> **최근 업데이트**: 템플릿 파일명 영문화 (structure-event-response-pattern.md 등), 전체 워크플로우 문서화

## 참고 자료
- **워크플로우 다이어그램**: [workflow.mmd](workflow.mmd)
- **템플릿 목록 및 선택 기준**: `struct-docs/templates/README.md` (report-default, iaej, structure-event-response 등)
- **상세 시나리오**: `../../docs/struct-usage-scenarios.md` (또는 [scenarios.md](scenarios.md))
