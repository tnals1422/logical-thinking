# Orchestrator — Express Package 후속 트리거 (Phase 5)

> **목적**: write 완료 후 Express Package (brief/package/slide-deck) 트리거 로직 중앙화.
> **사용**: orchestrator.md 고수준만 유지하고 상세는 이 파일 Read.

## Step EP0: 선행 게이트 (submissionTarget: true)

| 조건 | Package |
|------|---------|
| `submissionReady: pass` (Review Data 또는 대상 frontmatter) | 진행 가능 |
| `submissionReady: fail` 또는 DT-Submission 위반 | **보류** — W4/W3 재생성 후 review 재실행 |
| `submissionTarget: false` | `submissionReady` 검사 **skip** (Working본 — express는 명시 요청만) |

## Step EP0b: 트리거 조건

| 조건 | 동작 |
|------|------|
| `Brief.summaryDetailRatio: split-1-5` | Executive Brief 생성 후보 |
| `Brief.summaryDetailRatio: summary-only` | `packageMode: brief` (1페이지) |
| `deliverableType: meeting-material` + split/summary | `package` 시 slide-deck 추가 |
| User Input `/struct-express brief {경로}` | 명시적 Full Report → express |
| Options `package: true` | `packageMode: package` |
| `split-1-5` 미해당 + 명시 없음 | **스킵** (기존 slide/memo/story만) |

## Step EP1: 모드별 실행

| 모드 | 동작 |
|------|------|
| **Autonomous** | split-1-5 + write pass → **자동** express (`package` 또는 `brief`; meeting-material이면 slide-deck 포함) |
| **Collaborative** | "Executive Brief(1:5 요약)와 첨부 목차를 생성할까요?" 확인 후 진행 |

Review `deliverableQuality: fail` · `submissionReady: fail` · `force_rework` 진행 중이면 **Package 트리거 보류**.

## Step EP2: express 호출 Context

```
## Task
express 수행

## Express Package
packageMode: brief | package | slide-deck | attachments
fullReportSource: {방금 저장한 write 경로}
trigger: post-write-auto | user-explicit

## Full Report Source
path: {fullReportSource}
{Read한 Full Report 전문 또는 핵심 발췌 + "전문은 path 참조"}

## Brief / Deliverable Spec
{write 시 사용한 Brief·Spec 재주입 — summaryDetailRatio 유지}

## Format
brief   # packageMode에 따라 expression이 템플릿 선택
```

- `packageMode: package` → expression이 brief → (meeting-material 시 slide-deck) → attachments-index **순 생성**
- 각 파일 저장 후 `briefs`·`previousDocuments`에 `linkedFullReport` 연계

## Step EP3: Package 완료 보고

- Autonomous: "패키지: executive-brief + attachments-index 저장 (Full: ...)" 한 줄
- Collaborative: 생성 파일 목록 + 링크 관계 표

express **명령 직접 호출** 시: User Input에 경로 있으면 `## Full Report Source` Read 후 동일 절차.

참조: `reference/agent-shared-contract.md`, expression.md, `struct-docs/templates/express/*.md`
