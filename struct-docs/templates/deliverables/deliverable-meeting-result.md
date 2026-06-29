---
tags: [struct, template, deliverable, meeting]
pattern: deliverable-meeting-result
deliverableType: meeting-result
name: "회의결과보고서"
useWhen: "Brief.deliverableType이 meeting-result일 때. 회의 후 논의·결정 사항 공유. 회의록·메모 입력에 적합."
outputStyle: "제3자적·객관적. 안건별 정리. 안건당 mini-SCQA 또는 SER 선택."
defaultLogicPattern: none
logicSections:
  - "§2.{n} scqa-pattern (안건별 — 권장)"
  - "§2.{n} incident-causal ser (쟁점·대응 안건 — 선택)"
inputHint: "회의록, 메모 — prior thinking보다 회의 원문 우선"
reference: "reference/president/대통령 보고서.md Ch.4 회의결과"
---

# {회의 결과 보고 제목}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| 회의 일시 | {YYYY-MM-DD HH:mm} |
| 회의 장소 | {장소·화상} |

## 요약

> {coreClaim — 회의 전체 결론·핵심 결정}

- **분위기·논쟁 요지**: {1~2문장}
- **주요 결정**: {bullet 2~3개}

---

## 1. 회의 개요

### 1.1 목적·개최 배경

- **목적**: { }
- **배경**: { }

### 1.2 일시·장소·안건

| 항목 | 내용 |
|------|------|
| 일시 | { } |
| 장소 | { } |
| 안건 | {안건 1; 안건 2} |

### 1.3 참석자

| 구분 | 성명·소속 | 비고 |
|------|----------|------|
| 참석 | { } | { } |

---

## 2. 회의 결과

<!-- 안건별 반복 -->

### 2.{n} {안건 명} (logic: scqa-pattern — **권장 전개**)

> 안건별 mini-SCQA. scqa-pattern의 Situation·Complication·Question·Answer를 적용.
> 패턴: `patterns/general/scqa-pattern.md`

#### 상황 (Situation)

- {논의 배경·사실}

#### 문제 (Complication)

- {쟁점·대립·결정 필요성}

#### 핵심 질문 (Question)

> {이 안건에서 답해야 할 1문장}

#### 답변·결정 (Answer)

- **결정 사항**: {합의·결정·보류}
- **근거 요지**: {bullet}

| 항목 | 결정 내용 |
|------|----------|
| { } | { } |

#### 참석자 주요 의견

| 발언자(소속) | 의견 요지 | 비고 |
|-------------|----------|------|
| { } | { } | { } |

<!-- 쟁점·대응 안건: 아래 SER 블록 추가 가능. SCQA와 병행 -->

#### 구조-사건-대처 (logic: incident-causal-pattern, mode=ser — 선택)

- **구조**: { }
- **사건**: { }
- **대처**: {회의에서 결정·합의한 대응}

---

## 3. 종합 정리 (해당 시)

- **후속 조치**: {담당·기한}
- **미결 사항**: { }
- **전체 분위기**: { }

---

## 참고·첨부

- {회의록·녹취·회의자료 링크}

---

## Appendix: 품질 점검

- 제3자적·객관적 서술: {pass / fail}
- logicSectionsFilled: {안건별 SCQA 4요소 — pass/fail}
- 사용 템플릿: deliverable-meeting-result.md
- prior thinking 기반: {보통 no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### 입력 우선순위

1. 회의록·메모
2. meeting-material 산출물
3. prior thinking (배경만)

### Review T1

- [ ] 안건마다 **결정 사항** 명시 (없으면 "보류")
- [ ] SCQA (logic: scqa-pattern): Situation·Complication·Question·Answer **4요소** (안건당)
- [ ] 작성자 주관 배제

### Gold reference

`reference/president/회의결과보고서1_과학기술자문회의 보고결과.pdf`