---
tags: [minto, think, v2-verification, grok]
created: 2026-07-15
type: thought
coreClaim: "Grok-only Parent-as-Orchestrator는 orchestrator 자식 스폰을 제거하고 Parent가 오케스트레이션을 인라인 수행한 뒤 specialist만 호출함으로써, Grok Build에서 Struct think 경로를 안정화한다."
verification: V2
---

## CS 분석 (범주·요점)

### 범주 1: 런타임 실패 지점
- 요점: Grok에서 `subagent_type: "orchestrator"` 자식 스폰이 tool requirements 불충족으로 실패 → think 품질 게이트 경로 단절

### 범주 2: 아키텍처 대응 (Parent-as-Orchestrator)
- 요점: Parent(메인)가 Mode·Prior·저장·memory를 인라인 수행하고, specialist만 `spawn_subagent` — orchestrator.md는 프로세스 스펙(SoT), 스폰 대상 아님

### 범주 3: Think 경로 안정화 조건
- 요점: `/struct-think` → thinking specialist 1회 호출 → Markdown 반환 → Parent가 `01-thinking` 저장·`previousThoughts` 갱신

### 범주 4: Host 범위 (Grok-only)
- 요점: Primary runtime을 Grok Build로 고정해 “skill → orchestrator 자식” 이중 경로를 제거, 실패 패턴 재유입 차단

> 추론 보충 범주: **품질 게이트 보존** — spawn 스킵만 하면 Brief/Prior/Review가 빠지므로, Parent 인라인이 “경로 안정화”의 필수 조건임을 보충

**abstractionLevels**: 장애 증상 → 스폰 금지 규칙 → Parent 책임 분리 → think E2E 계약

## GPS 구조화

### 그룹 구조 (typeRationale)
- **Hub + Branch**: 중심 주장은 “think 경로 안정화”; 허브에서 실패 원인·대응 구조·경로 계약·host 범위가 갈라짐.

```
                    [coreClaim]
         Grok-only Parent-as-Orchestrator
              → think 경로 안정화
                        │
        ┌───────────────┼───────────────┬────────────────┐
        ▼               ▼               ▼                ▼
   실패 원인 제거   책임 분리 설계   Think E2E 계약   Host 단일화
  (orch spawn 금지) (Parent 인라인)  (specialist only) (Grok-only)
```

### 비교 분석 (패러렐)

| 축 | 기존 (CC 가정) | L1 (Parent-as-Orchestrator) |
|----|----------------|-----------------------------|
| 진입 | Skill → spawn(orchestrator) | Skill = Parent가 orch 프로세스 수행 |
| 스폰 | orch → specialist | specialist only |
| 실패점 | orch 자식 빌드 실패 | primary 경로에 orch 스폰 **없음** |
| 품질 게이트 | orch 자식에 위임 | Parent 인라인 (Mode·Prior·저장·memory) |
| think 결과 | 경로 단절 가능 | Markdown → Parent 저장 확정 |

### 순서 분석 (시리즈)

```
orch spawn 실패 확인
  → Grok-only 결정 (primary = Grok)
  → Parent-as-Orchestrator 채택 (옵션 D)
  → think: P0~P2 → spawn(thinking) → 저장/memory
  → 경로 안정 (품질 게이트 유지)
```

## 핵심 주장

> Grok-only Parent-as-Orchestrator는 orchestrator 자식 스폰을 제거하고 Parent가 오케스트레이션을 인라인 수행한 뒤 specialist만 호출함으로써, Grok Build에서 Struct think 경로를 안정화한다.

## 피라미드 구조

### 근거 1: 실패 원인을 경로에서 제거한다
- Grok에서 orchestrator 자식 스폰이 tool requirements로 실패하는 것이 think 단절의 직접 원인
- Primary에서 `subagent_type: "orchestrator"`를 **금지**하면 해당 실패점이 구조적으로 소멸
- “스폰 재시도/플랫폼 수정 대기”가 아니라 **경로 재설계**로 안정성을 확보

