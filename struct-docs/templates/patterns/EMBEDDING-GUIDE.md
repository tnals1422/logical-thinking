# Pattern Embedding Guide

**Purpose**: Define clear rules for using `patterns/*.md` inside deliverables via `(logic: xxx-pattern)` anchors.

This guide is the authoritative reference for writing agent (W1/W2) and review agent (D7 / logicSectionsFilled).

---

## 1. Core Principle

- **Pattern is the authority** for its logic structure.
- When `(logic: xxx-pattern)` appears in a deliverable, the **pattern file** dictates:
  - Step headings (e.g. 1. 기반구조, 2. 행동, 3. 사건, 4. 판단)
  - Development method ("이런 까닭에 →", Feature → Advantage → Benefit → Evidence 등)
  - Recommended visuals (Mermaid block-beta, tables, etc.)
- Deliverable provides only the **container section** and context (why this logic block exists here, what the surrounding deliverable expects).

**Do not duplicate** a pattern’s internal numbered steps inside a `deliverables/*.md` file.

---

## 2. What to Include vs Omit on Embed

### Always include (from the pattern)
- The core logical steps with their defined names and order.
- Guidance on how to develop each step (e.g. "기반구조는 변하지 않는 구조·제도·인프라 중심").
- Required elements mentioned in the pattern (e.g. 최소 2개의 "이런 까닭에 →").
- Recommended Mermaid / table formats (adapted to the surrounding section).

### Usually omit on embed
- Pattern’s standalone `## Executive Summary` (deliverable already has 개요 or 요약).
- Pattern’s top-level document title.
- Pattern’s standalone `## 결론` or final summary when the deliverable has a higher-level 건의/결론 섹션.
- Pattern’s full "패턴 가이드" section (agent-only reference).

**Exception**: If the entire deliverable is basically one pattern (rare, mostly `general` case), you may keep more of the pattern’s wrapper.

---

## 3. How Writing Agent Should Consume

### W1 (Skeleton)
1. Read the deliverable template → place major sections and the `(logic: ...)` container heading.
2. Read the referenced pattern.
3. Under the container, insert the **pattern’s core logic steps** as sub-structure.
4. Map prior thinking Level 1 items into the appropriate logical steps.

Example (policy-planning §2.2):
```markdown
### 2.2 원인분석 (logic: iaej-pattern — **필수 전개**)

> iaej-pattern의 4단계 + '이런 까닭에 →' 제언을 전개. 패턴: patterns/reports/iaej-pattern.md

#### 2.2.1 기반구조 (Infrastructure)
...
```

(Or keep pattern’s own numbering if deliverable prefers a clean hand-off.)

### W2 (Draft)
- Fill each step from the pattern using prior thinking + input.
- Never replace a pattern step name with a different one (this triggers D7 L4).

### W3 / W4
- Audience and submission rules are owned by the deliverable / Brief, not the pattern.

---

## 4. Pattern-by-Pattern Embed Notes

| Pattern | Typical Embed Locations | Special Embed Rules | Omit on Embed |
|---------|--------------------------|---------------------|---------------|
| `scqa-pattern` | policy-planning §3, meeting-result 안건별, general | Question 1문장 + Answer 표·근거 필수 | Standalone Executive Summary + 결론 (deliverable가 대신함) |
| `iaej-pattern` | policy-planning §2.2, policy-reference §2.2 (lite) | 4단계 모두 + '이런 까닭에 →' 최소 2개. lite 모드에서는 판단·제언 생략 | Full 결론 (상위 건의로 대체) |
| `incident-causal-pattern` (ser) | coordination §3, event B.2, meeting-result (선택) | Structure → Event → Response + 대처 효과 | — |
| `incident-causal-pattern` (stad) | situation-info (information+인과) | State → Trigger → Accident → Damage → Countermeasures | — |
| `objective-policy-pattern` | policy-planning §4, meeting-material (decision), event (planning) | (1) **목적** / **방침** 쌍 형식 엄격 유지. 최소 2쌍 권장 | Standalone wrapper |
| `fabe-pattern` | policy-planning 대안 비교, meeting-material 옵션 평가 | Feature → Advantage → Benefit → Evidence 순서 | — |
| `prep-pattern` | policy-planning §3 내부, meeting-result 결정 포인트 | Point-Reason-Evidence-Point | — |
| `case-measure-pattern` | 교육·규칙 관련 섹션 | 조건 → 조치/판단 | — |

---

## 5. Review Agent (D7 / logicSectionsFilled) 규칙

Review는 다음을 확인한다:

1. Deliverable frontmatter `logicSections`에 선언된 앵커가 본문에 존재하는가?
2. 해당 앵커 아래에 **패턴의 핵심 단계 제목**이 누락 없이 등장하는가?
3. 각 단계에 bullet·표·Mermaid 등 **실질 내용**이 1개 이상 있는가?
4. 단계 이름 변경(L4) 금지: "기반구조"를 "배경"으로 바꾸지 말 것.
5. 분기 준수 (meetingPurpose, eventPhase, logicPatternMode, subType).

**W4 제출본**의 경우 Appendix `logicSectionsFilled`가 제거되더라도, `deliverableTemplate`의 `logicSections` 선언과 본문 `(logic: …)` + 실제 패턴 단계를 대조한다.

---

## 6. Adding or Modifying a Pattern

- 패턴을 개선할 때 **반드시** 이 EMBEDDING-GUIDE와 templates/README.md의 Role Contract를 함께 검토.
- 임베드 시 동작이 달라지는 경우 이 가이드를 업데이트.
- 새 패턴 추가 시:
  1. 해당 카테고리 폴더에 배치
  2. `patterns/README.md` 갱신
  3. templates/README.md 매트릭스 갱신
  4. 필요 시 이 EMBEDDING-GUIDE에 한 줄 추가

---

## 7. Examples of Good Embed Anchors (in deliverables)

```markdown
### 2.2 원인분석 (logic: iaej-pattern — **필수 전개**)
> iaej-pattern 4단계를 전개하라. 패턴 파일의 단계 제목과 방법을 그대로 사용.

### 3. 조정 대안 분석 (logic: incident-causal-pattern, mode=ser — **필수 전개**)

### 3.C.4 결정 후 추진 (logic: objective-policy-pattern — **필수 전개**)
```

Avoid writing the pattern’s internal headings inside the deliverable file.

---

**Related documents**
- `docs/struct-template-role-unification-plan.md`
- `struct-docs/templates/README.md` (Role Contract)
- `reference/writing-pipeline.md`
- `reference/orchestrator-review-gate.md`
- Individual pattern files (their own "패턴 가이드" 섹션)

이 가이드는 Phase 1 이후 patterns와 writing/review 에이전트의 동작 기준이 된다.