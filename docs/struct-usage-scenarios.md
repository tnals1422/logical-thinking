---
tags: [struct, usage, scenarios, spec]
created: 2026-06-19
type: specification
priority: high
status: draft-for-review
---

# Struct Agent Team (Minto 기반) — Usage Scenarios Specification

> **목적**: Struct Agent Team (Minto 기반) 개선을 위한 **구체적인 사용자 사용 시나리오 명세**. "시나리오 없이 agent를 만들지 않는다"는 원칙을 지키기 위한 내부 개발 문서.
> **대상**: Orchestrator / specialists 구현자, 스킬 작성자, 품질 검토자
> **원칙**: 모든 주요 동작(라우팅, Prior 주입, Consumption 규칙 등)은 최소 하나 이상의 구체 시나리오에 의해 정당화되어야 한다.
> **주의**: 이 문서는 end-user용 가이드가 아니라, agent 팀 자체를 개선하기 위한 spec이다.

## 개요

이 문서는 Struct Agent Team (Minto 기반)의 **실제 사용 맥락**을 시나리오 수준으로 상세히 정의한다.

기존 `struct-docs/usage/*.md` (치트시트)는 명령어 문법 중심으로 너무 추상적이며, 아래 문제를 가지고 있었다:
- Chaining(think → write) 의 실제 동작 기대치 불명확
- Prior Thinking Reuse가 언제/어떻게 동작해야 하는지 구체 기준 없음
- 입력 품질, 반복 작업, override 상황에 대한 처리 전략 부재
- Solve / Express가 거의 실제 사용되지 않아 검증되지 않음

**현재 실제 사용 현황 (2026-06 관찰)**: 
주로 긴 투자/거시 분석 브레인덤프 → 피라미드 구조화 → 적합한 템플릿(현재 SCQA 중심, 향후 IAEJ/목적방침/구조사건대처/상태트리거사고손해 등) 리포트 체이닝 중심. Solve와 Express는 아직 거의 사용되지 않음.

(참고: 사용자向け 치트시트는 `struct-docs/usage/*.md` 에 별도 유지)

이 명세는 위 문제를 해결하기 위한 **재점검의 기반**이다.

---

## 우선순위 및 분류

| ID | 시나리오 | 우선순위 | 현재 실제 사용 빈도 (관찰) | 핵심 위험 |
|----|----------|----------|-----------------------------|----------|
| S01 | Standalone `/struct-think` (브레인덤프 구조화) | P0 | 높음 (실제 주 사용) | coreClaim 품질, MECE 엄격성, reverse structuring |
| S02 | `/struct-think` → `/struct-write` 체이닝 | P0 | 높음 | Prior 선택 정확도 + Read + Pyramid Consumption 품질 |
| S03 | Standalone `/struct-solve` (문제 분석) | P1 | 매우 낮음 (거의 미사용) | 가설 MECE, Root Cause 깊이, Prior framing 오용 |
| S04 | Prior 기반 `/struct-express` | P1 | 매우 낮음 (거의 미사용) | Level 1 → 형식 매핑 품질, Marp/1페이지 규칙 준수 |
| S05 | 반복 정교화 (Iterative) | P0 | 중간 (자연 발생) | Memory 참조, 증분 vs 전체 재작성 판단 |
| S06 | Cross-session Prior Reuse | P1 | 중간 | 유사도 판단 로직의 정확성, 오래된 prior 처리 |
| S07 | 모호/역방향 입력 + Override | P0 | 빈번 | 자동 판단 vs 명시적 신호 처리 일관성 |

## 작업 모드 (필수 구분: 두 가지 운영 방식)

사용자는 **항상 두 가지 명확한 옵션**을 선택할 수 있어야 한다.

### 옵션 1: 인간 참여형 (Human-in-the-Loop / Collaborative)
**목적**
- Agent의 Minto 사고 과정을 사용자가 직접 관찰하고 참여하면서 **내재화**
- 잘못된 결과물(약한 coreClaim, MECE 위반, 부정확한 Prior 소비 등)을 실시간으로 방지
- Agent를 "도구"가 아니라 "사고 코치"로 사용

**특징**
- 중간 단계에서 사용자 개입 기회 제공 (Prior 선택 확인, Critique Pass 결과 검토, 핵심 주장 수정 제안 등)
- Agent는 자신의 판단 근거를 상세히 설명하고, 불확실한 부분을 명시적으로 물어봄
- Memory / Prior 자동 주입 시에도 "이 prior를 사용할까요?" 같은 확인 단계 권장
- 최종 결과물 생성 전 반드시 사용자의 승인/수정 루프를 거침

**적합한 상황**
- 학습 목적
- 중요한 의사결정 (투자, 전략, 보고서 등)
- Agent의 품질을 지속적으로 높이고 싶은 단계

### 옵션 2: 자율 실행형 (Autonomous / Fast Production)
**목적**
- 최소 개입으로 **빠르게 결과물 생산**
- 당장 써야 하는 보고서, 슬라이드, 분석이 필요할 때

**특징**
- Orchestrator가 대부분의 판단을 자동 수행 (prior 선택, 옵션 결정, 중간 검증)
- 가능한 한 full pipeline을 한 번에 실행
- 사용자는 최종 결과물만 받고, 필요 시 "더 수정해" 또는 "이 부분만 고쳐"로 후속 지시
- 투명성(무엇을 했는지 요약)은 유지하되, 단계별 확인은 최소화

**적합한 상황**
- 시간 압박이 있을 때
- 이미 agent 팀에 대한 신뢰가 쌓인 후
- 초안 생산 → 사용자가 별도 편집하는 워크플로

### 모드 적용 원칙 (모든 시나리오에 공통)

**기본 동작**
- 모드 미지정 시 → **기본은 옵션 1 (인간 참여형)**
- 모드 전달 방법:
  - 자연어: "천천히 같이 해줘", "자세히 검토하면서", "빠르게", "지금 당장 초안만"
  - 명시 옵션: `mode: collaborative` / `mode: autonomous`

**Operational Differences (Orchestrator & Specialist 공통)**

| 측면                  | 옵션 1: 인간 참여형 (Collaborative)                          | 옵션 2: 자율 실행형 (Autonomous)                          |
|-----------------------|-------------------------------------------------------------|---------------------------------------------------------|
| **개입 타이밍**       | 중간 단계마다 확인 요청 (Prior, 주요 판단, 최종 전)           | 전체 파이프라인 자동 실행, 최종 결과만 반환               |
| **설명 수준**         | 판단 근거 + 불확실성 + 대안 상세 설명                        | "이렇게 결정했습니다" + 짧은 요약만                       |
| **확인 포인트**       | Prior 선택, 핵심 주장, MECE/Critique 결과, 저장 전           | 거의 없음 (사용자 후속 지시 대기)                         |
| **출력 스타일**       | 시각 자료(표·다이어그램) + 개조식 + 설명 추가                 | 동일 기본 스타일 유지하되 더 간결하게                     |
| **품질 하한선**       | MECE + Critique Pass + Pyramid Data **반드시** 수행         | 동일 (자동으로 수행)                                      |
| **Orchestrator 역할** | 모드 감지 → Specialist에게 "collaborative 모드" 전달 + 확인 프롬프트 추가 | 모드 감지 → full 자동 라우팅 + 최소 요약 생성             |
| **Specialist 역할**   | 단계별 판단 공개 + "이 판단이 맞습니까?" 식 질문               | 내부적으로 모든 단계 수행 후 결과만 반환                   |

**공통 제약**
- 두 모드 모두에서 **MECE 검증 + Critique Pass + Pyramid Data**는 절대 생략 불가
- 사용자가 "이 모드로 해줘"라고 명시하면 해당 모드를 우선 적용
- 모드 전환은 언제든 가능 (중간에 "이제 자율로 마무리해" 가능)

이 두 옵션은 모든 S01~S07 시나리오에 걸쳐 **구체적인 행동**으로 구현되어야 한다.

### 모드별 핵심 행동 요약 (S01~S07)

| 시나리오 | 옵션 1 (참여형) 주요 포인트 | 옵션 2 (자율형) 주요 포인트 |
|----------|-----------------------------|-----------------------------|
| S01 Think | Prior 확인 → 단계별 (CS/GPS/Pyramid/MECE) 승인 → 최종 visual 확인 | 자동 전체 실행 → 최소 요약 + 저장 경로 |
| S02 Write | Prior 사용 확인 + coreClaim/Level1 논의 + MECE 승인 | 자동 Pyramid Consumption → 변경 요약만 |
| S03 Solve | Framing/가설/Root Cause/해결안 단계별 확인 + 테이블 제시 | 자동 5 Whys + 테이블 생성 → 요약 표 |
| S04 Express | Prior/형식/매핑 확인 + 미리보기 승인 | 자동 format 매핑 → 간단 생성 완료 |
| S05 Iterative | 개선 범위 협의 + 전후 비교 + 저장 승인 | 자동 증분 → 주요 변경 bullet 3~5개 |
| S06 Cross Prior | 오래된 prior 유효성 확인 + 후보 선택 | 자동 최근 우선 + 한 줄 노트 |
| S07 Override | 신호 확인 + 가정 공개 + visual 승인 | 즉시 raw 전환 + 최소 주석 |

기본 출력 스타일(개조식 + 표/다이어그램)은 두 모드 공통. 참여형은 설명 보강, 자율형은 간결화.

---

## 상세 시나리오

### S01: Standalone Think — 복잡한 주제 브레인덤프 구조화

**User Goal**  
산재된 생각, 강의 내용, 시장 관찰, 아이디어를 **한 번에** 최고 품질의 구조화된 논리 피라미드로 정리하고 싶다. 나중에 `/struct-write`, `/struct-solve`, `/struct-express`에서 재사용할 수 있는 견고한 Artifact를 얻고 싶다.

**Persona / Context**  
개인 투자 분석가 / 전략가 / 지식 노동자. 한국 거시경제, 산업, 기업 투자 테제를 자주 다룸. 입력은 800~3000자 분량의 메모, 강의 요약, 데이터 포인트 + 의견이 산발적으로 섞여 있음.

