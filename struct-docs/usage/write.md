---
tags: [struct, usage, write]
created: 2026-06-17
type: cheatsheet
---
# /struct-write — 문서/보고서 생성

## 문법
```
/struct-write {문서 주제 또는 지시}
/struct-write {주제} template:{템플릿명}
```

## 모드
- **참여형 (기본)**: "자세히 같이", "검토하면서" → Prior 확인 + coreClaim/Level1 논의 + 템플릿 선택 확인
- **자율형**: "빠르게", "초안만" → prior 자동 소비 + 템플릿 자동 선택 + 변경 요약만

Orchestrator가 prior pyramid 주입과 모드 전달, 템플릿 선택 전후 컨텍스트 관리를 담당합니다.

## 언제 쓰나
- 업무 보고서, 기획서, 제안서를 빠르게 작성할 때
- `/think`로 정리한 생각을 문서로 만들 때
- 다양한 템플릿 (기본 SCQA, I-A-E-J, 목적-방침, 구조-사건-대처, 상태-트리거-사고-손해 등)이 필요할 때

## 예시
```
/struct-write 자세히 같이 분기 실적 보고서 작성
/struct-write 빠르게 신규 기능 도입 제안서
/struct-write {주제} template:objective-policy-pattern
```

## 템플릿 선택
Agent가 pyramid 분석 후 내용 특성에 따라 자동 선택 (Collaborative에서는 확인):

| 템플릿 파일                            | 주요 용도                     |
|----------------------------------------|-------------------------------|
| report-default.md                      | 기본 SCQA 보고서             |
| iaej-pattern.md                        | 기반구조-행동-사건-판단      |
| structure-event-response-pattern.md    | 구조-사건-대처               |
| state-trigger-accident-damage-pattern.md | 상태-트리거-사고-손해       |
| objective-policy-pattern.md            | 목적-방침                    |

자세한 선택 기준 및 `useWhen` 은 `struct-docs/templates/README.md` 참조.
`struct-docs/templates/` 에 새 파일을 추가하면 자동으로 인식됩니다.

## 출력
- 선택된 템플릿 구조 따름 (기본 SCQA)
- Executive Summary + 근거 중심 (개조식 + 표 우선)
- 기본 스타일: 개조식 + 시각 자료
- 저장: `struct-docs/02-writing/YYYYMMDD-{title}.md`

## 관련
- `/struct-think` 먼저 구조화 → Orchestrator가 **Prior Thinking Pyramid** 자동 주입 → writing agent가 Pyramid Consumption 규칙 적용.
- 템플릿 선택 및 목록: `struct-docs/templates/README.md` (영문 파일명 사용: report-default.md, iaej-pattern.md, objective-policy-pattern.md 등)
- 전체 흐름: [workflow.mmd](workflow.mmd)
- 상세 시나리오: [scenarios.md](scenarios.md) (S02, S05, S06)
