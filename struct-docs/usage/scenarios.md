---
tags: [struct, usage, scenarios, spec]
created: 2026-06-19
type: specification
priority: high
status: draft-for-review
---

# Struct Agent Team — Usage Scenarios Specification

> **목적**: 구체적인 사용자 사용 시나리오를 정의. 실제 사용 중심 spec 문서.
> 상세 내용은 `docs/struct-usage-scenarios.md` 참조.

**빠른 모드 안내**
- 참여형 (기본): "자세히 같이 해줘", "검토하면서"
- 자율형: "빠르게", "초안만"

두 모드 모두 기본 출력은 **개조식 + 표/다이어그램**.

---

## 주요 시나리오 (요약)
> **대상**: Orchestrator, specialists(Thinking/Writing/Problem-solving/Expression), Skills, Memory, Prior Reuse 로직 검증 및 개선.
> **원칙**: 시나리오 없이 기능/프롬프트를 만들지 않는다. 모든 주요 동작은 최소 1개 이상의 구체 시나리오에 의해 정당화되어야 한다.

## 개요

이 문서는 Minto Agent Team의 **실제 사용 맥락**을 시나리오 수준으로 상세히 정의한다.

기존 `think.md` / `write.md` 등은 명령어 치트시트 수준이었으며, 다음과 같은 문제를 가지고 있었다:
- "언제 쓰나"가 너무 추상적
- Chaining(think → write) 의 실제 동작 기대치 불명확
- Prior Thinking Reuse가 언제/어떻게 동작해야 하는지 기준 없음
- 입력 품질, 반복 작업, override 상황에 대한 처리 전략 부재
- Solve / Express가 거의 검증되지 않음

이 명세는 위 문제를 해결하기 위한 **재점검의 기반**이다.

---

## 우선순위 및 분류

| ID | 시나리오 | 우선순위 | 현재 실제 사용 빈도 | 핵심 위험 |
|----|----------|----------|---------------------|----------|
| S01 | Standalone Think (브레인덤프 구조화) | P0 | 높음 (실제 주 사용) | coreClaim 품질, MECE, reverse structuring |
| S02 | Think → Write 체이닝 (보고서 생성) | P0 | 높음 | Prior 주입 정확도, 재사용 vs 재작성 균형 |
| S03 | Standalone /solve (문제 분석) | P1 | 낮음 | 가설 MECE, Root Cause 깊이, Prior framing 활용 |
| S04 | Prior 기반 Express (슬라이드/메모) | P1 | 매우 낮음 | Level 1 → 슬라이드 매핑 품질, 형식 충실도 |
| S05 | 반복 정교화 (Iterative) | P0 | 중간 | Memory/파일 참조, 증분 개선 vs 전체 재구성 |
| S06 | Cross-session Prior Reuse | P1 | 중간 | Prior 선택 정확도 (유사도), 오래된 prior 처리 |
| S07 | 모호/역방향 입력 + Override | P0 | 빈번 | 자동 판단 실패, 사용자 명시적 지시 처리 |
| S08 | Think → Branch → Review Gate (Fidelity 검증) | P0 | 신규 | Fidelity 충실도, Regeneration Directives 품질, Orchestrator 재생성 제어 |

---

**모드 사용법** (참여형 기본)
- 참여형: "자세히 같이 해줘", "검토하면서 같이"
- 자율형: "빠르게", "초안만"

**상세 시나리오·Gap·테스트**: `../../docs/struct-usage-scenarios.md` 참조

### S08 (신규): Think → Branch → Review Gate (Compliance + Fidelity)
- 목적: 단계 완료 시점에 핵심 규칙 준수(Compliance) + 소비 충실도(Fidelity) 검증
- 감사 범위 (현재):
  - thinking: Non-negotiables, MECE 검증, Mandatory Critique Pass 수행 여부
  - writing: Pyramid Consumption 규칙 준수 여부
