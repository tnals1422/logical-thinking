# Minto Thinking Enhancement Planning Document

> **Summary**: Part 1(정보 정리 기초) CS/GPS 프레임워크를 Thinking 프로세스에 통합하여 논리적 사고 품질 고도화
>
> **Project**: agent-creator (Minto Agent Team)
> **Author**: 박수민
> **Date**: 2026-06-18
> **Status**: Draft

---

## Executive Summary

| Perspective | Content |
|-------------|---------|
| **Problem** | 현재 Thinking Agent가 입력 텍스트를 바로 피라미드로 구축하여, 정보를 분해·정리하는 사전 단계(CS/GPS)가 누락됨. 복잡한 정보의 구조를 파악하지 못하고 평면적 그룹핑만 수행 |
| **Solution** | Part 1의 CS(범주·요점) + GPS(그룹·패러렐·시리즈) 프레임워크를 피라미드 구축 전 단계로 추가하고, 인과관계 모델·로직트리 규칙·역방향 구조화를 보강 |
| **Function/UX Effect** | 입력 텍스트의 추상화 수준별 분리(범주→요점→본문), 다차원 비교(패러렐), 순서 기반 누락 탐지(시리즈)가 자동 수행되어 피라미드 품질 향상 |
| **Core Value** | "무엇을 쓸 것인가(CS)"와 "어떻게 구조화할 것인가(GPS)"를 명확히 분리하여, 사고의 정밀도와 재현성을 높임 |

---

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | 피라미드 구축 전 정보 정리 단계 부재로 인한 논리적 사고 품질 한계 |
| **WHO** | Minto 스킬 사용자 (개인 전문가) |
| **RISK** | 프로세스 단계 증가로 인한 응답 시간 증가 및 프롬프트 복잡도 상승 |
| **SUCCESS** | 6개 GAP 반영 완료, MECE 검증 통과율 유지(>90%), 응답 시간 <3분 |
| **SCOPE** | thinking.md → problem-solving.md → writing.md → expression.md 순차 개선 |

---

## 1. Overview

### 1.1 Purpose

Minto 에이전트 팀의 Thinking 프로세스에 Part 1(정보 정리에 필요한 기초 지식과 사고방식)의 핵심 프레임워크를 통합한다. 현재 에이전트는 입력을 바로 피라미드로 구축하지만, 원서에서는 피라미드 이전에 **정보를 분해·정리하는 CS/GPS 단계**를 거친다. 이 누락된 단계를 추가하여 논리적 사고의 정밀도를 높인다.

### 1.2 Background

- **현재 상태**: Thinking Agent의 5-Step(아이디어 추출 → 피라미드 → MECE → 논리분석 → 요약)
- **문제점**: Part 1에서 제시하는 6개 핵심 프레임워크 중 CS, GPS, 인과관계 모델, 로직트리 규칙, 질문→결론→행동 구조가 부재하거나 약함
- **원인**: 초기 구현 시 Minto 피라미드 원칙(Part 2~3)에 집중하여 Part 1의 기초 사고방식을 반영하지 않음

### 1.3 Related Documents

- 원서: Part 1. 정보 정리에 필요한 기초 지식과 사고방식 (1~4장)
- 이전 Plan: `docs/archive/2026-06/minto-agent-team/minto-agent-team.plan.md`
- 현재 에이전트: `.claude/agents/thinking.md`, `problem-solving.md`, `writing.md`, `expression.md`

---

## 2. Scope

### 2.1 In Scope

- [x] GAP 1: CS(범주·요점) 프레임워크 — thinking.md에 Step 추가
- [x] GAP 2: GPS(그룹·패러렐·시리즈) 프레임워크 — thinking.md에 Step 추가
- [x] GAP 3: 논리도해 개념 — GPS 구조의 텍스트 다이어그램 출력
- [x] GAP 4: 인과관계 모델 정밀화 — problem-solving.md에 독립/종속 사건 프레임 추가
- [x] GAP 5: 로직트리 실용 규칙 — MECE 검증 단계에 완화 규칙 적용
- [x] GAP 6: 질문→결론→행동 구조 — thinking.md에 역방향 구조화 경로 추가
- [x] ThinkingResult JSON v2 — csAnalysis, gpsStructure 필드 확장
- [x] 하위 에이전트 전파 — writing.md, expression.md에 CS/GPS 참조 반영

### 2.2 Out of Scope

