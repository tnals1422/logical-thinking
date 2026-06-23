---
tags: [struct, template, state-trigger-accident-damage]
pattern: state-trigger-accident-damage
name: "State-Trigger-Accident-Damage (상태-트리거-사고-손해)"
useWhen: "복잡한 인과관계를 분석하거나, '평소 상태'에서 '작은 트리거'가 '사고'를 일으켜 '손해'로 이어지는 흐름을 명확히 할 때. 대책 수립(예방)과 함께 설명해야 할 때 특히 강력."
outputStyle: "4단계 인과 흐름 + (선택) 대책 매핑. Mermaid flowchart 또는 block diagram 강력 추천."
---

# {문서 제목}

> **패턴**: 상태(State) → 트리거(Trigger) → 사고(Accident) → 손해(Damage)

## Executive Summary

{문제의 본질: 어떤 '지속 상태'가 있었고, 어떤 '작은 트리거'가 사고를 유발했으며, 결과적으로 어떤 손해가 발생했는지 1~2문장 요약. 대책 방향도 간단히 언급.}

## 1. 상태 (State) — 지속적인 배경 조건

{사고가 일어나기 전부터 계속 존재하던 구조적·습관적·환경적 상태}

- 상태 A: ...
- 상태 B: ...
- ...

**왜 이 상태가 위험한가**
...

## 2. 트리거 (Trigger) — 촉발 사건

{작지만 결정적인 계기. 이 한 가지가 없었다면 사고가 일어나지 않았을 가능성이 높은 사건}

- 트리거 내용
- 발생 시점/조건

## 3. 사고 (Accident) — 직접적 사건

{트리거로 인해 실제로 발생한 사고·오류·장애}

...

## 4. 손해 (Damage) — 최종 결과

{사람·자산·조직·신뢰 등에 미친 피해. 정량 가능하면 수치 포함}

...

## 대책 (Countermeasures) — 선택 섹션

(사고 후 재발 방지 또는 사전 예방을 위한 대책을 상태-트리거-사고-손해 각 단계별로 매핑)

| 단계   | 대책 아이디어                  | 담당/시기   |
|--------|--------------------------------|-------------|
| 상태   | ...                            | ...         |
| 트리거 | ...                            | ...         |
| 사고   | ...                            | ...         |
| 손해   | ...                            | ...         |

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

**개념**
> 무언가가 계속되는 상태에서 특정 트리거(작은 사건)가 사고를 일으켜 손해가 발생함.

**용처**
- 복잡한 인과관계 설명
- 상황 파악이 중요한 분석
- 꼼꼼한 대책 수립이 필요한 경우

**작성 방법**
1. 정보를 작게 나눈다
2. 화살표로 인과관계 표현
3. 상태 / 트리거 / 사고 / 손해로 분류
4. (권장) 대책을 단계별로 매핑

참고 예시는 하단에 보존되어 있음. 실제 문서 작성 시 새로운 사례를 채운다.

---

## 참고 예시 (기존 사례 — 실제 작성 시 교체)
### Case 1. 복잡한 인과관계
#### input
~~~
2021년 A시 병원의 내부 정보 시스템이 랜섬웨어에 감염되어 모든 전자 의료기록 데이터가 암호화되는 까닭에 진료를 못하는 사건이 발생했습니다.이 사건의 발단은 업무 연락을 가장하여 병원에 보낸 스팸 이메일의 첨부 파일을 한 직원이 연 것으로, 이 때문에 업데이트하지 않고 사용하던 PC에서 악성 소프트웨어가 실행되었고, PC가 외부에 설치된 공격용 서버에 접속되어 공격자의 원격 조작을 허용한 탓에 데이터가 암호화된 것으로 판단합니다.
이후 조사겨로가, 외부에서 병원 내부 정보 시스템에 접속하는 데 이용한 VPN 장치의 취약점을 방치했다는 것과 VPN 장치에 접속할 수 있는 범위를 제한하지 않았던 것이 이유로 밝혀졌습니다.
~~~

#### output
~~~mermaid
block-beta

columns 4

  

%% [왼쪽 레이블 영역 및 첫 행 배치]

lbl_status["상태"]:1

node_A["A 단말기 보안이<br>취약한 상태"]:1

node_B["B VPN 장치의<br>취약점을 방치"]:1

node_C["C VPN 장치의 접속<br>범위를 제한하지<br>않음"]:1

  

%% [두 번째 행: 트리거]

lbl_trigger["트리거"]:1

node_D["D 스팸 메일을<br>열어 버림"]:1

space:2

  