- Compliance 강도: Option A (Non-negotiables 매우 엄격 / 과정 증거 중간 / 품질은 관대)
- 기대: 증거 기반 판정 + 강제/권고 구분된 Regeneration Directives
- Orchestrator 책임: 단계 완료 후 review 호출 제어, force_rework 시 재호출 주도
- 검증 포인트: 산출물에 규칙 수행 흔적이 명확히 기록되었는지, prior 소비가 충실했는지

**치트시트**: `think.md`, `write.md`, `solve.md`, `express.md` (템플릿 선택 포함)
- 템플릿 상세: `struct-docs/templates/README.md` (영문 파일명: report-default, iaej-pattern, structure-event-response-pattern 등)
- **워크플로우 다이어그램**: [workflow.mmd](workflow.mmd)

---

## 상세 시나리오

### S01: Standalone Think — 복잡한 주제 브레인덤프 구조화

**User Goal**  
산재된 생각, 강의 내용, 시장 관찰, 아이디어를 **한 번에** 최고 품질의 Minto 피라미드로 구조화하고 싶다. 나중에 write/solve/express에서 재사용할 수 있는 견고한 Artifact를 얻고 싶다.

**Persona / Context**  
개발자 / 개인 투자자 / 전략가 / 지식 노동자. 한국 거시·산업·기업 분석을 자주 함. 입력은 800~3000자 분량의 메모, 강의 요약, 산발적 bullet이 섞여 있음.

**Invocation**  
`/think {긴 입력 전체}` 또는 `/struct-think ...`

**Input Profile**
- 길고 비구조화됨 (브레인덤프)
- 여러 주제가 섞여 있거나, 핵심 주장이 뒤에 나옴 (reverse)
- 데이터, 의견, 질문, 가정이 혼재
- "결론적으로 ... 해야 한다" 스타일이 자주 등장

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

**현재 구현 상태 (2026-06-19 기준, preliminary)**
- Thinking Agent의 Non-negotiables와 6-Step은 잘 정의되어 있음
- Pyramid Data 블록은 추가됨
- 하지만 "브레인덤프 품질"에 특화된 지침은 부족 (CS 단계에서 어떻게 요약할지)

---

### S02: Think → Write 체이닝 (가장 중요한 가치 흐름)

**User Goal**  
`/think`로 구조를 잡은 다음, **최소한의 중복 사고 없이** SCQA 보고서로 빠르게 변환하고 싶다. Prior pyramid의 coreClaim과 Level 1을 최대한 존중하면서도, narrative와 Situation/Complication/Question은 자연스럽게 보강되기를 원함.

**Persona / Context**  
투자 리포트, 전략 문서, 제안서를 자주 작성하는 사람. think 결과가 이미 있고, 같은 세션 또는 다음 세션에서 write를 호출.

**Invocation**  
1. `/think {주제}` 수행
2. (같은 세션 또는 이후) `/write {주제 또는 think와 유사한 지시}`

**Input Profile**
- write 입력은 think 입력과 동일하거나 약간 더 짧거나, "이걸 보고서로 만들어줘" 스타일

**Happy Path Flow (Prior Reuse 기대)**
1. Orchestrator: write 명령 인식
2. memory에서 relevant previousThought 검색 (키워드 + coreClaim 유사도)
3. `savedTo` 파일을 **Read** 도구로 실제 로드
4. `## Previous Thinking Pyramid` 블록을 정확한 형식으로 구성하여 Context에 주입
5. Writing Agent 호출
6. Writing Agent는 **Pyramid Consumption 규칙**을 따라:
   - coreClaim을 4.1 핵심 주장으로 우선 사용
   - Level 1을 4.2 근거 구조의 기반으로 매핑
   - MECE 검증 + Critique Pass는 **여전히 수행**하여 기록
7. SCQA 문서 저장 + memory previousDocuments 업데이트

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

**현재 구현 상태**
- Orchestrator에 Read + 주입 로직은 상세히 정의됨
- Writing Agent에 Pyramid Consumption 규칙은 작성됨
- 그러나 "Prior 선택 정확도"와 "부분 유효 prior 처리"에 대한 구체 지침 부족
- 실제 chaining 사례(현대차 Physical AI)는 존재하지만, 자동 선택이 항상 성공했는지 검증 부족

