---
tags: [struct, template, deliverable, event]
pattern: deliverable-event
deliverableType: event
name: "행사보고서"
useWhen: "Brief.deliverableType이 event일 때. 행사 기획(사전) 또는 행사 진행(사후) 보고. eventPhase로 분기."
outputStyle: "서술형 개조식 + 일정표. 제목에 행사명·기획/진행 성격 반영."
eventPhases: planning | progress
defaultLogicPattern: objective-policy-pattern
reference: "reference/president/대통령 보고서.md Ch.5·Ch.4 제목예시 + PDF gold"
guideNote: "Ch.5 본문 미완 — PDF·제목 예시 기반 skeleton (2026-06)"
---

# {행사명 + 기획 또는 진행 — 제목 예: OO 학습 행사계획 및 말씀 참고자료}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| eventPhase | {planning \| progress} |
| 행사 일시 | {예정 또는 실시 일시} |
| 행사 장소 | { } |

## 개요

> {coreClaim — 행사 목적·핵심 메시지 1문장}

- **행사 성격**: {학습·발표·기념·시찰 등}
- **대상**: {참석·초청 대상}
- {planning: 승인·건의 요약 / progress: 결과 한 줄}

---

<!-- eventPhase=planning: §A만 작성. §B 생략 -->

## A. 행사 기획 (eventPhase = planning)

### A.1 행사 목적·배경

- **목적**: {행사로 달성할 것}
- **배경**: {왜 이 행사가 필요한가}
- **기대 효과**: {행사 후 기대 상태}

### A.2 행사 개요

| 항목 | 내용 |
|------|------|
| 행사명 | { } |
| 일시 | {YYYY-MM-DD HH:mm~} |
| 장소 | { } |
| 주최·주관 | { } |
| 대상·규모 | {인원·직급} |
| 형식 | {대면/화상/혼합} |

### A.3 프로그램

| 순서 | 시간 | 내용 | 담당·비고 |
|------|------|------|----------|
| 1 | { } | { } | { } |
| 2 | { } | { } | { } |

(1) **목적**: {세부 프로그램 목적}

    **방침**: {진행 방식 — objective-policy 쌍}

(2) **목적**: { }

    **방침**: { }

### A.4 운영·예산·안전

- **인력·역할**: { }
- **예산**: {항목·금액}
- **안전·비상**: {해당 시}
- **홍보·초청**: { }

### A.5 말씀·참고자료 (해당 시)

> VIP·결정권자 말씀 참고용 — 가이드 제목 예시 반영

- **말씀 포인트**: {제안 메시지 bullet}
- **참고 배경**: {숙지할 맥락}
- **Q&A 예상**: { }

### A.6 건의 사항 (planning · decision-maker 시)

- [ ] {일정·예산·장소 승인 등}
- [ ] { }

---

<!-- eventPhase=progress: §B만 작성. §A 생략 -->

## B. 행사 진행 (eventPhase = progress)

### B.1 행사 개요 (실행)

| 항목 | 계획 | 실제 | 차이 |
|------|------|------|------|
| 일시 | { } | { } | { } |
| 장소 | { } | { } | { } |
| 참석 | {명} | {명} | { } |

### B.2 실행 결과

- {프로그램별 진행 요약 — 계획 대비}
- {변경·지연·취소 사항}

### B.3 참석·반응

| 구분 | 인원·주요 참석 | 비고 |
|------|---------------|------|
| 초청 | { } | { } |
| 실참석 | { } | { } |

- **현장 분위기·반응**: {객관적 서술}

### B.4 주요 내용·성과

- {핵심 발표·시연·합의 내용}
- {성과·KPI — 해당 시}

### B.5 참고자료

- {사진·자료·배포물 목록}
- {말씀 참고자료 링크 — 행사 후 정리본}

### B.6 후속·개선

- {미비점·개선 제안}
- {후속 일정}

---

## 참고·첨부

- {첨부 목록}

---

## Appendix: 품질 점검

- eventPhase: {planning \| progress}
- 해당 Phase 섹션만 출력 (A 또는 B): {pass / fail}
- 사용 템플릿: deliverable-event.md
- guideNote: Ch.5 본문 미완 — PDF 대조 권장

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### eventPhase 분기

| eventPhase | Brief 힌트 | 작성 섹션 | 시점 |
|------------|-----------|----------|------|
| `planning` | 기획, 계획, 사전, 말씀 참고 | **§A만** | 행사 전 |
| `progress` | 결과, 진행, 사후, 실적 | **§B만** | 행사 후 |

미지정 시 User Input 추론 (`실시했다`→progress, `계획`→planning).

Options: `event-phase: planning | progress`

### 제목 (Ch.4 예시)

- `정부업무관리 시스템 장•차관 학습 행사계획 및 말씀 참고자료`
- 기획: `… 행사계획`, 진행: `… 행사 결과` 또는 `… 진행 보고`

### Pyramid Consumption

| Prior | planning | progress |
|-------|----------|----------|
| coreClaim | 개요 + A.6 건의 | 개요 + B.4 성과 |
| Level 1 | A.3 프로그램 | B.4 주요 내용 |

### 논리 패턴

- A.3 프로그램 항목: `objective-policy-pattern`
- B.2 실행: `structure-event-response-pattern` (계획→사건(변경)→대처) 선택 가능

### meeting-material과의 관계

- 행사 **전** 브리핑 성격 → `meeting-material` + `info-share`도 고려
- 공식 **행사 보고서** 양식 → 본 템플릿

### Review T1

- [ ] eventPhase에 맞는 **A 또는 B만** 출력
- [ ] planning + decision-maker → A.6 건의
- [ ] progress → B.1 계획 vs 실제 대비
- [ ] Ch.5 가이드 미완 — PDF gold와 대조 시 품질 검증 권장

### Gold reference

`reference/president/행사기획보고서_정부 업무관리시스템.pdf`, `reference/president/행사진행보고서_정부업무관리시스템 참고자료.pdf`