%% [연결 화살표 레이어 1]

space:1

arrow_D<["&nbsp;"]>(down):1

arrow_A<["&nbsp;"]>(down):1

space:1

  

%% [세 번째 행: 사고 시작]

lbl_accident["사고"]:1

node_E["E 단말기에서 악성 소프트웨어 실행"]:1

space:2

  

%% [연결 화살표 레이어 2]

space:1

arrow_E<["&nbsp;"]>(down):1

arrow_B<["&nbsp;"]>(down):1

arrow_C<["&nbsp;"]>(down):1

  

%% [네 번째 행: F]

space:1

node_F["F 공격용 서버에 접속"]:3

  

%% [연결 화살표 레이어 3]

space:2

arrow_F<["&nbsp;"]>(down):1

space:1

  

%% [다섯 번째 행: G]

space:1

node_G["G 원격 조작을 허용"]:3

  

%% [연결 화살표 레이어 4]

space:2

arrow_G<["&nbsp;"]>(down):1

space:1

  

%% [여섯 번째 행: H]

space:1

node_H["H 데이터가 암호화됨"]:3

  

%% [연결 화살표 레이어 5]

space:2

arrow_H<["&nbsp;"]>(down):1

space:1

  

%% [일곱 번째 행: 손해 / I]

lbl_damage["손해"]:1

node_I["I 전자 의료 기록 데이터에 접근할 수 없어 진료 불가"]:3

  
  

%% [스타일 및 디자인 설정]

style lbl_status fill:none,stroke:none,font-weight:bold,color:#4a90e2

style lbl_trigger fill:none,stroke:none,font-weight:bold,color:#4a90e2

style lbl_accident fill:none,stroke:none,font-weight:bold,color:#4a90e2

style lbl_damage fill:none,stroke:none,font-weight:bold,color:#4a90e2

  

style node_A fill:#d1e6f7,stroke:none

style node_B fill:#d1e6f7,stroke:none

style node_C fill:#d1e6f7,stroke:none

style node_D fill:#d1e6f7,stroke:none

style node_E fill:#d1e6f7,stroke:none

style node_F fill:#d1e6f7,stroke:none

style node_G fill:#d1e6f7,stroke:none

style node_H fill:#d1e6f7,stroke:none

style node_I fill:#d1e6f7,stroke:none
~~~


### Case 2. 대책 수립
#### Input
~~~
A 씨는 주유할 떄, 정전기 제거를 깜빡하곤 합니다. 어느 날 A씨가 주유 중일 때, 정전기 불꽃이 튀어 휘발유에 불이 붙는 바람에 차량이 크게 망가졌습니다. 손상이 너무 심해 폐차할 수밖에 없었습니다.
~~~
#### output
~~~ mermaid
flowchart TD
    %% =====================================================
    %% 발생 경위 + 대책 결합 다이어그램
    %% =====================================================

    subgraph 발생경위 ["발생 경위"]
        direction TB
        
        State["<b>상태</b><br/>주유 작업 중 정전기 제거를 잊는 일이 있음"]
        Trigger["<b>트리거</b><br/>어느 날 주유 작업 중 정전기 불꽃이 튐"]
        Accident["<b>사고</b><br/>휘발유에 불이 붙음"]
        Damage["<b>손해</b><br/>차량이 크게 손상돼 폐차함"]
        
        State --> Trigger
        Trigger --> Accident
        Accident --> Damage
    end

    subgraph 대책 ["대책"]
        direction TB
        
        StateC["안전 교육 강화"]
        TriggerC["정전기가 잘 발생하지 않도록<br/>작업장 온도 관리"]
        AccidentC["휘발유 차량 대신 전기차를 사용"]
        DamageC["손해보험 가입"]
    end

    %% 각 단계와 대책을 점선으로 연결
    State ~~~ StateC
    Trigger ~~~ TriggerC
    Accident ~~~ AccidentC
    Damage ~~~ DamageC

    %% =====================================================
    %% 스타일링
    %% =====================================================
    classDef leftBox fill:#BBDEFB, stroke:#1976D2, stroke-width:2px, rx:8, ry:8
    classDef rightBox fill:#C8E6C9, stroke:#388E3C, stroke-width:2px, rx:8, ry:8

    class State,Trigger,Accident,Damage leftBox
    class StateC,TriggerC,AccidentC,DamageC rightBox

    linkStyle 0,1,2 stroke:#1976D2, stroke-width:2px
    linkStyle 3,4,5,6 stroke:#81C784, stroke-width:1.5px, stroke-dasharray:5,3
~~~