**Invocation**  
`/struct-think {긴 입력 전체}` (스킬 또는 직접 호출)

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Thinking Agent**
- Prior 검색 후: "이 prior (coreClaim 요약, 유사도 XX%)를 참고할까요?" + Prior의 Level 1 3줄 요약 제시 → 사용자 승인 대기
- CS 분석 단계: 주요 범주 3~5개 추출 후 "이 범주들이 맞습니까? 누락된 축 있나요?" 확인
- GPS 구조화 단계: ASCII/Mermaid diagram 생성 후 "이 구조가 가장 유용한가요?" 확인
- Pyramid Building 단계: coreClaim 초안 제시 → "이 한 문장이 전체를 대표합니까?" 확인
- MECE + Critique Pass 후: "MECE 결과: passed / partial" + 주요 이슈 1~2개 공개 → "이 구조로 진행할까요?" 승인
- 최종 출력 전: 전체 피라미드 요약(visual) + "이 결과를 struct-docs/에 저장할까요?" 확인

**옵션 2 (자율 실행형) — Orchestrator + Thinking Agent**
- Prior가 충분히 유사하면 자동 주입 (요약 한 줄만 언급: "관련 prior 자동 적용")
- 전체 6-Step + Critique Pass를 내부 수행
- Reverse structuring 감지 시에도 자동으로 처리
- 최종 결과: 
  - 핵심 주장 1문장
  - Level 1 bullets + 간단 visual (ASCII or Mermaid)
  - "struct-docs/01-thinking/xxx.md에 저장했습니다"
- 추가 설명이나 중간 판단은 최소화

**Invocation 예시**
- Collaborative: `/struct-think 자세히 같이 해줘 ...` 또는 `mode: collaborative`
- Autonomous: `/struct-think 빠르게` 또는 `mode: autonomous`

**Input Profile**
- 길고 비구조화됨 (브레인덤프)
- 여러 주제가 섞여 있거나, 핵심 주장이 뒤에 나옴 (reverse)
- 데이터, 의견, 질문, 가정이 혼재
- "결론적으로 ... 해야 한다" 스타일이 자주 등장

**Concrete Example (실제 사용 스타일)**
```
현대차가 피지컬 AI로 가는 과정에서 자율주행 파운드리 모델(Momenta)과 구글/엔비디아 중립 협력, 그리고 OpenAI/SpaceX/Anthropic IPO가 시장을 AI 비즈니스 모델로 전환시킬 때 KOSPI가 3두마차 체제로 갈 수 있다는 생각이 든다. 중국은 보안 문제로... (800~2000자 분량의 산발적 메모 + 데이터 포인트 + 개인 의견 혼재)
```

**Happy Path Flow**
1. Orchestrator가 think 명령 인식, memory 로드
2. Prior 검색 (이 주제와 강하게 겹치는 게 있으면 주입하되, standalone이므로 강제는 아님)
3. Thinking Agent 호출 (full 6-Step + Mandatory Critique Pass + MECE)
4. Pyramid Data 블록 포함하여 출력
5. `struct-docs/01-thinking/YYYYMMDD-*.md` 저장 + memory `previousThoughts` 추가

**기대 동작 (에이전트가 반드시 만족해야 할 것)**
- coreClaim 정확히 **1문장** (사용자가 "이게 핵심이구나" 할 수 있는 수준)
- Level 1: 3~5개, MECE 통과 (또는 partial과 이유 명시)
- GPS 구조 (ASCII + 테이블) 명확
- Critique Pass 결과와 개선 여부 투명 기록
- Reverse structuring인 경우 `structuringPath: "reverse"` 정확히 기록
- Pyramid Data JSON 블록 마지막에 포함 (downstream 소비용)

**Success Criteria (관찰 가능)**
- 사용자가 추가 질문 없이 "이제 이걸로 보고서 써줘" 또는 "이거 괜찮네"라고 함
- 30일 후 다시 열어봤을 때 구조가 여전히 설득력 있음
- Pyramid Data를 parsing해서 Level1 배열을 즉시 추출할 수 있음

**Critical Edge Cases**
- 입력이 1개 주제로 명확히 압축되지 않을 때 (어떻게 처리?)
- 강한 반론이 있는데도 coreClaim이 과도하게 확신적일 때
- 데이터가 부족한데도 "추론 보충"을 어디까지 할지
- 너무 많은 Level 1 후보가 나올 때 (5개 초과 처리)

**현재 구현 상태 (2026-06-19)**
- P0 Mode별 상세 행동 (S01 Collaborative/Autonomous 체크리스트)이 thinking.md에 정확히 반영됨
- Pyramid Data 블록 + MECE/Critique 필수
- Orchestrator Step 0.5 모드 감지 + Prior 주입 동작
- 추가 템플릿 선택은 write에서 담당 (think는 standalone)

---

### S02: Think → Write 체이닝 (가장 중요한 가치 흐름)

**User Goal**  
`/struct-think`로 구조를 잡은 다음, **최소한의 중복 사고 없이** 내용 특성에 맞는 템플릿(기본 SCQA, 또는 IAEJ/목적방침/구조사건대처 등)을 적용한 보고서로 빠르게 변환하고 싶다. Prior pyramid의 coreClaim과 Level 1을 최대한 존중하면서도, narrative와 배경/문제/질문은 자연스럽게 보강되기를 원함. (정보 정리 후 agent가 템플릿 선택)

**Persona / Context**  
메모, 학습용 문서, 보고서, 전략 문서, 기획서, 제안서를 자주 작성하는 사람. think 결과가 이미 있고, 같은 세션 또는 다음 세션에서 write를 호출.

**Invocation**  
1. `/struct-think {주제}` 수행
2. (같은 세션 또는 이후) `/struct-write {주제 또는 think와 유사한 지시}`

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Writing Agent**
- Orchestrator: 관련 prior 발견 시 "이 pyramid (coreClaim + Level 1 요약)를 기반으로 [적합한 템플릿] 작성할까요? (선택 후보: SCQA/IAEJ/목적방침/구조사건대처 등)" 확인 + source 파일명 표시
- Writing Agent:
  - coreClaim: "이 문장을 그대로 4.1 핵심 주장으로 쓸까요? 약간 수정할까요?" 확인
  - Level 1 매핑: 각 근거를 bullet으로 먼저 보여주고 "이 bullet을 narrative로 확장할까요, 그대로 둘까요?" 논의
  - Situation/Complication: 초안 작성 후 "이 배경 설명이 정확합니까?" 확인
  - Answer 섹션 완성 전: MECE + Critique 요약 공개 → "이 문서로 저장할까요?" 최종 승인
- 출력: bullets + 표 위주 + "이 pyramid 기반" 투명 표기

**옵션 2 (자율 실행형) — Orchestrator + Writing Agent**
- 관련 prior가 있으면 자동 주입
- Writing Agent는 Pyramid Consumption 규칙을 내부 적용하여 pyramid 분석 후 내용 특성에 맞는 템플릿 선택 → full 문서 생성 (SCQA는 기본 중 하나)
- 변경된 부분만 간단 요약 제공 (예: "coreClaim 95% 유지, Level 1 3개 중 1개 보강")
- 최종 출력: 
  - Executive Summary (bullet 중심)
  - Answer 섹션 (Level 1 bullets + 최소 narrative)
  - "struct-docs/02-writing/xxx.md 저장 완료 (prior 기반)"
- 추가 확인 없이 바로 반환

**Invocation 예시**
- Collaborative: `/struct-write 천천히 같이 {주제}` 또는 `mode: collaborative`
- Autonomous: `/struct-write 빠르게` 또는 `mode: autonomous`

**Input Profile**
- write 입력은 think 입력과 동일하거나 약간 더 짧거나, "이걸 보고서로 만들어줘" 스타일

**Happy Path Flow (Prior Reuse 기대)**
1. Orchestrator: write 명령 인식 + 옵션 파싱 (`use-prior`, "이전 생각 무시" 등)
2. `previousThoughts`에서 주제 유사도 판단:
   - User Input 키워드 + 각 prior의 `coreClaim` + `pyramidSummary`로 점수 계산
   - 최고 점수 1개만 선택 (너무 낮으면 prior 없음)
3. 선택된 prior의 `savedTo` 경로로 **Read** 도구 호출하여 전체 파일 로드
4. 파일에서 Pyramid Data / coreClaim / Level 1 등을 추출해 정확한 형식으로 `## Previous Thinking Pyramid` 블록 주입
5. Writing Agent 호출 (Pyramid Consumption 규칙 적용)
6. Writing Agent는:
   - coreClaim을 4.1 핵심 주장 우선 사용
   - Level 1을 4.2 근거의 구조 기반으로 매핑
   - 여전히 MECE 검증 + Critique Pass 수행 + 기록
7. 결과 저장 + memory previousDocuments 업데이트

**기대 동작 (매우 중요)**
- "raw input에서 처음부터 다시 Minto thinking 하지 말고" pyramid 기반으로 작업
- Prior가 있으면 Situation/Complication은 사용자의 원래 입력 + prior의 CS 일부를 참고해 보강
- 최종 문서에도 "이 pyramid 기반으로 작성" 여부를 투명하게 언급할 수 있어야 함 (선택)
- Prior를 사용했더라도 반드시 MECE + Critique Pass 결과 포함

**Success Criteria**
- think에서 나온 coreClaim이 write의 Executive Summary와 Answer의 4.1에 거의 그대로 또는 최소 수정으로 등장
- Level 1 근거가 4.2의 큰 구조를 형성
- 사용자가 "이전 생각이 잘 반영됐다" 또는 "생각보다 빠르고 좋다"고 느낌
- think와 write의 coreClaim이 심각하게 충돌하지 않음

**Critical Edge Cases**
- think 후 바로 write를 하지 않고, 여러 think가 memory에 쌓였을 때 "잘못된 prior 선택"
- 사용자가 "이전 생각 무시하고 새로 써줘"라고 명시
- think의 MECE가 partial이었는데 write가 그걸 그대로 받아들임
- 주제가 약간 달라서 prior가 부분적으로만 유효할 때