### 근거 2: Parent 인라인 오케스트레이션이 품질 게이트를 보존한다
- Mode 감지, `.struct-memory.json` R/W, Prior 주입, 결과 저장은 Parent 책임으로 유지
- orchestrator.md는 실행 주체가 아니라 **프로세스 스펙(SoT)** — 로직 재사용, 중첩 스폰 불필요
- Fallback-only(게이트 생략)와 달리 Brief/Prior/Review 의미를 잃지 않음

### 근거 3: Think 경로는 specialist 1회 호출로 계약이 단순·검증 가능하다
- `/struct-think` 계약: Parent 준비 → `spawn(thinking)` → Markdown 반환 → `struct-docs/01-thinking` + memory
- Specialist는 도메인 산출(피라미드)만 담당; 중첩 orchestrator 불필요
- 성공 기준이 명확: primary에 orch 스폰 없음 + thinking 결과 저장 + previousThoughts 갱신

### 근거 4: Grok-only가 이중 호스트 복잡도·실패 재유입을 차단한다
- CC full pipeline을 6개월 비목표로 두면 “skill → orch 자식” 분기가 primary에 남지 않음
- 단일 런타임·단일 오케스트레이션 모델로 유지보수·온보딩 경로가 일치
- Known Issue Closed 조건도 “orch 스폰 성공”이 아니라 “parent+specialist로 품질 게이트 동작”으로 정렬

## MECE 검증

- 결과: **passed**
- 로직트리 규칙: Level 1~2 엄격 MECE / Level 3+ 실용
- 검토:
  - L1 축 = **제거(실패점)** · **보존(게이트)** · **단순화(think 계약)** · **범위(Grok-only)** → 상호 배제
  - 네 축이 “왜 안정화되는가”를 원인·설계·경로·host로 포괄 (CE 누락 없음)
  - singleParent 위반 없음
- issues: 없음 (경미: write/review 경로는 본 think 범위 밖 — 의도적 CE 제한)

### Critique Pass 수행 결과
1. **MECE**: Level 1 4축 중복 없음
2. **논리 지지**: 각 L1 제거 시 coreClaim 지지력 약화
3. **강력 반론**: “Parent 인라인 컨텍스트 비대” → think는 specialist 1회·저장만이라 부담 제한
4. **누락 관점**: Express/Review는 think 안정화 주장의 필수 축 아님
5. **개선 결정**: 재구성 불필요

## 요약

Grok에서 orchestrator 자식 스폰 실패가 think 경로를 끊는다. Parent-as-Orchestrator는 그 스폰을 금지하고 Parent가 게이트를 인라인으로 유지한 채 thinking specialist만 호출한다. Grok-only로 이중 경로를 제거하면 이 안정화가 primary 기본값이 된다.

```
coreClaim (1문장)
├── 1. orch spawn 실패점 제거
├── 2. Parent 인라인으로 품질 게이트 보존
├── 3. think = specialist 1회 단순 계약
└── 4. Grok-only로 이중 호스트·실패 재유입 차단
```

> structuringPath: **reverse** (결론 선행 입력을 역추적 후 정방향 피라미드로 재구성)

## Pyramid Data (for downstream agents)

```json
{
  "coreClaim": "Grok-only Parent-as-Orchestrator는 orchestrator 자식 스폰을 제거하고 Parent가 오케스트레이션을 인라인 수행한 뒤 specialist만 호출함으로써, Grok Build에서 Struct think 경로를 안정화한다.",
  "level1": [
    "orch 자식 스폰 실패점을 primary 경로에서 제거",
    "Parent 인라인 오케스트레이션으로 Mode·Prior·저장·memory 품질 게이트 보존",
    "think는 specialist 1회 호출·Markdown 반환·Parent 저장의 단순 계약",
    "Grok-only로 이중 호스트 분기와 실패 패턴 재유입 차단"
  ],
  "structuringPath": "reverse",
  "meceStatus": "passed",
  "gpsSummary": "Hub+Branch(실패제거·게이트보존·think계약·host단일화) + Parallel(기존 vs L1) + Series(실패→결정→PAO→think E2E)"
}
```