- Part 2~3의 추가 프레임워크 (이미 반영됨)
- 스킬 인터페이스 변경 (`/minto-think` 등 호출 방식 유지)
- Orchestrator 로직 변경 (라우팅 규칙 유지)
- 시각적 다이어그램 이미지 생성 (텍스트 기반 ASCII만)
- 새로운 에이전트 추가

---

## 3. Requirements

### 3.1 Functional Requirements

| ID | Requirement | Priority | Status |
|----|-------------|----------|--------|
| FR-01 | thinking.md에 CS 분석 단계 추가: 입력 텍스트에서 범주(항목)와 요점(정리 내용)을 추출하는 Step | High | Pending |
| FR-02 | thinking.md에 GPS 구조화 단계 추가: 그룹(분기/포함/중심 3유형), 패러렐(비교표), 시리즈(순서 정렬) | High | Pending |
| FR-03 | ThinkingResult JSON v2에 `csAnalysis`와 `gpsStructure` 필드 추가 | High | Pending |
| FR-04 | GPS 구조를 텍스트 다이어그램으로 출력 (분기형=트리, 포함형=중첩, 패러렐=표, 시리즈=화살표) | Medium | Pending |
| FR-05 | problem-solving.md에 독립사건/종속사건 인과관계 프레임 추가 | Medium | Pending |
| FR-06 | MECE 검증에 로직트리 실용 규칙 적용 (3단계 이후 완화, 자식=부모1개) | Medium | Pending |
| FR-07 | thinking.md에 역방향 구조화 경로 추가 (질문→결론→행동: 결론을 알지만 설명이 서툰 경우) | Medium | Pending |
| FR-08 | writing.md, expression.md의 내장 Thinking 로직에 CS/GPS 참조 반영 | Low | Pending |

### 3.2 Non-Functional Requirements

| Category | Criteria | Measurement Method |
|----------|----------|-------------------|
| 응답 시간 | CS/GPS 단계 추가 후에도 전체 응답 <3분 | 수동 측정 10회 평균 |
| 호환성 | 기존 ThinkingResult JSON v1 필드 유지 (하위 호환) | 기존 테스트 재실행 |
| 프롬프트 크기 | 에이전트 프롬프트 총량 증가 <50% | 문자 수 비교 |

---

## 4. Success Criteria

### 4.1 Definition of Done

- [ ] thinking.md에 CS 분석 + GPS 구조화 2개 Step이 추가됨
- [ ] ThinkingResult JSON v2에 csAnalysis, gpsStructure 필드가 포함됨
- [ ] problem-solving.md에 독립/종속 사건 인과관계 프레임이 추가됨
- [ ] MECE 검증에 로직트리 실용 규칙(3단계 이후 완화)이 반영됨
- [ ] thinking.md에 역방향 구조화(질문→결론→행동) 경로가 추가됨
- [ ] writing.md, expression.md의 내장 Thinking 로직이 CS/GPS를 참조함
- [ ] `/minto-think` 실행 시 CS/GPS 분석 결과가 출력에 포함됨

### 4.2 Quality Criteria

- [ ] 기존 `/minto-think` 테스트 케이스가 동일하게 동작 (하위 호환)
- [ ] Part 1 예제(CD 분실 사건, S 프로젝트 입력 작업)로 CS/GPS 분석이 올바르게 수행됨
- [ ] MECE 검증 통과율 >90% 유지

---

## 5. Risks and Mitigation

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| 프롬프트 복잡도 증가로 에이전트 품질 저하 | High | Medium | 단계별 점진 추가 + 각 Step 독립 테스트 |
| CS/GPS 단계 추가로 응답 시간 증가 | Medium | High | CS/GPS를 단일 Step으로 통합하여 오버헤드 최소화 |
| ThinkingResult v2 호환성 문제 | Medium | Low | v1 필드 전체 유지, 새 필드는 optional |
| 복잡한 입력에서 GPS 유형 오판 | Medium | Medium | 그룹 3유형 판단 기준을 명시적으로 정의 |

---

## 6. Impact Analysis

### 6.1 Changed Resources

| Resource | Type | Change Description |
|----------|------|--------------------|
| `.claude/agents/thinking.md` | Agent Prompt | CS/GPS Step 추가, MECE 규칙 보강, 역방향 구조화 추가, JSON v2 스키마 |
| `.claude/agents/problem-solving.md` | Agent Prompt | 인과관계 모델에 독립/종속 사건 프레임 추가 |
| `.claude/agents/writing.md` | Agent Prompt | 내장 Thinking 로직에 CS/GPS 참조 추가 |
| `.claude/agents/expression.md` | Agent Prompt | 내장 Thinking 로직에 CS/GPS 참조 추가 |

