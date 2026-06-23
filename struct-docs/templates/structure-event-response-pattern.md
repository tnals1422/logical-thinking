---
tags: [struct, template, structure-event-response]
pattern: structure-event-response
name: "Structure-Event-Response (구조-사건-대처)"
useWhen: "과거에 발생한 사건과 그에 대한 대응을 명확히 구분하여 기록·분석할 때. '구조는 변하지 않고, 사건은 일어났으며, 대처는 의도적으로 했다'는 3요소 분석이 필요할 때."
outputStyle: "구조 / 사건 / 대처 3단 구조 + 인과 관계 시각화 (Mermaid block-beta 추천)"
---

# {문서 제목}

> **패턴**: Structure → Event → Response (구조 → 사건 → 대처)

## Executive Summary

{사건이 일어난 구조적 배경과, 대처의 핵심 결과 한 줄 요약}

## 1. 구조 (Structure)

{변하지 않는 배경 정보, 시스템, 제도, 물리적·조직적 조건}

- 핵심 구조 요소
  - ...
- 이 구조가 왜 중요한가

## 2. 사건 (Event)

{사람 의도와 무관하게 발생한 일. 인과관계가 구조로부터 비롯됨을 명시}

**발생 경위 (시간/인과 순서로)**

1. {첫 번째 사건/변화}
2. {두 번째 사건}
3. ...

**사건과 구조의 관계**
- 구조 때문에 일어날 수밖에 없었던 점
- 우연적 요소 (구조와 구분)

## 3. 대처 (Response)

{사람이 사태 수습을 위해 '생각하여 의도적으로' 한 행동}

- 즉시 대처
  - ...
- 후속 대책 (대처와 구분: 이미 일어난 일에 대한 대응 vs 예방)
  - ...

## 결과 및 교훈

- 사건으로 인한 직접 영향
- 대처의 효과 평가
- 향후 유사 사건을 위한 개선점

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**핵심 개념**
"일어난 일에 어떻게 대처했는지를 기록한다." (과거 중심)

**용어**
- **구조**: 변하지 않는 정보 (배경 시스템)
- **사건**: 의도와 무관하게 저절로 일어난 일 (인과는 구조에 기인)
- **대처**: 사람이 사태를 수습하고자 생각하여 의도적으로 한 일

**대처 vs 대책 구분**
- 대처: 이미 발생한 문제를 해결
- 대책: 문제가 일어나기 전에 미리 준비

**권장 출력 형식**
- 구조/사건/대처 3단 섹션
- 시간순 또는 인과순 블록 다이어그램 (block-beta) 적극 사용

(아래는 참고 예시 — 실제 작성 시 새 내용으로 대체)

### 참고용 원본 입력 예시
클라우드 데이터센터의 냉각 시스템이 이상 폭염으로 효율 저하 → 서버 온도 상승 → 일부 서비스 중지 및 시스템 증설.

### 참고 Mermaid (block-beta 구조-사건-대처) 예시
~~~mermaid
block-beta
columns 3
h1["이런 구조로<br>움직입니다"]:1
h2["그 구조에서<br>이런 일이 일어났습니다"]:1
h3["이에 이렇게<br>대응했습니다"]:1
t1["구조"]:1
t2["사건"]:1
t3["대처"]:1
struct1["클라우드 서버"]:1
event3["③ 온도 상승"]:1
action4["④ 서비스 중지"]:1
struct2["냉각 시스템"]:1
event2["② 효율 저하"]:1
action5["⑤ 시스템 증설"]:1
struct3["외부"]:1
event1["① 이상 폭염"]:1
style t1 fill:none,stroke:none,font-weight:bold
style t2 fill:none,stroke:none,font-weight:bold
style t3 fill:none,stroke:none,font-weight:bold
style struct1 fill:#4a90e2,stroke:none,color:#fff
style struct2 fill:#4a90e2,stroke:none,color:#fff
style struct3 fill:#4a90e2,stroke:none,color:#fff
~~~

