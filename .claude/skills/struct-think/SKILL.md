---
description: 아이디어를 구조화된 논리로 정리합니다 (피라미드 원칙 기반)
user-invocable: true
---

# /struct-think

사용자의 아이디어를 명확한 논리 구조(피라미드)로 정리하는 스킬.

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
think 수행

## User Input
{사용자가 입력한 텍스트 전체}

## Context (from .struct-memory.json)
{이전 생각 정리 요약이 있으면 포함, 없으면 "이전 컨텍스트 없음"}
```

3. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "thinking"`**으로 직접 호출한다:

1. 반환된 피라미드 Markdown을 사용자에게 출력한다
2. `.struct-memory.json`에 결과를 기록한다:
   - `previousThoughts` 배열에 추가
   - `maxItemsPerArray` 초과 시 가장 오래된 항목 제거
3. `struct-docs/01-thinking/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
   - Frontmatter 포함: tags, created, type