**현재 구현 상태 (2026-06-19)**
- P0 Mode별 상세 행동 (S02 Collaborative/Autonomous)이 writing.md에 반영됨 (Prior 확인, Level 1 bullet 먼저, 변경 요약)
- Pyramid Consumption 규칙 + 템플릿 선택 로직 (Glob + 내용 분석) 구현
- Orchestrator Read + 주입 + 모드 전달 완료
- 템플릿은 `struct-docs/templates/` (`deliverables/` · `patterns/` · `express/` · `shared/`)에 있음 (사용자 추가 가능)

---

### S03: Standalone Solve — 문제 분석 및 해결안 도출

**User Goal**  
명확한 "문제"가 주어졌을 때, 감정적으로 접근하지 않고 구조화된 논리 피라미드로 문제를 분석하고, 가설을 MECE하게 세우고, 근본원인을 파고, 실행 가능한 해결안을 우선순위와 함께 얻고 싶다.

**Persona / Context**  
팀 리더, 운영자, 컨설턴트 스타일. "왜 생산성이 떨어졌는가", "고객이 왜 이탈하는가" 같은 operational 문제.

**Invocation**  
`/struct-solve {문제 설명 전체}`

**Input Profile**
- 문제 상황 + 일부 관찰 데이터가 있음
- "원인을 모르겠다" 또는 "대응 방안을 모르겠다"가 핵심
- 종종 "왜" 질문 형태

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Problem-solving Agent**
- Orchestrator: 관련 prior 발견 시 "이 pyramid를 문제 구조화(framing)에 참고할까요? (Level 1 요약 3줄)" 확인
- Problem-solving Agent:
  - 문제 정의 단계: "문제의 주요 축"을 bullet으로 제시 후 "이 축으로 충분합니까? 추가할 관점 있나요?" 확인
  - 가설 생성: 최소 3개 가설을 표로 보여주고 "이 가설들이 MECE합니까? 수정할 가설 있나요?" 논의
  - Root Cause: 5 Whys 결과 + 인과 모델(독립/종속 사건) 시각화 후 "이 근본 원인이 맞습니까?" 확인
  - 해결안: Impact/Effort/Urgency 테이블 제시 → "우선순위 조정 원하는 부분 있나요?" 승인
- 최종 전: MECE + Critique 요약 + "이 분석으로 struct-docs/03-solving/에 저장할까요?" 확인

**옵션 2 (자율 실행형) — Orchestrator + Problem-solving Agent**
- prior가 있으면 framing 품질 향상에 자동 참고
- 전체 Phase 자동 수행: 문제 분해 → 최소 3개 가설(MECE) → 5 Whys + 인과 모델 → 해결안 평가 테이블 → 액션 플랜
- 결과:
  - 가설 3개 + 우선순위 해결안 표 요약
  - "struct-docs/03-solving/xxx.md 저장 완료"
- 추가 질문 없이 바로 반환 (사용자 후속: "이 가설 더 자세히" 등)

**Invocation 예시**
- Collaborative: `/struct-solve 자세히 같이 해줘 {문제}`
- Autonomous: `/struct-solve 빠르게 {문제}` 또는 `mode: autonomous`

**Happy Path Flow**
1. Orchestrator가 solve 인식
2. (가능하면) 관련 prior thinking 검색 → Problem Framing 품질 향상에만 사용 (직접 주장 이식 금지)
3. Problem-solving Agent 호출
4. 내부: Minto 피라미드로 문제 분해 (1. 문제 정의)
5. 최소 3개 가설 생성 (MECE)
6. 5 Whys + 인과관계 모델 (독립/종속 사건)
7. 해결안 평가 (Impact/Effort/Urgency) + 우선순위 + 액션 플랜
8. 항상 MECE 검증 + Critique Pass 기록

**기대 동작 (Prior 활용 시)**
- Prior pyramid를 "문제의 가능한 축" 또는 "고려 관점"으로 참고
- "이 prior의 결론을 답으로 쓰지 말 것" 명확 구분 (writing과 다름)

**Success Criteria**
- 가설이 최소 3개이며 서로 배타적
- Root Cause가 표면 원인과 구분됨
- 해결안 테이블이 실제 실행 가능 (담당자/기한 placeholder 포함)
- 사용자가 "이렇게까지 체계적으로 생각 못 했다"고 느낌

**Critical Edge Cases**
- 문제가 너무 광범위해서 가설이 5개 이상 필요할 때
- Prior가 있는데 solve가 그걸 "답"으로 착각할 때
- 데이터가 전혀 없을 때 가정 명시 수준
- Solve 결과를 다시 write로 문서화하고 싶을 때의 연결

**현재 구현 상태 (2026-06-19)**
- S03 Mode별 상세 행동 (P1)이 problem-solving.md에 반영됨
- Prior는 framing 용도로만 사용 (Pyramid Consumption과 구분)
- 실제 사용 빈도는 낮음 (P1)

---

### S04: Prior 기반 Express — 구조화된 내용을 발표/메모로 변환

**User Goal**  
이미 구조화된 think 또는 write 결과를 가지고, **형식만 바꿔서** 바로 발표용 슬라이드나 팀 공유용 1페이지 메모를 만들고 싶다. 다시 전체 thinking을 하지 않기를 원함.

**Persona / Context**  
회의에서 발표하거나, 상사/팀원에게 빠르게 공유해야 하는 상황.

**Invocation**  
`/struct-express slide {주제 또는 "이전 결과 기반으로"}`  
`/struct-express memo ...`  
(또는 think/write 결과 파일을 참조하는 식으로 호출)

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Expression Agent**
- Orchestrator: Prior 발견 시 "이 pyramid (coreClaim + Level 1 요약)를 기반으로 변환할까요? 형식은 slide / memo / story 중 무엇으로 할까요?" 확인
- Expression Agent:
  - 핵심 메시지: coreClaim을 "슬라이드 최상단 메시지"로 제시 후 "이 메시지가 정확합니까?" 확인
  - Level 1 매핑: 각 근거를 "슬라이드/메모 섹션"으로 어떻게 나눌지 bullet으로 보여주고 "이 구조로 진행할까요?" 확인
  - 형식 세부: slide인 경우 "슬라이드 수(5~12장)와 각 장 bullet 수" 제안 → 조정 논의
  - 생성 후: 전체 구조 미리보기(visual) + "이 출력으로 만족하십니까? 특정 슬라이드 수정할까요?" 확인
- 출력: 기본 개조식 + 시각 구분 유지

**옵션 2 (자율 실행형) — Orchestrator + Expression Agent**
- 관련 prior 자동 주입
- 요청 format에 따라 자동 매핑 (coreClaim → 메시지, Level 1 → 근거)
- 전체 콘텐츠 즉시 생성 (Marp slide / 1페이지 memo / story)
- 결과:
  - "struct-docs/04-expressing/xxx.md 생성 완료 (7장 슬라이드 / 1페이지 메모)"
  - 간단한 구조 요약만 제공
- 후속 지시 대기

**Invocation 예시**
- Collaborative: `/struct-express slide 자세히 같이 {주제}`
- Autonomous: `/struct-express 빠르게 memo {주제}` 또는 `mode: autonomous`

**Happy Path Flow**
1. Orchestrator가 express + format 인식
2. 관련 prior (특히 최근 think 또는 write) 검색 + Read
3. `## Previous Thinking Pyramid` 주입
4. Expression Agent 호출
5. Prior의 coreClaim → 핵심 메시지 슬라이드 최상단
6. Level 1 → 개별 슬라이드 또는 메모 근거
7. 형식 요구사항 (Marp `---`, 1페이지 제한, 스토리 구조) 엄격 준수

**Success Criteria**
- 슬라이드 5~12장, 각 장당 포인트 3개 이내
- coreClaim이 제목/핵심 메시지 슬라이드에 명확히 등장
- 사용자가 "이거 바로 Marp에 붙여넣고 발표하면 되겠네"라고 함

**Critical Edge Cases**
- Prior가 report인데 express memo로 할 때 요약 수준 조절
- 슬라이드와 memo의 구조 매핑 규칙 차이
- "story" 형식일 때 선택된 템플릿(예: SCQA 또는 다른 패턴)을 narrative로 어떻게 풀 것인가

**현재 구현 상태 (2026-06-19)**
- S04 Mode별 상세 행동 (P1)이 expression.md에 반영됨
- Prior → 슬라이드/메모 매핑 규칙 존재
- 실제 사용 빈도는 낮음 (P1)

---

### S05: 반복 정교화 (Iterative Refinement)

**User Goal**  
한 번 만든 pyramid/report를 "더 좋게" 만들고 싶다. "리스크 더 넣어", "반론 고려해서 다시", "이 근거 더 깊게 파봐".

**Invocation**
- 같은 주제로 다시 `/struct-think` 또는 `/struct-write` 호출하면서 지시
- "이전 결과 수정해줘", "리스크 더 고려해서 다시 분석해" 스타일

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Specialist**
- Orchestrator: "이전 pyramid (timestamp + coreClaim)를 기반으로 증분 개선할까요?" + 이전 버전 주요 차이 3줄 제시
- 범위 협의: "어떤 부분을 집중할까요? (1) 리스크 추가 (2) 반론 보강 (3) 데이터 업데이트" — 사용자 선택
- 변경 후: "변경 전/후 비교" 표 + "이 수정으로 coreClaim이 달라졌습니까?" 확인
- 저장: "새 파일로 저장할까요? (이전 버전 유지)" 승인

**옵션 2 (자율 실행형) — Orchestrator + Specialist**
- 가장 최근 관련 prior 자동 선택
- 증분 개선을 내부 수행 (무엇을 바꿀지 자동 판단)
- 결과: "주요 변경사항" bullet 3~5개 + "struct-docs/에 새 버전 저장 완료"
- "이전 버전과 비교 원하시면 말씀해주세요" 한 줄 안내
- 추가 설명 최소화

**Invocation 예시**
- Collaborative: `/struct-think 이전처럼 자세히 같이 {주제}`
- Autonomous: `/struct-write 빠르게 증분 {주제}`

