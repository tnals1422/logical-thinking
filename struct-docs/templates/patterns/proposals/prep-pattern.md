---
tags: [struct, template, logic-pattern]
pattern: prep-pattern
name: "PREP (요점-이유-증거-요점)"
useWhen: "단일 핵심 주장·제언·권고를 설득력 있게 전달할 때. SCQA Answer 내부, IAEJ 판단 섹션, Executive Summary의 포인트, Q&A 답변, 짧은 건의에 최적. general fallback micro-structure."
outputStyle: "Point → Reason → Evidence/Example → Point (bookend) 구조. 개조식 + 1문장 Point 강조. 시각적으로는 간단 표 또는 4단 블록."
scope: standalone | section-embed
---

# {문서 제목}

> **패턴**: 요점(Point) → 이유(Reason) → 증거/사례(Evidence/Example) → 요점(Point)

## Executive Summary

{전체 문서의 가장 중요한 단일 주장 1문장. PREP의 첫 Point와 일치}

## 핵심 주장

> {가장 말하고 싶은 것 — 행동 제안이 들어간 명확한 한 문장}

### 이유 (Reason)

{왜 이 주장이 필요한가? 행동이 가져올 긍정적 효과나 문제 해결 설명. 정성 중심}

- {주요 이유}

### 증거 또는 사례 (Evidence / Example)

{이유를 뒷받침하는 데이터, 통계, 외부 사례, 벤치마크}

- {정량적 증거 또는 구체적 사례}

## 결론 (반복 Point)

> {첫 Point를 다시 강조하되, 판단 촉구·행동 유도 형태로 마무리}

**추천 행동**

- [ ] {구체적 다음 단계 1}
- [ ] {구체적 다음 단계 2}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**개념**
고전적 설득 구조. "주장 → 왜 → 근거 → 다시 주장(호소)"으로 청중이 쉽게 따라오게 한다. Minto의 "Answer first + grouped reasons"와 잘 맞음.

**구성요소 상세**
- **Point (요점)**: 가장 말하고 싶은 것. 대개 "무엇을 하자" 형태의 행동 제안. 문서 처음과 끝에 반복 (bookend).
- **Reason (이유)**: 행동이 필요한 이유. "이걸 하면 ~ 때문이다". 정성적 설명 위주.
- **Evidence / Example**: Reason의 신뢰를 높이는 근거. 정량 데이터(Evidence) 또는 구체 사례(Example). 둘 다 사용 가능.
- 마지막 Point: 판단 촉구 장치. "따라서 ~하시겠습니까?" 또는 "시작합시다" 형태.

**비고**
- Reason은 주로 정성, Evidence는 정량, Example은 개별 사례.
- 짧은 문서나 단일 포인트 전달 시 SCQA 전체 대신 PREP만으로 충분할 수 있음.
- SCQA의 Answer 섹션 내부 4.1~4.2 를 PREP로 전개하는 것도 효과적.

**When embedded inside a deliverable (embed mode)**

**Core logic block to use**:
- Point (요점)
- Reason (이유)
- Evidence / Example (증거/사례)
- Point (반복 — bookend)

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 전체 결론 (상위 구조 사용)

**Rules**:
- Point를 처음과 끝에 반복 (bookend)하여 강조.
- SCQA Answer 내부나 IAEJ 판단 섹션에서 micro-structure로 자주 사용.

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

**기존 deliverable 임베드 예시**
- `deliverable-policy-planning.md` §3 정책수단과 대안의 각 대안 설명
- `deliverable-meeting-result.md` §2 안건의 결정 포인트
- `patterns/general/scqa-pattern.md` Answer 내부 하위 구조
- standalone: 핵심 건의 1~3개 나열 시 각 항목에 적용

**권장 시각 자료**
간단 4단 block-beta 또는 테이블 (Point | Reason | Evidence | Action)

(참고 예시 — 실제 출력은 새로운 내용으로 채움)

~~~mermaid
block-beta
  columns 4
  
  p1["요점<br>Point"]
  t1["인력 부족을 해소하기 위해, 원격 근무 제도를 확충해야 합니다."]:3
  
  r1["이유<br>Reason"]
  t2["유능하지만 풀타임 출근이 어려운 인재를, 채용하기 유리해지기 때문입니다."]:3
  
  e1["증거<br>Evidence"]
  t3["해당되는 인재가 수도권만 해도 10만 명이라는 데이터도 있습니다."]:3
  
  ex1["예시<br>Example"]
  t4["실제로 당사의 동일 업종에서 성공한 사례도 있습니다."]:3
  
  p2["요점<br>Point"]
  t5["그러므로 원격 근무 제도의 도입 검토를 시작해도 괜찮겠습니까?"]:3
~~~