### 6.2 Current Consumers

| Resource | Operation | Code Path | Impact |
|----------|-----------|-----------|--------|
| thinking.md | READ | orchestrator.md → Agent 호출 | None (인터페이스 유지) |
| thinking.md | READ | minto-think SKILL.md → Agent 호출 | None (인터페이스 유지) |
| ThinkingResult JSON | READ | writing.md 내장 로직 | Needs verification (새 필드 추가) |
| ThinkingResult JSON | READ | problem-solving.md 내장 로직 | Needs verification (새 필드 추가) |
| ThinkingResult JSON | READ | expression.md 내장 로직 | Needs verification (새 필드 추가) |
| ThinkingResult JSON | WRITE | .minto-memory.json 저장 | Needs verification (새 필드 포함 저장) |

### 6.3 Verification

- [ ] orchestrator.md 라우팅 로직이 변경 없이 동작
- [ ] 4개 스킬(`/minto-think`, `/minto-write`, `/minto-solve`, `/minto-express`) 정상 호출
- [ ] .minto-memory.json에 확장된 ThinkingResult 저장/로드 정상

---

## 7. Architecture Considerations

### 7.1 Project Level Selection

| Level | Characteristics | Recommended For | Selected |
|-------|-----------------|-----------------|:--------:|
| **Starter** | 프롬프트 기반 에이전트, 파일 I/O만 | 현재 프로젝트 구조와 일치 | x |

### 7.2 Key Architectural Decisions

| Decision | Options | Selected | Rationale |
|----------|---------|----------|-----------|
| CS/GPS 통합 위치 | Step 분리 / 기존 Step 확장 | 새 Step 2개 추가 | 원서의 CS → GPS 순서를 명확히 반영 |
| ThinkingResult 확장 방식 | 필드 추가 / 별도 JSON / 내부 처리만 | 필드 추가 (v2) | 하위 에이전트에 구조 정보 전파 필요 |
| GPS 출력 형식 | 텍스트만 / ASCII 다이어그램 / 이미지 | ASCII 다이어그램 | 코드 없이 Markdown으로 표현 가능 |
| 에이전트 변경 순서 | 동시 / 순차 | 순차 (thinking → problem-solving → writing → expression) | 의존성 순서, 단계별 검증 가능 |

### 7.3 Thinking Process v2 구조

```
현재 (v1):
  Step 1: Idea Structuring (아이디어 추출 → 그룹핑)
  Step 2: Pyramid Building
  Step 3: MECE Validation
  Step 4: Logic Analysis
  Step 5: Summary Generation

개선 후 (v2):
  Step 1: CS Analysis (범주·요점 추출)        ← NEW
  Step 2: GPS Structuring (그룹/패러렐/시리즈) ← NEW
  Step 3: Pyramid Building (기존 Step 2)
  Step 4: MECE Validation (로직트리 규칙 보강) ← ENHANCED
  Step 5: Logic Analysis (기존 유지)
  Step 6: Summary Generation (기존 유지)

  [역방향 경로] 질문→결론→행동 (결론 선행 시)  ← NEW
```

---

## 8. Detailed Design Specification

### 8.1 Step 1: CS Analysis (범주·요점 추출)

**목적**: 입력 텍스트에서 "무엇을 써야 하는가"를 결정한다.

**프로세스**:
1. 입력 텍스트를 읽고 정보 항목(범주)을 식별한다
2. 각 범주 아래에 요점(정리된 핵심 내용)을 배치한다
3. 추상화 계층을 확인한다: 본문 < 요점 < 범주
4. 본문에서 생략된 범주가 있으면 추론하여 보충한다

**출력 (csAnalysis)**:
```json
{
  "csAnalysis": {
    "categories": [
      {
        "name": "업무 개요",
        "summaries": [
          "새 업무 과정 절차에 따른 데이터 입력과 수정 작업"
        ]
      },
      {
        "name": "문제점",
        "summaries": [
          "절차 매뉴얼에 불분명한 부분이 많음",
          "절차대로 작업해도 오류 발생",
          "사소한 입력 실수 빈발"
        ]
      }
    ]
  }
}
```

### 8.2 Step 2: GPS Structuring (구조화)