**Happy Path**
- Orchestrator가 memory + 파일 Read를 통해 가장 관련성 높은 prior pyramid를 강하게 우선 선택
- 대상 Agent (thinking/writing)가 "증분 개선" 모드로 동작:
  - 전체를 처음부터 재구성하지 않고, 기존 coreClaim/Level 1을 기반으로 보강/수정
  - 변경된 부분과 이유(예: 반론 추가, 데이터 업데이트)를 명확히 기록
- 결과는 새 파일로 저장 (버전 관리 성격) + memory에 추가

**Success Criteria**
- 사용자가 "이전보다 리스크가 잘 반영됐다", "더 날카로워졌다"고 느낌
- 이전 버전과 새 버전의 차이점(추가/수정된 Level 1, critique 결과)이 쉽게 비교 가능
- coreClaim이 크게 흔들리지 않고 정교해짐

**Critical Edge Cases**
- 사용자가 "완전히 새로 해" vs "이것만 고쳐"를 명확히 구분하지 않을 때
- 여러 prior 중 어떤 것을 기반으로 할지 Orchestrator가 잘못 선택
- 증분 개선이 오히려 논리를 약화시키는 경우 (Critique Pass에서 잡아야 함)

**현재 구현 상태 (2026-06-19)**
- S05 Mode별 상세 행동 (P0)이 thinking.md와 writing.md에 반영 (증분 개선, 전후 비교, 범위 협의)
- "이전 pyramid 기반으로 증분" 흐름 지원
- Memory append-only + prior 우선 선택 로직 존재

---

### S06: Cross-session Prior Reuse

**User Goal**  
며칠 전에 `/struct-think` 해둔 내용을, 지금 다른 문맥에서 `/struct-write`나 `/struct-solve`에 활용하고 싶다.

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator**
- cross-session prior 후보 발견 즉시: "XX일 전 prior (coreClaim 한 줄 + Level 1 2줄 요약, 유사도 XX%)를 사용할까요?" 확인
- 오래된 prior(30일 이상)인 경우: "이 prior는 30일 이상 지났습니다. 내용이 여전히 유효하다고 보십니까?" + 짧은 요약 제시
- 여러 후보 존재 시: 후보 목록(최근 순) 제시 → 사용자 선택
- 승인 후: 파일 Read → "이 prior를 Context에 주입했습니다" 확인

**옵션 2 (자율 실행형) — Orchestrator**
- 유사도 기준 통과하는 prior 자동 선택 (가장 최근 우선, 최대 1개)
- 30일 이상 prior도 자동 적용 (단, "오래된 prior 적용 (timestamp)" 한 줄 노트)
- 선택 근거: "유사도 XX%로 자동 선택"
- 주입 후: "struct-docs/XX/... prior 자동 적용" 요약만 제공
- 사용자가 후속으로 "다른 prior로" 요청 시 교체

**주요 위험**
- Orchestrator의 "주제 유사도 판단"이 정확해야 함
- 30일 이상 지난 prior에 대한 처리 (오래된 정보 플래그)
- 여러 유사한 prior 중 최선 선택

**현재 구현 상태 (2026-06-19)**
- S06 Mode별 상세 행동 (P1)이 orchestrator.md + specialists에 반영 (Collaborative: 확인, Autonomous: 자동 + 한 줄 노트)
- 유사도 기반 선택 + 30일 이상 prior 플래그 로직 존재
- 실제 사용 검증은 아직 낮음 (P1)

---

### S07: 모호 입력 / 역방향 입력 + 명시적 Override

**User Goal**
- 입력이 애매하거나 감정적일 때도 최대한 구조화해 주기를 바랄 때
- "이전 생각 완전히 무시하고 raw로 해줘"라고 명확히 지시했을 때

**처리 요구사항**
- Orchestrator는 Step 0에서 명시적 옵션 우선 파싱:
  - `use-prior: false`, `source: raw`, `ignore prior`, "이전 생각 무시", "raw로 새로" → 강제 무시
- 자연어 신호도 인지
- Reverse structuring 조건 자동 감지 (`결론적으로`, "해야 한다" 등) → `structuringPath: "reverse"` 기록
- 모호 입력 시 가정 명시 + CS 단계에서 보충 범주 적극 사용

**두 모드별 동작 차이 (구체적 행동)**

**옵션 1 (인간 참여형) — Orchestrator + Thinking Agent**
- Orchestrator가 override/모호 신호 감지 즉시: "이 입력이 모호하거나 이전 prior를 무시하라는 신호가 있습니다. 어떻게 할까요?" + 두 옵션 제시
- Thinking Agent:
  - "이 입력의 모호한 부분을 다음과 같이 해석했습니다" (bullet 3~4개 가정) → "이 가정들이 괜찮습니까?" 확인
  - 구조화 후: "이 구조는 reverse structuring입니다" 설명 + "이 방향으로 진행할까요?" 승인
- 최종 전: 모호 입력으로 인한 가정 목록 + visual + "이 결과를 저장할까요?" 확인

**옵션 2 (자율 실행형) — Orchestrator + Thinking Agent**
- override 신호 강하면 즉시 raw 모드 전환 (prior 완전 배제)
- 모호한 부분은 내부에서 최소 가정 설정 후 전체 구조화 수행
- 결과에 한 줄 주석: "모호한 입력으로 인해 다음 가정들을 세웠습니다"
- 추가 설명 최소화, 바로 결과 + 파일 경로 반환
- 사용자가 "가정 조정해줘" 요청 시에만 상세 공개

**Invocation 예시**
- Collaborative: `/struct-think 자세히 같이 (이전 무시하면서) {입력}`
- Autonomous: `/struct-think 빠르게 override {입력}`

**현재 구현 상태 (2026-06-19)**
- S07 Mode별 상세 행동 (P0)이 thinking.md에 반영 (override 신호 감지, 가정 bullet 제시 + 확인 vs 즉시 raw)
- Orchestrator Step 0 옵션 파싱 (use-prior:false 등) + 자연어 지원
- Reverse structuring 자동 감지 + 기록 지원

---

## P0 시나리오 핵심 검증 질문 (Gap Analysis 시 사용)

**S01 Standalone Think**
- 입력이 길고 산만할 때 CS 단계에서 중요한 축을 놓치지 않는가?
- Reverse structuring을 정확히 감지하고 `structuringPath`를 기록하는가?
- Pyramid Data 블록이 항상 생성되어 downstream 소비가 가능한가?
- **모드 구분**:
  - Collaborative: Prior 확인, 단계별 판단 공개, 최종 전 승인 요청이 명확한가?
  - Autonomous: 자동 판단 후 최소 요약 + 저장 경로만 제공하는가?

**S02 Think → Write**
- Orchestrator가 올바른 prior를 선택하고 Read로 전체 파일을 로드하는가?
- Writing Agent가 "raw부터 재생각" 하지 않고 pyramid를 기반으로 작성하는가?
- Prior를 사용한 경우에도 MECE + Critique Pass를 독립적으로 수행하는가?
- **모드 구분**:
  - Collaborative: Prior 사용 여부 확인 + 주요 판단 노출 + 승인 루프가 있는가?
  - Autonomous: prior 자동 적용 후 "변경 요약"만 제공하는가?

**S05 반복 정교화**
- "증분 개선" 모드를 지원하는가? (전체 재작성 vs 수정 구분)
- 이전 버전과 차이점을 사용자가 쉽게 파악할 수 있는가?
- Orchestrator가 반복 호출 시 가장 최근/관련 prior를 우선하는가?
- **모드 구분**:
  - Collaborative: 개선 범위 협의 + 변경 전후 비교 + 저장 승인 과정이 있는가?
  - Autonomous: 자동 증분 후 "주요 변경 3~5개" bullet만 제공하는가?

**S07 모호/Override**
- "이전 생각 무시" 같은 자연어 + 옵션을 reliably 파싱하는가?
- Override 시 prior를 완전히 배제하고 raw input만 사용하는가?
- **모드 구분**:
  - Collaborative: override 의도 확인 + 가정 공개 + 상세 설명 제공하는가?
  - Autonomous: 신호 감지 즉시 raw 전환 후 최소 주석만 남기는가?

---

## Gap Analysis — P0 Only (S01, S02, S05, S07) — 정교 버전

**범위**: P0 시나리오만 깊게 분석 (S01 Standalone Think, S02 Think→Write, S05 Iterative, S07 Override).

**분석 방법**:
- Spec = 이 문서의 "두 모드별 동작 차이 (구체적 행동)" + 모드 적용 원칙 + Default Output Style (개조식 + visual 우선)
- 구현 = 현재 `.claude/agents/*.md` (Mode Handling 섹션 + Orchestrator 로직 + 출력 요구사항)
- 형식: Spec 요구 → 현재 구현 → Gap (구체 bullet) → 구체 추천 (프롬프트에 바로 넣을 수 있는 문구)

**분석 프레임워크** (P0 전용):
- **Spec 기준**: 이 문서의 "두 모드별 동작 차이 (구체적 행동)" + 모드 적용 원칙 + Default Output Style
- **구현 기준**: 현재 `.claude/agents/*.md` (Mode Handling 섹션 + Orchestrator 로직 + 출력 요구사항)
- **형식**: Spec 요구 → 현재 구현 → Gap (구체 bullet) → 구체 추천 (프롬프트에 바로 넣을 수 있는 문구)
- **Spec 기준**: 이 문서의 "두 모드별 동작 차이 (구체적 행동)" + "모드 적용 원칙" + "Default Output Style"
- **구현 기준**: `.claude/agents/*.md` 현재 내용 (Mode Handling 섹션, Orchestrator 로직, 출력 요구사항)
- **평가 축**:
  - 모드 감지/전달 정확도
  - Collaborative: 확인 루프·설명·사용자 개입 정도
  - Autonomous: 자동화 범위·간결성
  - 출력 스타일 (개조식 + visual) 강제력
  - 품질 하한선 (MECE/Critique/Pyramid Data) 유지
  - 투명성 (무엇을 했는지 기록)

### P0 요약 대시보드 (깊게 분석)

