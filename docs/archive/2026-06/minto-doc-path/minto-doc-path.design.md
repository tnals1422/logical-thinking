# minto-doc-path Design Document

> **Summary**: minto-agent-team 산출물 저장 경로를 `docs/`에서 `minto-docs/`로 분리
>
> **Project**: agent-creator
> **Author**: 박수민
> **Date**: 2026-06-17
> **Status**: Archived
> **Planning Doc**: [minto-doc-path.plan.md](./minto-doc-path.plan.md)

---

## Context Anchor

| Key | Value |
|-----|-------|
| **WHY** | minto-agent-team 산출물과 PDCA 문서가 `docs/`에 혼재하여 구분 불가 |
| **WHO** | agent-creator 프로젝트 사용자 (개인) |
| **RISK** | 기존에 `docs/0X-*/`에 저장된 minto 산출물의 경로 불일치 |
| **SUCCESS** | minto 커맨드 실행 시 산출물이 `minto-docs/` 하위에 저장됨 |
| **SCOPE** | `.claude/agents/` 및 `.claude/skills/` 내 경로 참조 수정 (6개 파일) |

---

## 1. Overview

### 1.1 Design Goals

- `.claude/` 하위 6개 Markdown 파일에서 `docs/` 경로 참조를 `minto-docs/`로 일괄 교체
- `orchestrator.md`의 `## Constraints` 섹션에 경로 분리 원칙을 문서화하여 향후 컨텍스트 보존
- PDCA 관련 파일(`docs/01-plan/`, `docs/02-design/` 등) 미변경 보장

### 1.2 Design Principles

- 최소 변경: 경로 문자열만 교체, 로직 구조 불변
- 명시적 분리: 경로명 자체가 시스템 분리를 표현 (`minto-docs/` vs `docs/`)
- 컨텍스트 보존: 변경 근거를 Constraints에 기록하여 미래 수정 시 혼선 방지

---

## 2. Architecture Options

### 2.0 Architecture Comparison

| 기준 | Option A: 직접 치환 | Option B: 중앙 경로 선언 | Option C: 치환 + 문서화 |
|------|:--:|:--:|:--:|
| **새 파일 수** | 0 | 0 | 0 |
| **수정 파일 수** | 6 | 6 | 6 |
| **복잡도** | Low | Medium | Low |
| **유지보수성** | Medium | Medium | High |
| **노력** | Low | Medium | Low |
| **리스크** | Low | Low | Low |

**Selected: Option C** — 치환의 단순함을 유지하면서 Constraints에 경로 근거를 기록하여 향후 수정 시 컨텍스트를 보존한다.

### 2.1 변경 대상 파일 맵

```
.claude/
├── agents/
│   ├── orchestrator.md   ← docs/ 참조 8건 변경 + Constraints 보강
│   └── writing.md        ← docs/templates/ → minto-docs/templates/ (1건)
└── skills/
    ├── think.md          ← docs/01-thinking/ → minto-docs/01-thinking/ (1건)
    ├── write.md          ← docs/02-writing/  → minto-docs/02-writing/  (1건)
    ├── solve.md          ← docs/03-solving/  → minto-docs/03-solving/  (1건)
    └── express.md        ← docs/04-expressing/ → minto-docs/04-expressing/ (1건)
```

### 2.2 경로 변경 매핑

| 기존 경로 | 변경 후 경로 | 해당 파일 |
|-----------|-------------|-----------|
| `docs/` (출력 루트) | `minto-docs/` | orchestrator.md |
| `docs/01-thinking/` | `minto-docs/01-thinking/` | orchestrator.md, think.md |
| `docs/02-writing/` | `minto-docs/02-writing/` | orchestrator.md, write.md |
| `docs/03-solving/` | `minto-docs/03-solving/` | orchestrator.md, solve.md |
| `docs/04-expressing/` | `minto-docs/04-expressing/` | orchestrator.md, express.md |
| `docs/templates/` | `minto-docs/templates/` | writing.md |
| `docs/{category}` (저장 경로) | `minto-docs/{category}` | orchestrator.md |

---

## 3. 세부 변경 명세 (완료)

모든 항목 ✅ 완료. 상세 내용은 analysis/report 문서 참조.

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 0.1 | 2026-06-17 | Initial draft (Option C 선택) | 박수민 |
| 1.0 | 2026-06-17 | Archived | 박수민 |