**목적**: CS에서 추출한 범주와 요점을 "어떻게 구조화할 것인가"를 결정한다.

**프로세스**:
1. **그룹 탐색**: 같은 종류끼리 묶을 수 있는 요소를 찾는다
   - 유형 판단: 분기형(계층) / 포함형(집합) / 중심형(허브)
   - 분기형: 상하 계층이 있을 때
   - 포함형: 여러 요소를 하나로 모을 때
   - 중심형: 요소가 중심 개념을 촉진/지원할 때
2. **패러렐 탐색**: 그룹이 여러 개라면 공통 항목으로 비교표를 구성할 수 있는지 검토한다
3. **시리즈 탐색**: 그룹 내 요소에 순서를 매길 기준이 있는지 확인한다
   - 순서를 매기는 이유: 빠짐/중복 확인, 추가 그룹 발견, 목적 정립

**출력 (gpsStructure)**:
```json
{
  "gpsStructure": {
    "groups": [
      {
        "name": "문제 원인",
        "type": "branch",
        "elements": ["불분명한 절차", "입력 실수", "오류 검출 부재"]
      }
    ],
    "parallels": [
      {
        "axis": "문제-영향-해결",
        "items": [
          {"문제": "불분명한 절차", "영향": "작업 혼란", "해결": "매뉴얼 보완"},
          {"문제": "입력 실수", "영향": "공수 50% 증가", "해결": "오류 검출 기능"}
        ]
      }
    ],
    "series": [
      {
        "criterion": "발생 순서",
        "order": ["절차 불명확 인식", "입력 실수 발생", "오류 검출 실패", "공수 증가"]
      }
    ]
  }
}
```

### 8.3 Step 4 Enhanced: MECE Validation + 로직트리 규칙

**추가 규칙**:
- 피라미드 1~2단계: 엄격한 MECE 적용
- 피라미드 3단계 이후: MECE 완화 (완벽한 분류보다 실용적 분류 우선)
- 자식 요소는 부모 1개만 가진다 (중복 배치 금지)
- MECE가 잘 안 되는 경우: "문제를 올바르게 이해할 기회"로 해석하여 재구조화 유도

### 8.4 역방향 구조화: 질문→결론→행동

**적용 조건**: 입력에 이미 결론이 포함되어 있으나 논리적 설명이 부족한 경우

**프로세스**:
1. 입력에서 결론(주장)을 먼저 식별한다
2. "이 결론에 대해 어떤 질문이 나올 수 있는가?"를 역추적한다
3. 질문에 대한 답변으로 근거를 구성한다
4. 근거 → 구체적 행동(액션)을 도출한다
5. 결과를 정방향 피라미드(질문→결론→근거→행동)로 재구성한다

### 8.5 인과관계 모델 정밀화 (problem-solving.md)

**추가 프레임**:
- **독립 사건(원인)**: 다른 사건의 영향 없이 스스로 발생하는 사건
- **종속 사건(결과)**: 독립 사건에 의해 발생하는 사건
- **예측 활용**: 인과관계가 명확한 현상이라면, 독립 사건의 관찰 여부로 종속 사건 발생을 예측
- 기존 5 Whys와 병행 사용: 5 Whys로 근본 원인 도달 후, 독립/종속 프레임으로 예측력 강화

---

## 9. Implementation Order

| Step | Target File | Changes | Dependency |
|------|-------------|---------|------------|
| 1 | `thinking.md` | CS Analysis Step 추가 | None |
| 2 | `thinking.md` | GPS Structuring Step 추가 | Step 1 |
| 3 | `thinking.md` | ThinkingResult JSON v2 스키마 정의 | Step 1, 2 |
| 4 | `thinking.md` | MECE 로직트리 규칙 보강 | None |
| 5 | `thinking.md` | 역방향 구조화 경로 추가 | None |
| 6 | `problem-solving.md` | 독립/종속 사건 인과관계 프레임 추가 | None |
| 7 | `writing.md` | 내장 Thinking 로직에 CS/GPS 참조 반영 | Step 1, 2, 3 |
| 8 | `expression.md` | 내장 Thinking 로직에 CS/GPS 참조 반영 | Step 1, 2, 3 |

---

## 10. Next Steps

1. [ ] Design 문서 작성 (`/pdca design minto-thinking-enhancement`)
2. [ ] 팀 리뷰 및 승인
3. [ ] 구현 시작

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-06-18 | Initial draft | 박수민 |