| 시나리오 | Collaborative 주요 Gap | Autonomous 주요 Gap | 우선순위 |
|----------|------------------------|---------------------|----------|
| S01 Think | 단계별 확인 질문 거의 없음 | "무엇을 생략할지" (Self-Check 등) 미지정 | 최고 |
| S02 Write | "Level 1 bullet 먼저 보여주고 논의" 순서 미구현 | 변경 요약 예시 부족 | 최고 |
| S05 Iterative | 범위 협의 / "전후 비교 표" / 저장 승인 과정 전혀 없음 | 증분 시 "무엇을 바꿀지" 판단 로직 선언적 | 높 |
| S07 Override | "가정 bullet 제시 + 확인" + visual 승인 없음 | raw 전환 후 가정 기록 방식 구체적 지침 없음 | 높 |

**전반 결론 (P0, Gap Analysis 시점)**: 

(이 섹션은 상세 Mode별 행동 추가 *이전* 상태 분석입니다.)

이후 thinking.md / writing.md 에 "P0 Mode별 상세 행동" 섹션이 정확히 추가되어 spec의 확인 루프·질문·자동 분기가 대부분 반영되었습니다. (2026-06-19 기준)

현재는 P0 모드 동작이 agent 프롬프트에 잘 들어가 있습니다. 추가 검증(실제 실행)이 남음.

---

### S01: Standalone Think

**Collaborative Spec (주요 요구)**
- Prior 후: coreClaim + Level 1 3줄 + 유사도 제시 → "참고할까요?" 확인
- CS 후: 주요 범주 3~5개 bullet → "이 축들이 맞습니까? 누락된 축 있나요?" 확인
- GPS 후: ASCII/Mermaid diagram 생성 → "이 구조가 가장 유용한가요?" 확인
- Pyramid 후: coreClaim 초안 제시 → "이 한 문장이 전체를 대표합니까?" 확인
- MECE + Critique Pass 후: 결과 공개 + 주요 이슈 1~2개 → "이 구조로 진행할까요?" 승인
- 최종 전: 전체 피라미드 visual 요약 + "struct-docs/에 저장할까요?" 확인
- 출력: 개조식 + visual 필수

**Autonomous Spec (주요 요구)**
- Prior 충분히 유사 → 자동 주입 (한 줄 요약만)
- 전체 6-Step + Critique + Reverse Structuring 내부 수행 (중간 판단 생략)
- 결과: 핵심 주장 1문장 + Level 1 bullets + 간단 visual + "저장 완료"만

**현재 구현**
- Orchestrator: Step 0.5 모드 감지 + "## Mode" 전달 (키워드 기반)
- thinking.md:
  - "Mode Handling"에 "collaborative: 단계별 주요 판단 공개, 사용자 확인 요청 (CS 후, GPS 후, Pyramid 후, MECE/Critique 후), 설명 보강, visual 미리보기"
  - "autonomous: 전체 6-Step + Critique 내부 수행, 최소 요약 + 결과만"
- User-Facing Output Format에 모드별 차이 간단 언급
- Pyramid Data 블록은 잘 정의됨

**Gap (구체)**
- Collaborative: "CS 후 범주 bullet 제시 + 확인", "GPS 후 diagram 확인", "MECE 후 이슈 공개 + 승인" 같은 **실제 질문/순서**가 프롬프트에 없음. "단계별 확인"만 선언.
- Autonomous: "중간 Self-Check 생략"이나 "무엇을 최소화할지" 명시 없음.
- 출력 스타일: visual 강제는 있지만, "collaborative일 때 더 풍부한 diagram 설명" 지침 부족.
- 모드 감지: 이전 대화 맥락(반복 호출) 고려 로직 없음.

**추천 (프롬프트에 바로 추가 가능)**
thinking.md에 다음 섹션 추가:

```
## S01 Mode별 상세 행동 (P0)
### Collaborative
- Prior 후: "이 prior (coreClaim + Level 1 3줄 요약, 유사도 XX%)를 참고할까요?" + 요약 제시 → 승인 대기
- CS 후: 주요 범주 bullet 3~5개 제시 → "이 축들이 맞습니까? 누락된 관점 있나요?" 확인
- GPS 후: ASCII/Mermaid diagram 제시 → "이 구조가 가장 유용한가요?" 확인
- Pyramid 후: coreClaim 초안 제시 → "이 한 문장이 전체를 대표합니까?" 확인
- MECE + Critique 후: "결과: passed/partial" + 주요 이슈 1~2개 공개 → "이 구조로 진행할까요?" 승인
- 최종 전: 전체 피라미드 visual 요약 + "struct-docs/01-thinking/xxx.md에 저장할까요?" 확인

### Autonomous
- Prior 충분히 유사 → "관련 prior 자동 적용 (한 줄)"만 언급하고 진행
- 전체 6-Step + Critique + Reverse Structuring 내부 수행 (중간 Self-Check 등 생략)
- 최종 결과만:
  - 핵심 주장 1문장
  - Level 1 bullets (3~5개)
  - 간단 visual (ASCII 또는 Mermaid)
  - "struct-docs/01-thinking/xxx.md에 저장했습니다"
```

---

### S02: Think → Write

**Collaborative Spec (주요 요구)**
- Orchestrator: prior 발견 → "이 pyramid 기반으로 작성할까요?" + source + Level 1 요약 제시
- Writing Agent:
  - coreClaim: "그대로 4.1에 쓸까요? 수정할까요?" 확인
  - Level 1: 각 근거를 **bullet 먼저** 보여주고 "이 bullet을 narrative로 확장할까요, 그대로 둘까요?" 논의
  - Situation/Complication: 초안 후 "정확합니까?" 확인
  - Answer 완성 전: MECE + Critique 요약 공개 → "이 문서로 저장할까요?" 최종 승인
- 출력: bullets + 표 위주 + "이 pyramid 기반" 투명 표기

**Autonomous Spec (주요 요구)**
- Prior 자동 주입
- 내부 Pyramid Consumption 적용
- 결과에 "변경 요약 (예: coreClaim 95% 유지, Level 1 3개 중 1개 보강)" + 파일 경로만

**현재 구현**
- Orchestrator: Mode 전달 + Prior 주입 로직 양호
- writing.md:
  - "Mode Handling": "collaborative: coreClaim/Level1 사용 시 사용자 확인 요청, 단계별 설명 보강"
  - "autonomous: Pyramid Consumption 자동, 변경 요약만"
  - Pyramid Consumption 규칙 자체는 상세 (coreClaim 재사용, Level 1 bullet 중심 등)

**Gap (구체)**
- Collaborative: "Level 1 bullet 먼저 제시 후 논의", "Situation/Complication 확인", "MECE 미리 공유 + 승인" 순서가 프롬프트에 없음.
- Autonomous: "변경 요약" 예시 문구가 실제 출력 요구사항에 없음.
- 출력 스타일: bullet 중심은 잘 되어 있지만, 모드별 차등(collaborative일 때 더 많은 설명)이 약함.

**추천**
writing.md Pyramid Consumption 섹션에 모드별 분기 추가:

```
## S02 Mode별 상세 행동 (P0)
### Collaborative
- Orchestrator: "이 pyramid (coreClaim + Level 1 요약)를 기반으로 [적합한 템플릿] 작성할까요? (source: ...)" 확인
- Writing:
  - coreClaim: "이 문장을 4.1 핵심 주장으로 그대로 쓸까요? 약간 수정할까요?" 확인
  - Level 1: 각 근거를 **bullet으로 먼저** 제시 → "이 bullet을 narrative로 확장할까요, 그대로 둘까요?" 논의
  - Situation/Complication: 초안 후 "이 배경 설명이 정확합니까?" 확인
  - Answer 완성 전: MECE + Critique 요약 공개 → "이 문서로 저장할까요?" 최종 승인
- 출력에 "이 pyramid 기반" 투명 표기 + bullets + 표 위주

### Autonomous
- Prior 자동 주입
- Pyramid Consumption 내부 적용
- 최종 출력에만:
  - "변경 요약: coreClaim 95% 유지, Level 1 3개 중 1개 보강"
  - Executive Summary (bullet 중심)
  - Answer (Level 1 bullets + 최소 narrative)
  - "struct-docs/02-writing/xxx.md 저장 완료 (prior 기반)"
```

---

### S05: 반복 정교화

**Collaborative Spec (주요 요구)**
- Orchestrator: "이전 pyramid 기반으로 증분 개선할까요?" + 이전 주요 차이 3줄 제시
- 범위 협의: "리스크 추가 / 반론 보강 / 데이터 업데이트" 중 사용자 선택
- 변경 후: "전후 비교" 표 제시 + "coreClaim 달라졌습니까?" 확인
- 저장: "새 파일로 저장할까요? (이전 버전 유지)" 승인

**Autonomous Spec (주요 요구)**
- 최근 prior 자동 선택
- 증분 개선 내부 수행 (무엇을 바꿀지 자동 판단)
- 결과: "주요 변경 bullet 3~5개" + "새 버전 저장 완료"
- "비교 원하시면 말씀해주세요" 한 줄만

**현재 구현**
- Orchestrator: 모드 감지 있음
- thinking.md / writing.md: "증분 개선" 모드 언급은 있지만 위 구체 흐름(범위 협의, 전후 비교 표, 저장 승인)은 없음

**Gap (구체)**
- Collaborative의 "범위 협의 + 선택" 과정과 "전후 비교 표" 생성 지침이 전혀 없음.
- Autonomous에서 "증분 시 무엇을 바꿀지 자동 판단" 로직이 선언적.
- 저장 시 "이전 버전 유지" 옵션 확인 없음.

**추천**
thinking.md와 writing.md에 공통으로 추가:

```
## S05 Mode별 상세 행동 (P0)
### Collaborative
- "이전 pyramid 기반으로 증분 개선할까요?" + 이전 주요 차이 3줄 제시
- "어떤 부분 집중? (1) 리스크 추가 (2) 반론 보강 (3) 데이터 업데이트" — 사용자 선택
- 변경 후: "전후 비교" 표 제시 + "이 수정으로 coreClaim 달라졌습니까?" 확인
- 저장: "새 파일로 저장할까요? (이전 버전 유지)" 승인

### Autonomous
- 최근 prior 자동 선택
- 증분 개선 내부 (무엇을 바꿀지 자동 판단)
- 결과: "주요 변경 bullet 3~5개" + "struct-docs/에 새 버전 저장 완료"
- 비교 원하면 후속 요청 유도
```

