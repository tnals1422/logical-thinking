---
description: 구조화된 내용을 슬라이드·메모·스토리로 변환합니다
user-invocable: true
---

# /struct-express

구조화된 논리를 발표 슬라이드, 메모, 스토리텔링으로 변환하는 스킬.

## Usage

```
/struct-express {텍스트}                 → 슬라이드 (기본값)
/struct-express slide {텍스트}           → Marp 호환 슬라이드
/struct-express memo {텍스트}            → 1페이지 메모
/struct-express story {텍스트}           → 스토리텔링
```

## Instructions

1. `.struct-memory.json`이 존재하면 읽어서 이전 컨텍스트를 확인한다
2. 사용자 입력에서 format을 파싱한다:
   - 첫 단어가 slide/memo/story이면 해당 format 사용
   - 없으면 기본값 "slide"
3. Agent 도구를 **`subagent_type: "orchestrator"`**로 호출한다. 프롬프트에 다음을 포함한다:

```
## Task
express 수행

## User Input
{사용자가 입력한 텍스트 (format 키워드 제외)}

## Format
{slide | memo | story}

## Options
use-prior: true | false   (기본: 자동 판단)

## Context (from .struct-memory.json)
{이전 결과가 있으면 포함, 없으면 "이전 컨텍스트 없음"}

**참고**: 
- `use-prior: true` 전달 시 Orchestrator가 prior를 강제 주입.
- Orchestrator는 관련 prior thinking이 있을 경우 `## Previous Thinking Pyramid`를 주입한다.
- expression agent는 coreClaim을 핵심 메시지로, Level 1을 슬라이드/메모 근거로 매핑하는 것을 우선한다.
```

4. Orchestrator가 반환한 결과를 사용자에게 출력한다

## Fallback

Orchestrator가 없거나 호출 실패 시, Agent 도구를 **`subagent_type: "expression"`**으로 직접 호출한다:

1. 반환된 결과를 사용자에게 출력한다
2. `.struct-memory.json`의 `previousDocuments` 배열에 추가한다
3. `struct-docs/04-expressing/` 하위에 결과 파일을 저장한다
   - 파일명: `YYYYMMDD-{kebab-title}.md`
