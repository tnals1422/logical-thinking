---
description: 입력을 구조화된 논리 문서(적합한 템플릿 선택)로 만듭니다
user-invocable: true
---

# /struct-write

사용자의 입력을 명확한 논리 구조로 정리한 문서로 생성하는 스킬. (정보 정리 후 내용 특성에 맞는 템플릿 자동/협의 선택)

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
write 수행

## User Input
{사용자가 입력한 텍스트 전체}

## Options
template: {사용자가 지정한 템플릿, 없으면 "default"}
use-prior: true | false   (기본: 자동 판단. true = prior thinking 강제 사용, false = 무시)

## Context (from .struct-memory.json)
{이전 생각 정리 요약이 있으면 포함, 없으면 "이전 컨텍스트 없음"}

**참고**: 
- `use-prior: true` 또는 `source: thinking` 전달 시 Orchestrator는 prior thinking을 강제로 주입한다.
- Orchestrator는 관련 prior thinking이 있을 경우 `## Previous Thinking Pyramid` 블록을 주입한다.
- writing agent는 pyramid 분석 후 내용 특성(문제진단/제안/목적-방침/사건-대처 등)에 따라 가장 적합한 템플릿을 선택하여 적용한다. (SCQA는 하나의 옵션일 뿐)
- template 옵션으로 사용자가 지정 가능 (없으면 agent가 선택). Collaborative 모드에서는 선택 확인.
```

3. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "writing"`**으로 직접 호출한다:

1. 반환된 문서를 사용자에게 출력한다
2. `.struct-memory.json`의 `previousDocuments` 배열에 추가한다
3. `struct-docs/02-writing/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