---

### S07: 모호 입력 / 역방향 입력 + Override

**Collaborative Spec (주요 요구)**
- 신호 감지 즉시: "모호하거나 이전 prior 무시하라는 신호 있습니다. 어떻게 할까요?" + 옵션 제시
- Thinking: "이 가정들을 세웠습니다" (bullet 3~4개) → "괜찮습니까?" 확인
- 구조화 후: "reverse structuring입니다" 설명 + "이 방향으로?" 승인
- 최종 전: 가정 목록 + visual + 저장 확인

**Autonomous Spec (주요 요구)**
- 신호 강하면 즉시 raw 전환 (prior 완전 배제)
- 내부 최소 가정 설정
- 결과에 한 줄: "모호한 입력으로 인해 다음 가정들을 세웠습니다"
- 상세 설명 최소, 바로 결과 반환

**현재 구현**
- Orchestrator: override 신호 파싱 (use-prior:false, "이전 생각 무시" 등) 잘 되어 있음
- thinking.md: Mode Handling에 override/모호 언급
- 하지만 위 구체 흐름(가정 bullet 제시 + 확인, visual + 저장 확인) 없음

**Gap (구체)**
- Collaborative: "가정 bullet 3~4개 제시 후 확인"과 "visual 요약 + 승인"이 프롬프트에 없음.
- Autonomous: "어떤 가정을 세웠는지"를 결과에 어떻게 기록할지 구체적 지침 없음.
- 모드 전환 지원 (중간에 "이제 자율로") 로직 미비.

**추천**
thinking.md에 S07 섹션 추가:

```
## S07 Mode별 상세 행동 (P0)
### Collaborative
- 신호 감지 즉시: "이 입력이 모호하거나 이전 prior 무시 신호가 있습니다. 어떻게 할까요?" + 옵션 제시
- "이 입력의 모호한 부분을 다음과 같이 해석했습니다" (bullet 3~4개 가정) → "괜찮습니까?" 확인
- 구조화 후: "이 구조는 reverse structuring입니다" + "이 방향으로 진행할까요?" 승인
- 최종 전: 가정 목록 + visual + "저장할까요?" 확인

### Autonomous
- 신호 강하면 즉시 raw 모드 (prior 완전 배제)
- 내부 최소 가정 설정
- 결과에 한 줄: "모호한 입력으로 인해 다음 가정들을 세웠습니다"
- 상세 설명 없이 바로 결과 반환
```

---

### P0 종합 추천 (반영 완료)
1. ✅ **thinking.md**와 **writing.md**에 S01/S02/S05/S07 "P0 Mode별 상세 행동" 섹션 추가 완료
2. ✅ Orchestrator Step 0.5 + 모드 전달 + S06 상세 행동 반영 완료
3. ✅ Default 스타일 (개조식 + visual) 문서화 완료
4. Collaborative 확인 항목은 모드 섹션에서 수행하도록 설계

**이 Gap Analysis 작성 후 실제 프롬프트 반영이 완료되었습니다.** (추가 검증 추천)

---

**중복 Gap Analysis 정리 (2026-06-19)**

상세 Gap Analysis + 1:1 Spec vs 구현 비교 + 추천 문구는 위 "## Gap Analysis — P0 Only" 섹션에 이미 기록되어 있습니다.

이후 P0 Mode별 상세 행동이 agents에 주입 완료. 중복된 하단 블록은 정리했습니다. (실제 테스트 및 usage 치트시트 보강이 다음 관심사)
- 최종 전: 전체 피라미드 visual 요약 + "struct-docs/에 저장할까요?" 확인
- 출력: 개조식 + visual 필수

**Autonomous Spec (주요 요구)**
- Prior 충분히 유사 → 자동 주입 (한 줄 요약만)
- 전체 6-Step + Critique + Reverse Structuring 내부 수행 (중간 판단 생략)
- 결과: 핵심 주장 1문장 + Level 1 bullets + 간단 visual + "저장 완료"만

**현재 구현**
- Orchestrator: Step 0.5 모드 감지 + "## Mode" 전달 (키워드 기반)
- thinking.md:
  - "Mode Handling"에 "collaborative: 단계별 주요 판단 공개, 사용자 확인 요청 (CS 후, GPS 후, Pyramid 후, MECE/Critique 후), 설명 보강, visual 미리보기"
  - "autonomous: 전체 6-Step + Critique 내부 수행, 최소 요약 + 결과만"
- User-Facing Output Format에 모드별 차이 간단 언급
- Pyramid Data 블록은 잘 정의됨

**Gap (구체)**
- Collaborative: "CS 후 범주 bullet 제시 + 확인", "GPS 후 diagram 확인", "MECE 후 이슈 공개 + 승인" 같은 **실제 질문/순서**가 프롬프트에 없음. "단계별 확인"만 선언.
- Autonomous: "중간 Self-Check 생략"이나 "무엇을 최소화할지" 명시 없음.
- 출력 스타일: visual 강제는 있지만, "collaborative일 때 더 풍부한 diagram 설명" 지침 부족.
- 모드 감지: 이전 대화 맥락(반복 호출) 고려 로직 없음.

**추천 (프롬프트에 바로 추가 가능)**
thinking.md에 다음 섹션 추가:

```
## S01 Mode별 상세 행동 (P0)
### Collaborative
- Prior 후: "이 prior (coreClaim + Level 1 3줄 요약, 유사도 XX%)를 참고할까요?" + 요약 제시 → 승인 대기
- CS 후: 주요 범주 bullet 3~5개 제시 → "이 축들이 맞습니까? 누락된 관점 있나요?" 확인
- GPS 후: ASCII/Mermaid diagram 제시 → "이 구조가 가장 유용한가요?" 확인
- Pyramid 후: coreClaim 초안 제시 → "이 한 문장이 전체를 대표합니까?" 확인
- MECE + Critique 후: "결과: passed/partial" + 주요 이슈 1~2개 공개 → "이 구조로 진행할까요?" 승인
- 최종 전: 전체 피라미드 visual 요약 + "struct-docs/01-thinking/xxx.md에 저장할까요?" 확인

### Autonomous
- Prior 충분히 유사 → "관련 prior 자동 적용 (한 줄)"만 언급하고 진행
- 전체 6-Step + Critique + Reverse Structuring 내부 수행 (중간 Self-Check 등 생략)
- 최종 결과만:
  - 핵심 주장 1문장
  - Level 1 bullets (3~5개)
  - 간단 visual (ASCII 또는 Mermaid)
  - "struct-docs/01-thinking/xxx.md에 저장했습니다"
```

---

### S02: Think → Write

**Collaborative Spec (주요 요구)**
- Orchestrator: prior 발견 → "이 pyramid 기반으로 작성할까요?" + source + Level 1 요약 제시
- Writing Agent:
  - coreClaim: "그대로 4.1에 쓸까요? 수정할까요?" 확인
  - Level 1: 각 근거를 **bullet 먼저** 보여주고 "이 bullet을 narrative로 확장할까요, 그대로 둘까요?" 논의
  - Situation/Complication: 초안 후 "정확합니까?" 확인
  - Answer 완성 전: MECE + Critique 요약 공개 → "이 문서로 저장할까요?" 최종 승인
- 출력: bullets + 표 위주 + "이 pyramid 기반" 투명 표기

**Autonomous Spec (주요 요구)**
- Prior 자동 주입
- 내부 Pyramid Consumption 적용
- 결과에 "변경 요약 (예: coreClaim 95% 유지, Level 1 3개 중 1개 보강)" + 파일 경로만

**현재 구현**
- Orchestrator: Mode 전달 + Prior 주입 로직 양호
- writing.md:
  - "Mode Handling": "collaborative: coreClaim/Level1 사용 시 사용자 확인 요청, 단계별 설명 보강"
  - "autonomous: Pyramid Consumption 자동, 변경 요약만"
  - Pyramid Consumption 규칙 자체는 상세 (coreClaim 재사용, Level 1 bullet 중심 등)

**Gap (구체)**
- Collaborative: "Level 1 bullet 먼저 제시 후 논의", "Situation/Complication 확인", "MECE 미리 공유 + 승인" 순서가 프롬프트에 없음.
- Autonomous: "변경 요약" 예시 문구가 실제 출력 요구사항에 없음.
- 출력 스타일: bullet 중심은 잘 되어 있지만, 모드별 차등(collaborative일 때 더 많은 설명)이 약함.

**추천**
writing.md Pyramid Consumption 섹션에 모드별 분기 추가:

```
## S02 Mode별 상세 행동 (P0)
### Collaborative
- Orchestrator: "이 pyramid (coreClaim + Level 1 요약)를 기반으로 [적합한 템플릿] 작성할까요? (source: ...)" 확인
- Writing:
  - coreClaim: "이 문장을 4.1 핵심 주장으로 그대로 쓸까요? 약간 수정할까요?" 확인
  - Level 1: 각 근거를 **bullet으로 먼저** 제시 → "이 bullet을 narrative로 확장할까요, 그대로 둘까요?" 논의
  - Situation/Complication: 초안 후 "이 배경 설명이 정확합니까?" 확인
  - Answer 완성 전: MECE + Critique 요약 공개 → "이 문서로 저장할까요?" 최종 승인
- 출력에 "이 pyramid 기반" 투명 표기 + bullets + 표 위주

### Autonomous
- Prior 자동 주입
- Pyramid Consumption 내부 적용
- 최종 출력에만:
  - "변경 요약: coreClaim 95% 유지, Level 1 3개 중 1개 보강"
  - Executive Summary (bullet 중심)
  - Answer (Level 1 bullets + 최소 narrative)
  - "struct-docs/02-writing/xxx.md 저장 완료 (prior 기반)"
```

---

### S05: 반복 정교화

**Collaborative Spec (주요 요구)**
- Orchestrator: "이전 pyramid 기반으로 증분 개선할까요?" + 이전 주요 차이 3줄 제시
- 범위 협의: "리스크 추가 / 반론 보강 / 데이터 업데이트" 중 사용자 선택
- 변경 후: "전후 비교" 표 제시 + "coreClaim 달라졌습니까?" 확인
- 저장: "새 파일로 저장할까요? (이전 버전 유지)" 승인

