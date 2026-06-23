---
tags: [minto, plan, pdca]
created: 2026-06-17
type: plan
feature: minto-usage-guide
phase: plan
---

# minto-usage-guide Plan

## Executive Summary

| 관점 | 내용 |
|------|------|
| **문제** | `minto-agent-team`의 4개 커맨드(`/think`, `/write`, `/solve`, `/express`)를 사용하려 해도 어디서 어떻게 쓰는지 즉시 참조할 문서가 없음 |
| **솔루션** | `docs/usage/`에 커맨드별 치트 시트 파일 생성 — 커맨드 문법, 인자, 예시 코드 중심 |
| **기능 효과** | Claude Code 세션 시작 시 `/think --help` 대신 `docs/usage/` 참조로 즉시 작업 가능 |
| **핵심가치** | 인지 부하 없이 에이전트 팀을 바로 활용하는 개인 참조 문서 |

---

## Context Anchor

| 축 | 내용 |
|----|------|
| **WHY** | minto-agent-team 완성 후 사용 마찰을 없애기 위한 참조 문서 필요 |
| **WHO** | 본인 (개인 전용, 공유 불필요) |
| **RISK** | 과도한 설명으로 치트 시트가 가이드북화 될 위험 |
| **SUCCESS** | 각 파일을 30초 내 훑어보고 커맨드를 실행할 수 있는 상태 |
| **SCOPE** | docs/usage/ 아래 5개 파일 (index + 커맨드 4개), 코드 0줄 |

---

## 1. 요구사항

### 1.1 기능 요구사항

| # | 요구사항 | 우선순위 |
|---|----------|----------|
| FR-01 | `docs/usage/index.md` — 전체 커맨드 목록 + 1줄 설명 + 링크 | Must |
| FR-02 | `docs/usage/think.md` — `/think` 치트 시트 | Must |
| FR-03 | `docs/usage/write.md` — `/write` 치트 시트 | Must |
| FR-04 | `docs/usage/solve.md` — `/solve` 치트 시트 | Must |
| FR-05 | `docs/usage/express.md` — `/express` 치트 시트 | Must |

### 1.2 각 치트 시트 구성 (공통 포맷)

```
# /[커맨드명]

## 문법
/커맨드명 [인자]

## 언제 쓰나
- 상황 1
- 상황 2

## 예시
/커맨드명 예시 입력1
/커맨드명 예시 입력2

## 출력
- 출력 형태 설명
- 저장 경로

## 관련 커맨드
- /다른커맨드
```

### 1.3 비기능 요구사항

| # | 요구사항 |
|---|----------|
| NFR-01 | 각 파일 30줄 이내 (치트 시트 원칙) |
| NFR-02 | Obsidian Frontmatter 포함 (tags, created, type) |
| NFR-03 | 코드 블록으로 커맨드 예시 명확히 표현 |

---

## 2. 범위 (Scope)

### In Scope
- `docs/usage/index.md` — 커맨드 인덱스
- `docs/usage/think.md` — ThinkingAgent 치트 시트
- `docs/usage/write.md` — WritingAgent 치트 시트
- `docs/usage/solve.md` — ProblemSolvingAgent 치트 시트
- `docs/usage/express.md` — ExpressionAgent 치트 시트

### Out of Scope
- 에이전트 내부 동작 원리 설명 (Design 문서에 있음)
- 트러블슈팅 가이드 (v2에서)
- 영어 번역본

---

## 3. 성공 기준 (Success Criteria)

| # | 기준 | 측정 방법 |
|---|------|----------|
| SC-01 | 5개 파일 생성 완료 | 파일 존재 확인 |
| SC-02 | 각 파일 Frontmatter 포함 | 파일 상단 확인 |
| SC-03 | 각 파일 30줄 이내 | wc -l 확인 |
| SC-04 | 모든 커맨드에 2개 이상 실제 예시 포함 | 파일 내용 확인 |
| SC-05 | index.md에서 각 파일로 링크 연결 | 링크 존재 확인 |

---

## 4. 리스크

| # | 리스크 | 대응 |
|---|--------|------|
| R-01 | 치트 시트가 가이드북화 → 30줄 하드캡 적용 | 각 파일 작성 후 줄 수 체크 |
| R-02 | 실제 에이전트 동작과 문서 불일치 | `.claude/skills/*.md` 원본 파일 참조해서 작성 |

---

## 5. 구현 가이드

### 파일 목록
```
docs/usage/
  index.md        # 전체 커맨드 인덱스
  think.md        # /think 치트 시트
  write.md        # /write 치트 시트
  solve.md        # /solve 치트 시트
  express.md      # /express 치트 시트
```

### 작성 순서
1. `.claude/skills/think.md` 읽기 → `docs/usage/think.md` 작성
2. `.claude/skills/write.md` 읽기 → `docs/usage/write.md` 작성
3. `.claude/skills/solve.md` 읽기 → `docs/usage/solve.md` 작성
4. `.claude/skills/express.md` 읽기 → `docs/usage/express.md` 작성
5. `docs/usage/index.md` 작성 (4개 파일 요약 + 링크)

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-06-17 | Initial plan |
