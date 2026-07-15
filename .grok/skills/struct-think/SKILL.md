---
description: 아이디어를 구조화된 논리로 정리합니다 (피라미드 원칙 기반)
user-invocable: true
---

# /struct-think

사용자의 아이디어를 명확한 논리 구조(피라미드)로 정리하는 스킬.

## Primary runtime

**Grok Build · Parent-as-Orchestrator.**  
`subagent_type: "orchestrator"` 를 **스폰하지 않는다** (primary 금지).  
프로세스 스펙: `.grok/agents/orchestrator.md` (Parent가 인라인 수행).  
설계: `docs/archive/2026-07/grok-parent-as-orchestrator/grok-first-parent-as-orchestrator.design.md`

## Instructions

Parent(메인 에이전트)가 다음을 **직접** 수행한다.

1. **Mode**: collaborative(기본) | autonomous — 사용자 입력·옵션에서 감지
2. **Memory**: `.struct-memory.json` 로드 (없으면 orchestrator.md §2 기본 구조로 생성)
3. **Context**: 이전 `previousThoughts` 요약이 있으면 포함
4. **Specialist spawn** — `spawn_subagent` / Agent 도구:
   - `subagent_type: "thinking"` **만** 사용
   - 프롬프트:

```
## Task
think 수행

## User Input
{사용자가 입력한 텍스트 전체}

## Mode
{collaborative | autonomous}

## Context (from .struct-memory.json)
{이전 생각 정리 요약이 있으면 포함, 없으면 "이전 컨텍스트 없음"}

## Output Requirements
- Markdown (개조식 + 표/다이어그램 기본)
- Frontmatter: tags, created, type, coreClaim
```

5. **저장**: `struct-docs/01-thinking/YYYYMMDD-{kebab-title}.md`
6. **Memory 갱신**: `previousThoughts`에 항목 추가 (`maxItemsPerArray` 초과 시 가장 오래된 항목 제거). 필드: id, timestamp, coreClaim, level1Claims, pyramidSummary, meceStatus, savedTo 등 (orchestrator.md think 결과 스키마)
7. **출력**: 피라미드 Markdown을 사용자에게 표시

## Emergency

- `thinking` 스폰 실패 시: 오류를 사용자에게 보고. 조용히 생략하지 않는다.
- orchestrator 자식 스폰으로 재시도하지 않는다.

## 참고

- Brief / Deliverable Spec **없음** (think 전용)
- 상세 Process: `.grok/agents/orchestrator.md` think 라우팅