---

### S03: Standalone Solve — 문제 분석 및 해결안 도출

**User Goal**  
명확한 "문제"가 주어졌을 때, 감정적으로 접근하지 않고 Minto 피라미드로 문제를 구조화하고, 가설을 MECE하게 세우고, 근본원인을 파고, 실행 가능한 해결안을 우선순위와 함께 얻고 싶다.

**Persona / Context**  
팀 리더, 운영자, 컨설턴트 스타일. "왜 생산성이 떨어졌는가", "고객이 왜 이탈하는가" 같은 operational 문제.

**Invocation**  
`/solve {문제 설명 전체}`

**Input Profile**
- 문제 상황 + 일부 관찰 데이터가 있음
- "원인을 모르겠다" 또는 "대응 방안을 모르겠다"가 핵심
- 종종 "왜" 질문 형태

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

**현재 구현 상태**
- 문제 해결 Agent의 Phase 구조 (가설→RCA→해결안→액션)는 잘 정의
- Prior 활용 규칙("framing 용도로만")은 명시됨
- 하지만 실제 호출 사례 거의 없어서, 가설 MECE와 인과관계 모델의 실전 품질 미검증

---

### S04: Prior 기반 Express — 구조화된 내용을 발표/메모로 변환

**User Goal**  
이미 구조화된 think 또는 write 결과를 가지고, **형식만 바꿔서** 바로 발표용 슬라이드나 팀 공유용 1페이지 메모를 만들고 싶다. 다시 전체 thinking을 하지 않기를 원함.

**Persona / Context**  
회의에서 발표하거나, 상사/팀원에게 빠르게 공유해야 하는 상황.

**Invocation**  
`/express slide {주제 또는 "이전 결과 기반으로"}`  
`/express memo ...`  
(또는 think/write 결과 파일을 참조하는 식으로 호출)

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
- "story" 형식일 때 SCQA를 narrative로 어떻게 풀 것인가

**현재 구현 상태**
- Expression Agent에 출력 형식 템플릿은 존재
- Prior 활용 규칙은 간단히 명시
- **실제 사용 사례 거의 없음** → 형식 충실도와 Prior 매핑이 검증되지 않음

---

### S05: 반복 정교화 (Iterative Refinement)

**User Goal**  
한 번 만든 pyramid/report를 "더 좋게" 만들고 싶다. "리스크 더 넣어", "반론 고려해서 다시", "이 근거 더 깊게 파봐".

**Invocation**
- 같은 주제로 다시 `/think` 또는 `/write` 호출하면서 지시
- "이전 결과 수정해줘" 스타일

**Happy Path**
- Orchestrator가 이전 pyramid를 강하게 우선 선택
- Agent가 "증분 개선" 모드로 동작 (전체 재구성 vs 수정)
- 변경 사항과 이유를 명확히 기록

**Success Criteria**
- 사용자가 "점점 좋아진다"고 느끼는 증분
- 이전 버전과의 차이점이 추적 가능

**Critical Edge Cases**
- "완전히 새로 해" vs "이것만 고쳐"
- 여러 버전이 memory에 있을 때 최신/최고 품질 선택

**현재 상태**: 거의 정의되지 않음. Memory는 append-only 성격이 강함.

---

### S06: Cross-session Prior Reuse

**User Goal**  
며칠 전에 `/think` 해둔 내용을, 지금 다른 문맥에서 write나 solve에 활용하고 싶다.

**주요 위험**
- Orchestrator의 "주제 유사도 판단"이 정확해야 함
- 30일 이상 지난 prior에 대한 처리 (오래된 정보 플래그)
- 여러 유사한 prior 중 최선 선택

**현재 상태**
- Orchestrator에 키워드 추출 + 점수 계산 로직은 기술되어 있음
- 하지만 실제로 "가장 높은 점수"가 항상 옳은 prior를 고르는지 실전 검증 부족
- Read 실패 시 fallback은 정의됨

---

### S07: 모호 입력 / 역방향 입력 + 명시적 Override

