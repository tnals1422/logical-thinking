---
tags: [struct, template, logic-pattern]
pattern: objective-policy-pattern
name: "Objective-Policy (목적-방침)"
useWhen: "추진계획·회의 안건·행사 프로그램. '목적 + 방침' 쌍으로 실행 항목 정리. policy §4, meeting §3.C, event A.3 필수."
outputStyle: "번호 항목마다 **목적** / **방침** 2줄 쌍. deliverable 임베드 시 (n) 형식 유지."
scope: standalone | section-embed
---

# {문서 제목}

## Executive Summary

{전체 계획의 핵심 목적 한 줄 + 가장 중요한 방침 요약}

## {주요 섹션 제목, 예: 이후 진행 방침 / 추진 계획}

(1) **목적**: {이 항목을 하는 진짜 이유 — 결과 상태}

   **방침**: {구체적으로 어떻게 할 것인가 — 행동 규칙이나 방식}

(2) **목적**: {이 항목을 하는 진짜 이유}

   **방침**: {구체적으로 어떻게 할 것인가}

(3) **목적**: {이 항목을 하는 진짜 이유}

   **방침**: {구체적으로 어떻게 할 것인가}

## 추가 고려사항 (선택)

- 리스크 관리
- 성공 기준
- ...

---

## 패턴 가이드 (에이전트 참고 — 출력에 포함하지 않음)

### When embedded inside a deliverable (embed mode)

**Core logic block to use**:
- (1) **목적**: {달성 상태}
    **방침**: {실행 방식}
- (2) **목적**...
    **방침**...

**Omit on embed** (usually):
- Standalone Executive Summary
- Standalone 전체 wrapper (deliverable의 상위 섹션 사용)

**Rules**:
- 번호 매긴 (n) **목적 / 방침** 쌍 형식 엄격 유지.
- 최소 2쌍 권장 (policy-planning, meeting decision 등).
- "목적"은 결과 지향, "방침"은 방법·제약.

상세 규칙은 중앙 가이드 참조: `patterns/EMBEDDING-GUIDE.md`

**개념** (기본)
> 무엇을 위해 (목적)  
> 무엇을 하겠다 (방침)

**사용 원칙**
- 기존의 모호한 "해야 할 일" 목록을 목적-방침 쌍으로 재작성
- "목적"은 결과 지향, "방침"은 방법/제약/스타일 지향
- 문맥에 따라 '목표/전략', '의도/수단' 등으로 변형 가능

**Before → After 예시 (참고)**
- Before: "짧게, 그리고 깔끔하게 작성한다"
- After: 
  - 목적: 오류가 발생한 곳을 쉽게 찾기
  - 방침: 짧게, 깔끔하고 보기 좋게 프로그램을 작성한다

이 패턴은 특히 실행 계획, 팀 방침, 개인 작업 가이드라인에 적합하다. (policy-planning §4, meeting-material decision, event planning 등에서 자주 임베드)

