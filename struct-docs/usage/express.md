---
tags: [struct, usage, express]
created: 2026-06-17
type: cheatsheet
---
# /struct-express — 슬라이드/메모/스토리 + Express Package

## 문법
```
/struct-express {텍스트}                    → 슬라이드 (기본값)
/struct-express slide {텍스트}              → Marp 호환 슬라이드
/struct-express memo {텍스트}               → 1페이지 메모
/struct-express story {텍스트}              → 스토리텔링
/struct-express brief {Full Report 경로}    → Executive Brief 1:5
/struct-express package {Full Report 경로}  → 요약 + 첨부목차 (+ 회의 slide)
```

## Express Package (Phase 5)

write 산출물을 **패키지**로 분해·연계합니다.

```
Full Report (02-writing/)
├── Executive Brief      — 약 1/5 (취지·결론·건의)
├── Meeting Slide Deck   — meeting-material 시
└── Attachments Index    — 첨부·참고 링크 목차
```

| packageMode | 템플릿 | 저장 접미사 |
|-------------|--------|------------|
| `brief` | `express-executive-brief.md` | `-executive-brief` |
| `slide-deck` | `express-meeting-slide-deck.md` | `-slide-deck` |
| `attachments` | `express-attachments-index.md` | `-attachments-index` |
| `package` | 위 조합 | 여러 파일 |

**Executive Brief 규칙**: 보고 취지 + 결론·건의 중심 · 세부는 Full Report 링크 · 분량 ≈ 상세본 20%

## 자동 트리거 (write 후)

| 조건 | Autonomous | Collaborative |
|------|------------|---------------|
| `summary-detail: split-1-5` + Review pass | brief/package 자동 | 생성 확인 질문 |
| `meeting-material` + split-1-5 | slide-deck 포함 package | 동일 |
| Review fail / rework 중 | 보류 | 보류 |

예: `/struct-write ... summary-detail:split-1-5` → write → review → express package

## Brief (Phase 1 + 5)

| 필드 | express 영향 |
|------|-------------|
| `summary-detail: split-1-5` | Executive Brief 1:5 |
| `summary-detail: summary-only` | 1페이지 brief |
| `audience: decision-maker` | 조치·건의 블록 강조 |
| `deliverable-type: meeting-material` | Meeting Slide Deck |

## 모드
- **참여형**: package 생성 전 확인 · 파일 목록 표시
- **자율형**: write+review pass 후 자동 package · 요약 한 줄

Orchestrator: **Brief → Spec → Full Report Read → Express Package**

## 예시
```
/struct-express brief struct-docs/02-writing/20260628-policy-plan.md
/struct-express package struct-docs/02-writing/20260628-meeting.md
/struct-express 자세히 같이 slide 신제품 런칭 전략 발표
/struct-write 결정권자용 정책기획 ... summary-detail:split-1-5
```

## 출력
- Legacy: `slide` / `memo` / `story` → `struct-docs/04-expressing/YYYYMMDD-{title}.md`
- Package: `YYYYMMDD-{title}-executive-brief.md` 등 · frontmatter `linkedFullReport`, `packageRole`
- coreClaim → 핵심 메시지 · Full Report §구조 매핑

## 관련
- `/struct-write` + `split-1-5` → Orchestrator 자동 package (Phase 5)
- 템플릿: `struct-docs/templates/express/*.md`
- 전체 흐름: [workflow.mmd](workflow.mmd)