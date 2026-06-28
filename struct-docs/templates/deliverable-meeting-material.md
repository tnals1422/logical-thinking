---
tags: [struct, template, deliverable, meeting]
pattern: deliverable-meeting-material
deliverableType: meeting-material
name: "회의자료보고서"
useWhen: "Brief.deliverableType이 meeting-material일 때. 정보공유·의견수렴·의사결정 회의용 자료. 시간 제약 내 명료·평이한 표현."
outputStyle: "개조식 + 표. 본문(안건 중심)과 참고자료 분리. meetingPurpose에 따라 §3 분기."
meetingPurposes: info-share | opinion-gathering | decision
defaultLogicPattern: objective-policy-pattern
reference: "reference/president/대통령 보고서.md Ch.4 회의자료"
---

# {회의 자료 제목 — 안건·목적이 드러나게}

| 항목 | 내용 |
|------|------|
| 작성일 | {YYYY-MM-DD} |
| 작성자 | {성명·소속} |
| meetingPurpose | {info-share \| opinion-gathering \| decision} |
| 예상 회의 시간 | {분} |
| 대상 참석자 | {역할·배경 요약} |

## 회의 한눈에 보기

> {coreClaim — 이 회의에서 얻을 핵심 결론·공유·의견 한 줄}

- **목적**: {회의 목적}
- **안건**: {주요 안건 1~2줄}
- **기대 효과**: {회의 후 달성 상태}

---

## 1. 회의 경위

- {왜 이 회의가 소집되었는가}
- {관련 추진·협의 이력 요약}

| 시기 | 경과 | 비고 |
|------|------|------|
| { } | { } | { } |

## 2. 회의 목적

### 2.1 회의 목적

- {이 회의로 달성하려는 것 — 1~2문장}

### 2.2 개최 배경

- {배경·맥락 — 참석자가 공감할 사실}

### 2.3 기대 효과

- {회의를 통해 얻고자 하는 효과 — 가이드 T1: 필수 명시}
- {성공 기준 — 합의·결정·공유 완료 조건}

---

## 3. 회의 안건 설명

<!-- meetingPurpose=info-share: 아래 §3.A만 작성. §3.B·§3.C 생략 -->

### 3.A 정보공유 안건 (meetingPurpose = info-share)

#### 3.A.1 전달 내용

- {별도 설명 없이 이해 가능하도록 **상세** 기술 — 개조식}
- {핵심 데이터·현황·변경 사항}

| 항목 | 내용 | 비고 |
|------|------|------|
| { } | { } | { } |

#### 3.A.2 향후 활용 방안

- {공유 정보의 후속 활용}

#### 3.A.3 보안·유의 사항 (해당 시)

- {보안유지방안·배포 범위}

---

<!-- meetingPurpose=opinion-gathering: 아래 §3.B만 작성 -->

### 3.B 의견수렴 안건 (meetingPurpose = opinion-gathering)

#### 3.B.1 논의 목록

| # | 논의 주제 | 질문·수렴 포인트 |
|---|----------|-----------------|
| 1 | { } | { } |
| 2 | { } | { } |

#### 3.B.2 논의 배경·현황 (의사결정 전 의견수렴 시)

- {현재까지 논의 현황 — 가치중립}
- {쟁점사항·쟁점별 논거 — 표 권장}

| 쟁점 | 입장 A | 입장 B | 중립 요약 |
|------|--------|--------|----------|
| { } | { } | { } | { } |

> **참고**: 창의적 아이디어 수렴 시 — 지침·제약 최소화. 목적·배경만 명확히.

---

<!-- meetingPurpose=decision: 아래 §3.C만 작성. coordination 템플릿 §2~3 요소 활용 가능 -->

### 3.C 의사결정 안건 (meetingPurpose = decision)

#### 3.C.1 논의 현황

- {지금까지의 협의·조율 경과}

#### 3.C.2 결정 대상 쟁점

| 쟁점 | 결정 필요 사항 | 관련 논거 |
|------|---------------|----------|
| { } | { } | { } |

#### 3.C.3 이해관계자 입장 (해당 시)

| 주체 | 입장 | 핵심 논거 |
|------|------|----------|
| { } | { } | { } |

#### 3.C.4 결정 후 추진 계획·예상 문제

(1) **목적**: {결정 이후 달성할 상태}

    **방침**: {실행 방식 — objective-policy 쌍}

(2) **목적**: { }

    **방침**: { }

- **예상 문제점**: {결정 후 리스크}

---

## 4. 참고자료

<!-- 본문과 분리. 시간 제약 시 안건 핵심만 §3에, 나머지는 여기 -->

- [{참고 1 제목}]({링크 또는 첨부 표기})
- [{참고 2}]

---

## Appendix: 품질 점검

- meetingPurpose: {info-share \| opinion-gathering \| decision}
- 회의 목적·기대 효과 명시: {pass / fail}
- 본문·참고자료 분리: {pass / fail}
- 사용 템플릿: deliverable-meeting-material.md
- prior thinking 기반: {yes / no}
- express 연계 권장: {slide 5~12장 — Phase 5}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### meetingPurpose 분기

| meetingPurpose | Brief 힌트 | §3 작성 | 톤 |
|----------------|-----------|---------|-----|
| `info-share` | 공유, 전달, 브리핑 | **§3.A만** | 상세·자급자족 |
| `opinion-gathering` | 의견, 수렴, 논의 | **§3.B만** | 가치중립·쟁점 정리 |
| `decision` | 결정, 승인, 의결 | **§3.C만** | 쟁점·입장·추진계획 |

미지정 시 User Input에서 추론. 모호하면 `opinion-gathering`.

### Pyramid Consumption

| Prior | 매핑 |
|-------|------|
| coreClaim | 회의 한눈에 보기 + §2.1 목적 |
| Level 1 | §3 안건 소제목·논의 목록 |
| GPS 표 | §3.B 쟁점표, §3.C 결정 쟁점표 |

### 논리 패턴 (logicPattern)

- §3.C 추진·방침: `objective-policy-pattern` (목적-방침 쌍)
- `decision` + 쟁점 복잡 시: `deliverable-coordination.md` §2~3 참고하여 보강

### 작성 팁 (가이드 Ch.4)

- 참석자 다양한 배경 → **평이한 용어**, 균형 시각
- 안건 중심 자료 = §3 / 부수 = §4 참고자료
- 회의 시간 대비 분량 조절 (Autonomous: 30분 회의 ≈ §3 1~2페이지 분량 목표)

### Review T1

- [ ] §2.3 기대 효과 명시
- [ ] meetingPurpose에 맞는 §3 **하나만** 작성 (다른 §3 섹션 미출력)
- [ ] `decision` 시 §3.C.2 쟁점·결정 사항 존재
- [ ] 참고자료 §4 분리

### Gold reference

`reference/president/회의자료보고서1_중소기업 정책정보 전달체계 혁신방안 추진.pdf`, `회의자료보고서2_상반기 중소기업 현장체험단 실적 및 계획.pdf`