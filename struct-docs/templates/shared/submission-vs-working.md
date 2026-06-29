---
tags: [struct, template, shared]
pattern: submission-vs-working
name: "Submission vs Working 섹션 구분"
useWhen: "W4 External Face · submission-ready 판정. 단독 산출물 아님."
outputStyle: "제출본에 포함/제외 섹션 목록"
---

## Submission (수요자-facing — `02-writing/` 최종본)

| 포함 | 예 |
|------|-----|
| 표지 메타 | 작성일·작성자·보고 목적 |
| 개요 | coreClaim 인용·핵심 bullet |
| 본문 | deliverable 고수준 섹션 + `(logic: …)` 앵커 내부 패턴 단계 |
| 건의·결론 | 유형별 |
| 참고·첨부 | 외부 URL·자료명 |
| 출처·가정 | research·fact 구분 (해당 시) |

## Working (파이프라인 — W4에서 **제거**)

| 제외 | 이유 |
|------|------|
| `## Audience Pass` | W3 내부 점검 |
| `## MECE 검증 결과` | 품질 감사 흔적 |
| `## Writing Pipeline Meta` | 에이전트 실행 로그 |
| `## Appendix: 품질 점검` | logicSectionsFilled 등 |
| `<!-- … -->` | 템플릿 분기 주석 |
| struct-docs 내부 경로 | review·재생성 안내 |

상태는 **frontmatter**만: `draftStage`, `submissionReady`, `writingPipeline`.

상세: `reference/submission-ready-checklist.md` §4