---
tags: [struct, template, deliverable, coordination]
pattern: deliverable-coordination
deliverableType: coordination
name: "조정과제보고서"
useWhen: "Brief.deliverableType이 coordination일 때. 정책·사업 추진 중 갈등·이해관계 조정, 쟁점 해결, 조정방향 건의 보고서."
outputStyle: "서술형 개조식 + 쟁점·이해당사자 대비표. 제목 20자 이내. decision-maker 대상 시 건의·선정 이유 필수."
defaultLogicPattern: incident-causal-pattern
logicPatternMode: ser
logicSections:
  - "§3 incident-causal-pattern mode=ser (필수)"
  - "§5.2 objective-policy-pattern (권장)"
reference: "reference/president/대통령 보고서.md Ch.2 조정과제"
---

# {제목 — 쟁점·조정방향이 드러나게, 20자 이내}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| 보고 목적 | {Brief.reportPurpose 한 줄} |
| 핵심 쟁점 | {한 줄 요약} |

## 개요

> {coreClaim — 조정 방향·권고안 1문장}

- **쟁점**: {무엇이 쟁점인가}
- **권고**: {선정 대안·조정 방향 한 줄}
- {수요자 조치 요약 — requestedActionRequired 시}

---

## 1. 보고 개요

### 1.1 보고 목적과 쟁점 발생 배경

- **조정 필요성**: {왜 지금 조정 보고가 필요한가}
- **핵심 쟁점**: {무엇이 의사결정·조정 대상인가}
- **상위 과제·정책과의 관계**: {연계 맥락}

### 1.2 추진 경과

- {과제 추진 타임라인 — 쟁점 발생까지}
- {기존 협의·조정 시도 요약}

| 시기 | 주요 경과 | 결과 |
|------|----------|------|
| {날짜} | {협의·검토 내용} | {합의/이견 지속 등} |

## 2. 현황과 쟁점

### 2.1 현황과 실태

- {갈등·이견이 발생한 상황의 객관적 사실}
- {관련 수치·조건·제약}

### 2.2 쟁점 사항

| 쟁점 | 쟁점 내용 | 의사결정 필요 사항 |
|------|----------|-------------------|
| {쟁점 1} | {무엇이 대립하는가} | {결정권자가 정할 것} |
| {쟁점 2} | | |

### 2.3 이해당사자 의견 대비

| 이해당사자 | 핵심 입장 | 근거·요구 | 우려 |
|-----------|----------|----------|------|
| {A} | {입장 요약} | { } | { } |
| {B} | { } | { } | { } |

### 2.4 기존 대응·조정 실적

- {지금까지 시도한 조정·협의와 결과}
- {실패·한계 요인}

## 3. 조정 대안 분석 (logic: incident-causal-pattern, mode=ser — **필수 전개**)

> incident-causal-pattern (mode=ser)의 Structure → Event → Response + 대처 효과를 전개.
> 패턴 단계는 `patterns/reports/incident-causal-pattern.md` 참조. (EMBEDDING-GUIDE.md)

### 3.1 구조 (Structure) — 변하지 않는 배경

- {현행 제도·계약·조직·시스템 등 쟁점의 배경 구조}
- **구조가 쟁점을 만든 이유**: { }

### 3.2 사건 (Event) — 쟁점 발생 경위

**발생 경위**

1. {쟁점 촉발 사건}
2. {갈등 확대·의사결정 필요까지의 경과}

- **구조적 필연**: { }
- **우연·외부 요인**: { }

### 3.3 대처 (Response) — 조정 대안

| 대안 | 내용 요약 | 장점 | 단점 | 쟁점 해소도 |
|------|----------|------|------|:-----------:|
| {대안 A} | { } | { } | { } | {高/中/低} |
| {대안 B} | { } | { } | { } | |

**권장 대안**: {대안명}

- **즉시 대처**: {결정 후 즉시 실행}
- **후속 대처**: {조정 이후 후속 조치}

### 3.4 결과·교훈 (선정 대안 기준)

- **쟁점별 해소**: {각 쟁점 정리 방식}
- **잔여 리스크**: {불이익 집단·후속 갈등}
- **대처 효과 전망**: { }

## 4. 건의와 제안

> {Brief.requestedAction — 수요자에게 요청하는 조정·결정 사항}

### 4.1 건의 사항

- [ ] {건의 1}
- [ ] {건의 2}

### 4.2 권장 대안 선정 이유

- {왜 이 대안이 최선인가}
- {기각 대안의 결정적 단점}

## 5. 향후 계획 (logic: objective-policy-pattern — 권장)

(1) **목적**: {조정 결정 후 달성 상태}

    **방침**: {실행·소통 방식}

(2) **목적**: { }

    **방침**: { }

### 5.1 추진 일정

| 단계 | 내용 | 기한 | 담당 |
|------|------|------|------|
| 1 | {조정 결과 통보} | { } | { } |
| 2 | { } | { } | { } |

---

## 참고·첨부

- {쟁점별 상세 자료·회의록 등}

---

## Appendix: 품질 점검

- 검증 결과: {passed / partial / failed}
- logicSectionsFilled: {§3 SER 3단계 — pass/fail}
- 사용 템플릿: deliverable-coordination.md
- logicPattern: incident-causal-pattern (mode=ser)
- prior thinking 기반: {yes / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### 정책기획과의 차이

| 항목 | 정책기획 | 조정과제 |
|------|----------|----------|
| 원인 논리 | IAEJ §2.2 | SER §3 (구조→사건→대처) |
| §2 초점 | 현황·원인 | **쟁점·이해당사자 대비** |
| 건의 | §5 | §4 + **선정 이유 필수** |

### Review T1

- [ ] §3 (logic: incident-causal-pattern, mode=ser) 3단계 모두 채움 (패턴 단계 기준)
- [ ] §2.3 이해당사자 **대비** 표
- [ ] §4.2 선정 이유 명시

### Gold reference

`reference/president/조정과제보고서_OO사업점검결과 및 조정방향 보고.pdf`