**Autonomous Spec (주요 요구)**
- 최근 prior 자동 선택
- 증분 개선 내부 수행 (무엇을 바꿀지 자동 판단)
- 결과: "주요 변경 bullet 3~5개" + "새 버전 저장 완료"
- "비교 원하시면 말씀해주세요" 한 줄만

**현재 구현**
- Orchestrator: 모드 감지 있음
- thinking.md / writing.md: "증분 개선" 모드 언급은 있지만 위 구체 흐름(범위 협의, 전후 비교 표, 저장 승인)은 없음

**Gap (구체)**
- Collaborative의 "범위 협의 + 선택" 과정과 "전후 비교 표" 생성 지침이 전혀 없음.
- Autonomous에서 "증분 시 무엇을 바꿀지 자동 판단" 로직이 선언적.
- 저장 시 "이전 버전 유지" 옵션 확인 없음.

**추천**
thinking.md와 writing.md에 공통으로 추가:

```
## S05 Mode별 상세 행동 (P0)
### Collaborative
- "이전 pyramid 기반으로 증분 개선할까요?" + 이전 주요 차이 3줄 제시
- "어떤 부분 집중? (1) 리스크 추가 (2) 반론 보강 (3) 데이터 업데이트" — 사용자 선택
- 변경 후: "전후 비교" 표 제시 + "이 수정으로 coreClaim 달라졌습니까?" 확인
- 저장: "새 파일로 저장할까요? (이전 버전 유지)" 승인

### Autonomous
- 최근 prior 자동 선택
- 증분 개선 내부 (무엇을 바꿀지 자동 판단)
- 결과: "주요 변경 bullet 3~5개" + "struct-docs/에 새 버전 저장 완료"
- 비교 원하면 후속 요청 유도
```

---

### S07: 모호 입력 / 역방향 입력 + Override

**Collaborative Spec (주요 요구)**
- 신호 감지 즉시: "모호하거나 이전 prior 무시하라는 신호 있습니다. 어떻게 할까요?" + 옵션 제시
- Thinking: "이 가정들을 세웠습니다" (bullet 3~4개) → "괜찮습니까?" 확인
- 구조화 후: "reverse structuring입니다" 설명 + "이 방향으로?" 승인
- 최종 전: 가정 목록 + visual + 저장 확인

**Autonomous Spec (주요 요구)**
- 신호 강하면 즉시 raw 전환 (prior 완전 배제)
- 내부 최소 가정 설정
- 결과에 한 줄: "모호한 입력으로 인해 다음 가정들을 세웠습니다"
- 상세 설명 최소, 바로 결과 반환

**현재 구현**
- Orchestrator: override 신호 파싱 (use-prior:false, "이전 생각 무시" 등) 잘 되어 있음
- thinking.md: Mode Handling에 override/모호 언급
- 하지만 위 구체 흐름(가정 bullet 제시 + 확인, visual + 저장 확인) 없음

**Gap (구체)**
- Collaborative: "가정 bullet 3~4개 제시 후 확인"과 "visual 요약 + 승인"이 프롬프트에 없음.
- Autonomous: "어떤 가정을 세웠는지"를 결과에 어떻게 기록할지 구체적 지침 없음.
- 모드 전환 지원 (중간에 "이제 자율로") 로직 미비.

**추천**
thinking.md에 S07 섹션 추가:

```
## S07 Mode별 상세 행동 (P0)
### Collaborative
- 신호 감지 즉시: "이 입력이 모호하거나 이전 prior 무시 신호가 있습니다. 어떻게 할까요?" + 옵션 제시
- "이 입력의 모호한 부분을 다음과 같이 해석했습니다" (bullet 3~4개 가정) → "괜찮습니까?" 확인
- 구조화 후: "이 구조는 reverse structuring입니다" + "이 방향으로 진행할까요?" 승인
- 최종 전: 가정 목록 + visual + "저장할까요?" 확인

### Autonomous
- 신호 강하면 즉시 raw 모드 (prior 완전 배제)
- 내부 최소 가정 설정
- 결과에 한 줄: "모호한 입력으로 인해 다음 가정들을 세웠습니다"
- 상세 설명 없이 바로 결과 반환
```

---

### P0 종합 추천 (반영 완료)
1. ✅ **thinking.md**와 **writing.md**에 S01/S02/S05/S07 "P0 Mode별 상세 행동" 섹션 추가 완료
2. ✅ Orchestrator Step 0.5 + 모드 전달 + S06 상세 행동 반영 완료
3. ✅ Default 스타일 (개조식 + visual) 문서화 완료
4. Collaborative 확인 항목은 모드 섹션에서 수행하도록 설계

**이 Gap Analysis 작성 후 실제 프롬프트 반영이 완료되었습니다.** (추가 검증 추천)

| 영역                  | Collaborative (참여형) | Autonomous (자율형) | 전체 우선순위 |
|-----------------------|------------------------|---------------------|---------------|
| Orchestrator 모드 감지 | 중 (키워드 기반)      | 중                 | 높           |
| 모드별 확인/자동 로직 전달 | 낮~중                 | 낮                 | **최고**     |
| Specialists 상세 행동 | 낮 (기본 언급만)      | 낮                 | **최고**     |
| 출력 스타일 강제       | 중 (일부 언급)        | 중                 | 중           |
| 품질 하한선            | 높                     | 중                 | 낮           |

**전반 결론**: Orchestrator에 기본 모드 감지/전달은 들어갔으나, **spec의 세부 확인 질문과 행동 지침**이 specialists 프롬프트에 아직 구체적으로 반영되지 않음. "Mode Handling"이 선언적 수준.

### 2. 시나리오별 상세 Gap (1:1 매핑)

#### S01: Standalone Think
**Spec 요구 (Collaborative)**
- Prior 확인 (coreClaim + Level1 3줄 + 유사도)
- CS 후 범주 확인
- GPS 후 구조 확인 (diagram)
- Pyramid 후 coreClaim 확인
- MECE/Critique 후 이슈 공개 + 승인
- 최종 visual + 저장 확인

**Spec 요구 (Autonomous)**
- Prior 자동 주입 (한 줄 요약)
- 전체 6-Step + Critique 내부
- 최소 요약 (1문장 + bullets + visual) + 저장 경로만

**현재 구현**
- Orchestrator: Step 0.5 모드 감지 + "## Mode" 전달
- thinking.md: "Mode Handling"에 단계별 확인/자동 언급 (좋은 시작)
- 하지만 구체적 질문 템플릿("이 범주들이 맞습니까?")은 없음

**Gap**
- Collaborative 확인 질문이 너무 추상적임
- Autonomous에서 "무엇을 생략할지" (e.g. 중간 Self-Check 생략) 미지정
- 출력 스타일: visual 강제는 있지만 모드 차등이 약함

**추천**
thinking.md에 다음 추가:
```
## Mode별 행동 (S01)
Collaborative:
- Prior 후: "이 prior를 참고할까요? (coreClaim + Level 1 3줄 요약)" 확인
- CS 후: 범주 bullet 제시 + "이 축들이 맞습니까?" 확인
...
Autonomous:
- 전체 내부 수행
- 최종: "coreClaim: ... \n Level 1: \n - ...\n (visual: ASCII/Mermaid)"
```

(중복된 하단 Gap 상세 분석은 위 "## Gap Analysis — P0 Only (S01, S02, S05, S07) — 정교 버전" 섹션 참조. 중복 정리 완료)

#### S03: Standalone Solve (P1 — 상세 Gap은 생략)
**Spec 요구 (Collaborative)**
- Prior를 framing에 참고할지 확인 (Level 1 3줄)
- 문제 축 bullet 제시 + 확인
- 가설을 표로 보여주고 MECE 논의
- Root Cause + 인과 모델 visual 확인
- 해결안 테이블 → 우선순위 조정 확인
- MECE/Critique + 저장 승인

**Spec 요구 (Autonomous)**
- Prior 자동 참고
- 전체 자동 (3+ 가설 + 5 Whys + 인과 + 테이블 + 플랜)
- "가설 3개 + 우선순위 1위 Y" 요약 표 + 저장 완료

**현재 구현**
- problem-solving.md: "framing/가설 단계 확인" 언급
- 인과 모델 출력 형식은 있음

(P1 시나리오 상세 Gap 생략 — 위 P0 섹션 패턴과 동일하게 agents에 Mode Handling 반영됨) 

#### S04: Prior 기반 Express (P1)
(P1 — 상세 생략)
- 슬라이드 구성 원칙은 있음

**Gap**
- Collaborative에서 "미리보기 + 특정 수정" 루프가 약함
- Autonomous에서 format별 자동 동작이 선언적

**추천**
expression.md에 format별 모드 분기 추가.

#### S05~S07 (공통 패턴)
- Spec의 세부 (3줄 제시, 전후 비교 표, 가정 목록 + visual, 승인)는 아직 프롬프트에 "구체 템플릿" 형태로 들어가지 않음
- Orchestrator의 모드 감지가 키워드 수준이라 맥락(이전 대화) 고려가 약함

### 3. 공통 Gap (전 시나리오)
- **Mode별 상세 체크리스트 부재**: 현재는 "더 많은 확인" 같은 선언적 문구. Spec의 실제 질문/순서를 프롬프트에 복붙 수준으로 넣어야 함.
- **출력 스타일**: "개조식 + visual"이 권고 수준. "항상 bullet + 최소 1개 테이블/Mermaid 포함"처럼 강제할 필요.
- **투명성**: "무엇을 확인했는지"를 결과에 기록하는 지침이 약함 (특히 Collaborative).
- **모드 전환 지원**: 중간에 "이제 자율로 마무리해" 같은 전환 로직이 프롬프트에 없음.

### 4. 우선순위 추천 (Gap 후)
**P0 (즉시)**
1. 각 specialist에 "Mode별 상세 행동 체크리스트" (이 문서의 시나리오별 내용 기반) 추가
2. Orchestrator 모드 감지 로직 강화 (자연어 + 이전 맥락)

