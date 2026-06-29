---
tags: [struct, template, deliverable, event]
pattern: deliverable-event
deliverableType: event
name: "행사보고서"
useWhen: "Brief.deliverableType이 event일 때. 행사 기획(사전) 또는 행사 진행(사후) 보고."
outputStyle: "eventPhase 분기. planning=목적-방침, progress=SER."
eventPhases: [planning, progress]
defaultLogicPattern: objective-policy-pattern
logicSections:
  - "A.3 objective-policy-pattern (planning 필수)"
  - "B.2 incident-causal-pattern mode=ser (progress 필수)"
reference: "reference/president/대통령 보고서.md Ch.5·Ch.4"
guideNote: "Ch.5 본문 미완 — PDF gold 대조 권장"
---

# {행사명 + 기획 또는 진행}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| eventPhase | {planning | progress} |
| 행사 일시 | { } |
| 행사 장소 | { } |

## 개요

> {coreClaim — 행사 목적·핵심 메시지}

- **행사 성격**: { }
- **대상**: { }
- {planning: 건의 요약 / progress: 결과 한 줄}

---

<!-- eventPhase=planning: §A만 -->

## A. 행사 기획 (eventPhase = planning)

### A.1 행사 목적·배경

- **목적**: { }
- **배경**: { }
- **기대 효과**: { }

### A.2 행사 개요

| 항목 | 내용 |
|------|------|
| 행사명 | { } |
| 일시 | { } |
| 장소 | { } |
| 주최·주관 | { } |
| 대상·규모 | { } |

### A.3 프로그램 (logic: objective-policy-pattern — **필수 전개**)

> objective-policy-pattern 형식.
> `patterns/reports/objective-policy-pattern.md` 참조.

| 순서 | 시간 | 내용 | 담당 |
|------|------|------|------|
| 1 | { } | { } | { } |
| 2 | { } | { } | { } |

(1) **목적**: {프로그램 1 달성 상태}

    **방침**: {진행 방식}

(2) **목적**: { }

    **방침**: { }

### A.4 운영·예산·안전

- **인력·역할**: { }
- **예산**: { }
- **홍보·초청**: { }

### A.5 건의 (planning · decision-maker 시)

- [ ] {승인·결정 요청}

---

<!-- eventPhase=progress: §B만 -->

## B. 행사 진행 (eventPhase = progress)

### B.1 행사 개요 (실행)

| 항목 | 계획 | 실제 | 차이 |
|------|------|------|------|
| 일시 | { } | { } | { } |
| 참석 | { } | { } | { } |

### B.2 실행 결과 (logic: incident-causal-pattern, mode=ser — **필수 전개**)

> incident-causal (ser).
> `patterns/reports/incident-causal-pattern.md` 참조.

#### 구조 (Structure) — 계획·전제

- {원래 계획·전제 조건}

#### 사건 (Event) — 변경·지연·이슈

1. { }
2. { }

#### 대처 (Response) — 현장·운영 조치

- **즉시 대처**: { }
- **후속 대처**: { }

#### 결과·교훈

- {성과·KPI}
- {개선점}

### B.3 참석·반응

- {객관적 서술}

### B.4 후속

- {미비점·일정}

---

## 참고·첨부

- {첨부}

---

## Appendix: 품질 점검

- eventPhase: { }
- logicSectionsFilled: {A.3 목적-방침 | B.2 SER — pass/fail}
- 사용 템플릿: deliverable-event.md
- logicPattern: {objective-policy-pattern | incident-causal-pattern mode=ser}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### eventPhase 분기

| eventPhase | 섹션 | 논리 패턴 |
|------------|------|----------|
| `planning` | **§A만** | A.3 목적-방침 필수 |
| `progress` | **§B만** | B.2 SER 필수 |

### Review T1

- [ ] Phase에 맞는 A 또는 B만 출력
- [ ] planning: A.3 목적-방침 **최소 1쌍**
- [ ] progress: B.2 Structure·Event·Response **3단계**

### Gold reference

`reference/president/행사기획보고서_정부 업무관리시스템.pdf`, `행사진행보고서_정부업무관리시스템 참고자료.pdf`