**User Goal**
- 입력이 애매하거나 감정적일 때도 최대한 구조화해 주기를 바랄 때
- "이전 생각 완전히 무시하고 raw로 해줘"라고 명확히 지시했을 때

**처리 요구사항**
- Orchestrator와 Agent 모두 "use-prior: false" 또는 "ignore prior", "raw input", "이전 무시" 같은 신호를 명확히 인지
- Reverse structuring을 자동 감지하고 잘 처리
- 모호할 때는 가정을 명시적으로 드러냄

**현재 상태**
- Reverse structuring 규칙은 thinking.md에 있음
- Override 키워드는 orchestrator에 일부 언급
- 하지만 일관된 신호 파싱과 우선순위 규칙이 부족 (옵션 vs 자연어)

---

## 공통 패턴 & 원칙 (모든 시나리오에 적용)

### Prior Reuse 원칙 (재사용 vs 독립)
- Writing: coreClaim/Level1을 **Answer의 기반**으로 적극 재사용 (Pyramid Consumption). 템플릿은 `struct-docs/templates/` (report-default.md 등)에서 선택.
- Solve: Prior를 **문제 framing과 가설 생성 품질** 향상에만 사용 (직접 주장 이식 금지)
- Express: coreClaim = 핵심 메시지, Level1 = 슬라이드/근거 단위로 매핑
- Standalone Think: Prior는 참고 수준 (강제 아님)

### Memory & File 규칙
- `.struct-memory.json`은 라우팅과 선택을 위한 인덱스 역할 (previousThoughts 중심)
- 실제 내용은 `struct-docs/01-thinking/...` 등의 파일을 Orchestrator가 **Read** 도구로 로드
- maxItemsPerArray 초과 시 가장 오래된 항목 제거
- 자세한 메모리 구조 및 업데이트 규칙은 Orchestrator 프롬프트 참조

### 투명성
- Prior를 사용한 경우, 가능하면 최종 출력에 언급
- MECE 검증 + Critique Pass는 prior 사용 여부와 관계없이 항상 수행 + 기록

### Override 신호 (우선순위)
1. 명시적 옵션 (`use-prior: false`)
2. 사용자 자연어 ("이전 생각 무시", "raw로", "새로 분석해")
3. 자동 판단 (기본)

---

## 이 명세를 사용하는 방법 (개발/검증용)

1. 새로운 기능이나 프롬프트 변경 전, 반드시 이 문서의 해당 시나리오를 읽는다.
2. 변경 후에는 최소 해당 시나리오의 Happy Path + 1개 Edge Case를 직접 실행하여 검증.
3. 실제 사용 후 새로운 패턴이 발견되면 이 문서에 시나리오를 추가한다.
4. "이 기능은 어떤 시나리오를 지원하기 위한 것인가?"를 항상 질문한다.

**연관 문서**:
- 치트시트: `index.md`, `think.md`, `write.md`, `solve.md`, `express.md`
- 워크플로우: [workflow.mmd](workflow.mmd)
- 템플릿: `../templates/README.md`
- 상세 장문 시나리오: `../../docs/struct-usage-scenarios.md`

---

## 다음 단계 (이 문서 작성 후)

- [ ] 이 명세를 리뷰하고, 실제 사용 경험과 맞지 않는 부분 수정/추가
- [ ] P0 시나리오(S01, S02, S05, S07)부터 현재 구현(Orchestrator + Agent + Skill)을 1:1 매핑하여 Gap Analysis 문서 작성
- [ ] Gap을 바탕으로 agent 프롬프트, SKILL.md, memory 구조, usage 치트시트 개선 계획 수립
- [ ] 실제 테스트 입력 모음 구축 (각 시나리오별 골드 입력 + 기대 출력)

---

**문서 상태**: 2026-06-19. 템플릿 파일명 영문화 및 워크플로우 문서화 반영.
**작성 배경**: "구체적인 시나리오 없이 agent를 만들어서 허점이 너무 많고 추상적"이라는 문제 인식에서 출발.

> 관련 리소스: [workflow.mmd](workflow.mmd), `struct-docs/templates/README.md`
