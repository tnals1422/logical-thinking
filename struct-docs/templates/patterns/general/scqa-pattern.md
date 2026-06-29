---
tags: [struct, template, logic-pattern]
pattern: scqa-pattern
name: "SCQA (상황-문제-질문-답변)"
useWhen: "일반 분석·보고·대안 비교. Brief 없음·general 폴백. deliverable §대안·§결론 섹션 내부 전개."
outputStyle: "개조식 + 표 + 시각자료 우선 (Minto 기본 스타일)"
scope: standalone | section-embed
replaces: report-default
---

# {문서 제목}

## Executive Summary

> {coreClaim — 핵심 주장 1문장}

{핵심 주장 + 주요 근거 3줄 이내 bullet}

## 1. 상황 (Situation)

{독자가 동의할 수 있는 배경·사실. 변화 이전 정상 상태.}

## 2. 문제 (Complication)

{상황을 깨뜨리는 문제·변화·긴장. 왜 지금 중요한가.}

## 3. 핵심 질문 (Question)

> {문제에서 자연스럽게 도출되는 1문장 질문}

## 4. 답변 및 근거 (Answer)

### 4.1 핵심 주장

> {coreClaim}

### 4.2 근거

#### 근거 1: {제목}

- {포인트}
- {데이터·사례}
- {함의}

#### 근거 2: {제목}

- {포인트}
- {데이터·사례}
- {함의}

#### 근거 3: {제목}

- {포인트}
- {데이터·사례}
- {함의}

## 5. 결론 및 제언

{핵심 주장 재강조 + 실행 가능한 다음 단계}

### 액션 아이템

- [ ] {항목 1}
- [ ] {항목 2}
- [ ] {항목 3}

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### When embedded inside a deliverable (embed mode)

**Core logic block to use**:
- Situation (사실 중심)
- Complication (긴장·문제)
- Question (1문장)
- Answer + 근거 (표·bullet)
- (선택) 결론·제언

**Omit on embed** (usually):
- Standalone Executive Summary (deliverable의 개요/요약으로 대체)
- Standalone full 결론 (deliverable 상위 건의/결론 섹션 사용)

**Rules**:
- deliverable에 SCQA 앵커가 있으면 **Question 1문장 + Answer 구조**는 필수.
- Situation·Complication은 **객관적 사실 bullet** 우선.
- Answer는 prior thinking Level 1을 근거로 매핑.

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

### 기존 deliverable 임베드 예시 (참고)

| deliverable | 임베드 섹션 | 작성 범위 |
|-------------|------------|----------|
| policy-planning | §3.0 Question + §3.2~3.3 Answer | 대안 비교·선정 |
| meeting-result | §2.{n} 안건 (논의→결정) | 안건별 미니 SCQA |
| general | 전체 | standalone |

### 원칙 (기본)

- Situation·Complication은 **사실 bullet** 우선
- Answer는 prior Level 1을 근거 bullet로 매핑
- deliverable에 SCQA 섹션이 있으면 Question 1문장 + Answer 표/근거 **필수**