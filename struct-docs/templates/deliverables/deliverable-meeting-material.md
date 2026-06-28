---
tags: [struct, template, deliverable, meeting]
pattern: deliverable-meeting-material
deliverableType: meeting-material
name: "회의자료보고서"
useWhen: "Brief.deliverableType이 meeting-material일 때. 정보공유·의견수렴·의사결정 회의용 자료."
outputStyle: "개조식 + 표. meetingPurpose 분기. decision·추진 항목은 목적-방침 쌍 필수."
meetingPurposes: [info-share, opinion-gathering, decision]
defaultLogicPattern: objective-policy-pattern
logicSections:
  - "§3.C.4 objective-policy-pattern (decision 시 필수)"
  - "§3.C incident-causal ser (decision+쟁점 복잡 시 권장)"
reference: "reference/president/대통령 보고서.md Ch.4 회의자료"
---

# {회의 자료 제목 — 안건·목적이 드러나게}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| meetingPurpose | {info-share | opinion-gathering | decision} |
| 예상 회의 시간 | {분} |
| 대상 참석자 | {역할·배경 요약} |

## 회의 한눈에 보기

> {coreClaim — 회의 핵심 결론·공유·의견 한 줄}

- **목적**: {회의 목적}
- **안건**: {주요 안건}
- **기대 효과**: {회의 후 달성 상태}

---

## 1. 회의 경위

- {소집 이유}
- {관련 추진·협의 이력}

| 시기 | 경과 | 비고 |
|------|------|------|
| { } | { } | { } |

## 2. 회의 목적

### 2.1 회의 목적

- {달성하려는 것}

### 2.2 개최 배경

- {배경·맥락}

### 2.3 기대 효과

- {효과}
- **성공 기준**: {합의·결정·공유 완료 조건}

---

## 3. 회의 안건 설명

<!-- meetingPurpose=info-share: §3.A만 -->

### 3.A 정보공유 (meetingPurpose = info-share)

#### 3.A.1 전달 내용

- {상세·자급자족 가능한 개조식}
- {핵심 데이터·현황}

| 항목 | 내용 | 비고 |
|------|------|------|
| { } | { } | { } |

#### 3.A.2 향후 활용

- {후속 활용}

---

<!-- meetingPurpose=opinion-gathering: §3.B만 -->

### 3.B 의견수렴 (meetingPurpose = opinion-gathering)

#### 3.B.1 논의 목록

| # | 논의 주제 | 수렴 포인트 |
|---|----------|------------|
| 1 | { } | { } |

#### 3.B.2 쟁점·논거 (가치중립)

| 쟁점 | 입장 A | 입장 B | 중립 요약 |
|------|--------|--------|----------|
| { } | { } | { } | { } |

---

<!-- meetingPurpose=decision: §3.C만 -->

### 3.C 의사결정 (meetingPurpose = decision)

#### 3.C.1 논의 현황

- {협의·조율 경과}

#### 3.C.2 결정 대상 쟁점

| 쟁점 | 결정 필요 사항 | 관련 논거 |
|------|---------------|----------|
| { } | { } | { } |

#### 3.C.3 이해관계자 입장 (해당 시)

| 주체 | 입장 | 핵심 논거 |
|------|------|----------|
| { } | { } | { } |

#### 3.C.4 결정 후 추진 (logic: objective-policy-pattern — **필수 전개**)

(1) **목적**: {결정 이후 달성 상태}

    **방침**: {실행 방식}

(2) **목적**: { }

    **방침**: { }

- **예상 문제점**: {리스크}

#### 3.C.5 쟁점 인과 (logic: incident-causal-pattern, mode=ser — 쟁점 복잡 시 **권장**)

- **구조**: {현행 배경}
- **사건**: {쟁점 발생 경위}
- **대처**: {회의에서 제안하는 조정·결정안}

---

## 4. 참고자료

- [{참고 1}]({링크})

---

## Appendix: 품질 점검

- meetingPurpose: { }
- logicSectionsFilled: {§3.C.4 목적-방침 — pass/fail/n/a}
- 사용 템플릿: deliverable-meeting-material.md
- logicPattern: objective-policy-pattern
- prior thinking 기반: {yes / no}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### meetingPurpose 분기

| meetingPurpose | §3 작성 |
|----------------|---------|
| `info-share` | **§3.A만** |
| `opinion-gathering` | **§3.B만** |
| `decision` | **§3.C만** + §3.C.4 목적-방침 **필수** |

### Review T1

- [ ] meetingPurpose에 맞는 §3 **하나만** 출력
- [ ] `decision` 시 §3.C.4 목적-방침 **최소 1쌍**
- [ ] §2.3 기대 효과 명시

### Gold reference

`reference/president/회의자료보고서1_중소기업 정책정보 전달체계 혁신방안 추진.pdf`