---
tags: [struct, template, logic-pattern]
pattern: iaej-pattern
name: "IAEJ (기반구조-행동-사건-판단)"
useWhen: "제안·정책 원인분석. 계층적 '이런 까닭에 →' 제언. policy-planning §2.2 필수 전개."
outputStyle: "4계층 제목 고정 + 계층별 '이런 까닭에 →' 제언. deliverable 임베드 시 §2.2.1~2.2.4 제목 유지."
scope: standalone | section-embed
---

# {문서 제목}

> **패턴**: 기반구조(I) → 행동(A) → 사건(E) → 판단(J)

## Executive Summary

{4계층 분석을 통해 도출된 핵심 제언 1~2문장}

## 1. 기반구조 (Infrastructure)

{오랫동안 변하지 않는 구조·제도·인프라·환경적 기반을 사실 중심으로 서술}

- 핵심 요소 1
- 핵심 요소 2
- ...

## 2. 행동 (Activity)

{기반구조 위에서 일상적으로 또는 반복적으로 일어나는 활동·행위}

- 주요 행동 패턴
- ...

## 3. 사건 (Event)

{행동 과정에서 특정 기간·지역·조건에서 발생한 문제·이슈·돌발 상황}

- 주요 사건
- 인과적 맥락

## 4. 판단 (Judge) 및 제안

{각 계층에 대한 평가와, 그에 기반한 실행 가능한 제안}

### 기반구조 관련 제안
이런 까닭에 → {기반구조 수준의 개선 제언}

### 행동 관련 제안
이런 까닭에 → {행동/운영 수준의 제언}

### 사건 관련 제안
이런 까닭에 → {사건 대응/예방 수준의 제언}

## 결론

{전체 흐름 요약 + 가장 우선순위 높은 행동 제언}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**개념**
"제안을 염두에 두고 판단하고 정리한다." (미래 지향)

**용어 정의**
- **기반구조 (I)**: 오랫동안 변하지 않는 구조나 장치 (예: 도로망, 법제도, 플랫폼)
- **행동 (A)**: 기반구조에서 일어나는 활동 (예: 통행, 이용, 운영)
- **사건 (E)**: 행동 과정 중 발생한 문제 (예: 사고, 정체, 실패)
- **판단 (J)**: 위 계층들을 평가하고 제안을 도출

**선택 기준**
- '무엇을 바꿔야 하는가'에 대한 계층적 제안을 만들 때 강력
- '이런 까닭에 → ' 형태로 논리를 연결하기 좋음

**When embedded inside a deliverable (embed mode)**

**Core logic block to use**:
- 1. 기반구조 (Infrastructure)
- 2. 행동 (Activity)
- 3. 사건 (Event)
- 4. 판단 (Judge) 및 제언 (with '이런 까닭에 →' 최소 2개)

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 결론 (상위 deliverable의 건의/결론으로 대체)

**Rules**:
- deliverable 앵커 섹션의 하위 단계 제목은 pattern의 4단계를 따르되, deliverable 컨테이너 번호로 재번호 매김 가능 (예: 2.2.1 기반구조).
- 제목 변경 금지 (L4 위반).

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

**기존 deliverable 임베드 규칙 (참고)**
- `deliverable-policy-planning.md` §2.2 에서 IAEJ 4단계 전개
- `deliverable-policy-reference.md` §2.2 (lite — 판단·제언 생략 가능)

**권장 시각 자료**
IAEJ 4단계 블록 다이어그램 + 각 계층별 '이런 까닭에' 제언 목록

(아래는 참고용 기존 예시 — 실제 출력 시 새로운 내용으로 채움)

~~~mermaid
block-beta
columns 3

lbl_I["I"]:1
title_I["기반 구조"]:1
desc_I["오랫동안 변하지 않는 구조나 장치"]:1

lbl_A["A"]:1
title_A["행동"]:1
desc_A["기반 구조에서 일어나는 활동"]:1

lbl_E["E"]:1
title_E["사건"]:1
desc_E["행동 과정 중 발생한 일"]:1

lbl_J["J"]:1
title_J["판단"]:1
desc_J["각 계층 평가 및 제안"]:1

style title_I fill:#4a90e2,stroke:none,color:#fff,font-weight:bold
style title_A fill:#4a90e2,stroke:none,color:#fff,font-weight:bold
style title_E fill:#4a90e2,stroke:none,color:#fff,font-weight:bold
style title_J fill:#4a90e2,stroke:none,color:#fff,font-weight:bold
~~~