**P1**
- 출력 요구사항에 "기본 스타일" 강제 문구 + 예시
- Collaborative 모드에서 "확인한 항목 요약"을 결과에 자동 추가하는 로직

**P2**
- 테스트 입력으로 실제 모드 차이 검증

(상세 Gap Analysis는 상단 섹션 참조 — P0 구현 완료 상태)

## 공통 패턴 & 원칙 (모든 시나리오에 적용)

### Prior Reuse 원칙 (재사용 vs 독립)
- Writing: coreClaim/Level1을 **Answer의 기반**으로 적극 재사용 (Pyramid Consumption)
- Solve: Prior를 **문제 framing과 가설 생성 품질** 향상에만 사용 (직접 주장 이식 금지)
- Express: coreClaim = 핵심 메시지, Level1 = 슬라이드/근거 단위로 매핑
- Standalone Think: Prior는 참고 수준 (강제 아님)

### Memory & File 규칙
- `.struct-memory.json`은 라우팅과 선택을 위한 인덱스 역할
- 실제 내용은 `struct-docs/...` 파일을 Read로 로드
- maxItemsPerArray 초과 시 오래된 항목 제거

### 투명성
- Prior를 사용한 경우, 가능하면 최종 출력에 언급
- MECE 검증 + Critique Pass는 prior 사용 여부와 관계없이 항상 수행 + 기록

### Override 신호 (우선순위)
1. 명시적 옵션 (`use-prior: false`)
2. 사용자 자연어 ("이전 생각 무시", "raw로", "새로 분석해")
3. 자동 판단 (기본)

### 모드 선택 신호
- Collaborative 모드 신호: "천천히", "자세히", "같이 하자", "검토해줘", "확인하면서", "내가 참여할게"
- Autonomous 모드 신호: "빠르게", "지금 당장", "초안만", "한 번에", "자율로", "그냥 해줘"
- 기본값: Collaborative (안전 우선)
- Orchestrator는 모드를 가장 먼저 파싱하여 specialists에게 "이 모드로 동작하라"는 명확한 지시를 전달해야 함.

### 출력 스타일 (두 모드 공통 기본)
- 기본: 개조식 (bullet) + 표/다이어그램 (ASCII 또는 Mermaid)으로 직관적 표현
- 참여형 (옵션 1): 더 많은 설명 bullet + visual 미리보기 + 확인
- 자율형 (옵션 2): 간결 bullet + 최소 요약 visual
- 산출물 검토 시 "narrative로", "더 시각적으로" 등 요청으로 즉시 변경 가능

### Default Output Style (기본 출력 스타일 — 고정 원칙)
사용자 선호에 따라 **모든 산출물의 default**는 다음과 같다:

- **개조식 표현 우선**
  - bullet points, sub-bullets, numbered lists를 주된 표현 방식으로 사용
  - 긴 narrative 문단은 최소화 (필요한 경우에만 보조적으로 사용)

- **직관적 시각 자료 적극 활용**
  - 표(tables): 비교, MECE 검증, GPS 구조, 우선순위, 가설/해결안 등
  - 다이어그램: ASCII diagram 또는 Mermaid syntax (피라미드 계층, 흐름, 구조) — Mermaid는 많은 도구에서 이미지처럼 렌더링됨
  - 계층과 관계를 한눈에 파악할 수 있도록 시각화

- **적용 범위**
  - /struct-think: 피라미드 구조 → bullets + 표 + diagram
  - /struct-write: 적합한 템플릿 적용 문서 (SCQA/IAEJ/목적방침 등) → 각 섹션 bullets + embedded visuals/tables (템플릿 선택 로직 포함)
  - /struct-solve: 분석 결과 → 테이블 중심 + lists
  - /struct-express: 슬라이드/메모 → bullet 중심 + 시각적 구분

**변경 가능성 (중요)**
- 위 스타일은 **default**일 뿐
- 구체적인 스타일 세부사항(표 형식, 다이어그램 종류, bullet 깊이, narrative 사용 정도 등)은 사용자가 점진적으로 적용 요청하면서 발전시킨다.
- 산출물 생성 후 검토 시 언제든 변경/보완 요청 가능
  - 예: "narrative style로 바꿔줘", "더 문단식으로 써줘", "도표 없이 텍스트만", "Mermaid 대신 ASCII로"
- 특히 **Option 1 (인간 참여형)** 모드에서는 검토 과정에서 자연스럽게 조정
- **Option 2 (자율형)** 모드에서도 기본은 유지하되, 결과물 받은 후 후속 지시로 쉽게 변경 가능

이 스타일은 "직관적으로 이해하기 쉽게"라는 사용자의 핵심 선호를 반영한다. 구체적 규칙은 실제 사용 피드백을 통해 점진적으로 정교화한다.

---

### P0 Mode 테스트 케이스 (Collaborative vs Autonomous 검증)

아래는 P0 시나리오별 실제 호출 예시입니다. (기본 스타일: 개조식 + visual)

**S01 Think**
- Collaborative: `/struct-think 자세히 같이 해줘 [긴 브레인덤프 입력]`
  - 기대: Prior 확인 (coreClaim + Level1 3줄) → CS 범주 확인 → GPS diagram 확인 → Pyramid coreClaim 확인 → MECE/Critique 이슈 공개 → visual + 저장 확인
- Autonomous: `/struct-think 빠르게 [긴 브레인덤프 입력]`
  - 기대: 자동 전체 → 1문장 + bullets + 간단 visual + "struct-docs/01-thinking/xxx.md 저장 완료"

**S02 Write**
- Collaborative: `/struct-write 천천히 같이 [주제]`
  - 기대: Prior 확인 + source → coreClaim 확인 → Level1 bullet 먼저 제시 + 확장 논의 → Situation 확인 → MECE 미리 공유 → 최종 승인
- Autonomous: `/struct-write 빠르게 [주제]`
  - 기대: Prior 자동 → pyramid 분석 후 템플릿 선택 (SCQA 또는 다른) → full 문서 → "변경 요약" + "선택된 템플릿: XXX (이유: YYY)" + 저장 경로만

**S05 Iterative**
- Collaborative: `/struct-think 이전처럼 자세히 같이 [주제]`
  - 기대: 이전 pyramid 제시 → 범위 선택 (리스크/반론/데이터) → 전후 비교 표 → coreClaim 변화 확인 → 새 파일 저장 승인
- Autonomous: `/struct-write 빠르게 증분 [주제]`
  - 기대: 자동 증분 → 주요 변경 bullet 3~5개 + 새 버전 저장 완료

**S07 Override**
- Collaborative: `/struct-think 자세히 같이 (이전 무시하면서) [모호한 긴 입력]`
  - 기대: 신호 확인 → 가정 bullet 제시 + 확인 → reverse 설명 + 승인 → 가정 목록 + visual + 저장 확인
- Autonomous: `/struct-think 빠르게 override [모호한 긴 입력]`
  - 기대: 즉시 raw → 최소 가정 → 한 줄 주석 + 결과 반환

**검증 포인트**
- Collaborative: 실제로 확인 질문이 나오고 사용자가 개입할 수 있는가?
- Autonomous: 확인 없이 전체 결과가 바로 나오는가?
- 두 모드 모두 기본 스타일(개조식 + visual)이 유지되는가?
- Prior, MECE, Critique가 모드에 관계없이 수행되는가?

실제 환경에서 위 호출을 해보고 결과가 이 문서의 spec과 일치하는지 확인하세요. 차이가 나면 해당 프롬프트의 Mode별 섹션을 조정하세요.

---

## 이 명세를 사용하는 방법 (개발/검증용)

1. 새로운 기능이나 프롬프트 변경 전, 반드시 이 문서의 해당 시나리오를 읽는다.
2. 변경 시 **반드시 두 모드(인간 참여형 / 자율 실행형)** 모두 고려하여 동작을 정의한다.
3. 변경 후에는 최소 해당 시나리오의 Happy Path + 1개 Edge Case를 **두 모드 각각**으로 직접 실행하여 검증.
4. 실제 사용 후 새로운 패턴이 발견되면 이 문서에 시나리오를 추가한다.
5. "이 기능은 어떤 시나리오를 지원하기 위한 것인가? 그리고 어떤 모드에서 주로 쓰일 것인가?"를 항상 질문한다.
6. 출력 스타일은 기본(직관적 도표 + 개조식)을 유지하되, 구체적 세부사항은 사용자가 점진적으로 요청하면서 발전시킨다.

---

## 다음 단계 (이 문서 작성 후)

- [x] 두 가지 작업 모드 (인간 참여형 / 자율 실행형) 상세 행동 정의 + spec 내 표/시나리오 반영
- [x] Agent 프롬프트에 P0/P1 Mode별 상세 행동 반영 완료 (thinking/writing/orchestrator 등)
- [x] **Gap Analysis 정교 버전** 작성 + P0 추천 반영 완료
- [x] A 작업: struct-docs/usage/*.md 치트시트 보강 (모드 + 템플릿 + 호출 예시)
- [x] B 작업: docs/struct-usage-scenarios.md 정리 (현재 구현 상태 업데이트 + 중복 Gap 정리 + 다음 단계 갱신)
- [x] SKILL.md 4곳의 .minto-memory.json → .struct-memory.json 수정 (버그 방지)
- [ ] 실제 P0 시나리오 테스트 실행 (collaborative/autonomous + prior + 템플릿 선택 검증)
- [ ] 출력 스타일 세부사항은 사용자 점진적 요청에 따라 발전 (기본은 개조식 + visual 유지)

---

**문서 상태**: 2026-06-19 (A + B 작업 완료)
**마지막 수정**: 2026-06-19
- P0 Mode별 상세 행동 agents에 반영 완료
- 다중 템플릿 + 모드 + Prior 재사용 체계
- usage 치트시트 보강 (A)
- spec Gap/상태/다음 단계 정리 (B)
- SKILL.md memory 경로 버그 수정

**작성 배경**: "구체적인 시나리오 없이 agent를 만들어서 허점이 너무 많고 추상적"이라는 문제 인식에서 출발. + 사용자 다중 템플릿 + 두 모드 요구사항 반영.
