---
tags: [struct, template, logic-pattern]
pattern: fabe-pattern
name: "FABE (특징-우위성-이익-증거)"
useWhen: "경쟁사·대안·현상 비교를 통한 우위 설명. 제품·정책·전략 옵션 선택 시 '왜 이것이 더 나은가'를 설득. policy-planning 대안 분석, meeting-material 옵션 평가, 제안서에 강력."
outputStyle: "4계층 고정 구조 + block-beta 다이어그램. 'Feature → Advantage → Benefit → Evidence' 순으로 계층화. 대안 간 비교 표와 함께 사용."
scope: standalone | section-embed
---

# {문서 제목}

> **패턴**: 특징(Feature) → 우위성(Advantage) → 이익(Benefit) → 증거(Evidence)

## Executive Summary

{제안/선택 대상의 핵심 우위 1~2문장. 경쟁/현상 대비 어떤 점에서 우월한지 요약}

## 1. 특징 (Feature)

{틀·스펙·구조·기능상의 차이점. 객관적 사실 중심}

- {주요 특징 1}
- {주요 특징 2}
- ...

## 2. 우위성 (Advantage)

{특징이 가져오는 성능·효율·품질상의 우위. "이것이 더 낫다" 수준}

- {특징 대비 우위}
- {정량적·정성적 차이}

## 3. 이익 (Benefit)

{우위성이 사용자/조직/목표에 가져다주는 실질적 이익·가치. "그래서 뭐가 좋은가"}

- {사용자/조직이 얻는 구체적 이익}
- {목표 달성에 기여하는 방식}

## 4. 증거 (Evidence)

{우위와 이익을 뒷받침하는 정량 데이터, 사례, 벤치마크}

- {데이터·통계·비교 수치}
- {성공 사례 또는 검증 결과}

## 결론 및 제언

{전체 흐름 요약 + 선택·채택 권고}

**이런 까닭에 →** {특징 수준의 제언 또는 차별화 유지 방안}

**이런 까닭에 →** {이익 실현을 위한 실행 제언}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**개념**
경쟁 상대 또는 현상(As-Is)과 비교하여 "이것이 왜 더 나은 선택인가"를 4단계로 증명한다. (미래 지향적 설득)

**용어 정의**
- **특징 (Feature)**: 구조·스펙·방식의 차이 (틀의 차이)
- **우위성 (Advantage)**: 특징이 만드는 성능·효율의 차이
- **이익 (Benefit)**: 우위가 사용자에게 주는 실질 가치·효과 (용도의 차이)
- **증거 (Evidence)**: 정량적 데이터, 사례, 외부 검증으로 신뢰 확보

**선택 기준**
- 옵션 비교·경쟁 우위 설명이 핵심일 때 최적
- "이것 vs 저것" 또는 "현행 vs 신규" 구조에서 강력
- 단순 사실 나열이 아닌 이익까지 연결해야 할 때 사용

**When embedded inside a deliverable (embed mode)**

**Core logic block to use**:
- 1. 특징 (Feature)
- 2. 우위성 (Advantage)
- 3. 이익 (Benefit)
- 4. 증거 (Evidence)

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 결론 (deliverable 상위 섹션 사용)

**Rules**:
- Feature → Advantage → Benefit → Evidence 순서 엄수.
- 대안 비교 표와 함께 사용 시 강력.

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

**기존 deliverable 임베드 예시**
- `deliverable-policy-planning.md` §3 정책수단과 대안 내부 비교
- `deliverable-meeting-material.md` 옵션 평가 파트
- standalone: 제품 비교, 전략 제안, RFP 응답 우위 설명

**권장 시각 자료**
FABE 4열 block-beta + "이런 까닭에" 제언. 대안 간 비교표 병행.

(참고 예시 — 실제 출력은 새로운 내용으로 채움)

~~~mermaid
block-beta
  columns 4
  
  f1["특징<br>Feature"]
  t1["터보 엔진 탑재"]:3
  
  a1["우위성<br>Advantage"]
  t2["터보 차량이 아닌 차량보다, 고출력을 낼 수 있음"]:3
  
  b1["이익<br>Benefit"]
  t3["중량물을 싣고도 비탈길을 편히 오를 수 있음"]:3
  
  e1["증거<br>Evidence"]
  t4["경차 개조 캠핑카의 4할"]:3
~~~
