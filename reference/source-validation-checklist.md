# Source Validation Checklist

Struct Deliverable System Phase 6 기준. research agent·review `source-quality` 검증의 공통 기준.

> 설계: `docs/struct-deliverable-system.design.md` §2.1 F3 (읽을수록 의문 — 출처 불명)  
> 참고: `reference/president/대통령 보고서.md` — 근거·균형·출처 명시

---

## 1. 범위

- **입력**: 사용자 제공 자료·URL·인용·수치·주장 (웹 자동 수집 **아님** — Phase 6는 체크리스트·구조화 검증)
- **출력**: Source Validation Report + `## Source Validation` Context 블록 (downstream 주입)
- **연계**: write/solve 전처리 · review `source-quality` · Deliverable F3 보조

---

## 2. 검증 차원 (5항)

| # | 차원 | 질문 | pass 기준 |
|---|------|------|-----------|
| **S1** | 출처 명시 | 핵심 주장·수치에 출처가 있는가? | 주장별 1:1 출처 또는 `출처 미확인` 명시 |
| **S2** | 다중 출처 | 단일 출처 편향이 없는가? | 핵심 주장에 **2개 이상** 독립 출처 또는 교차검증 기록 |
| **S3** | 균형 | 반대·대안 관점이 반영됐는가? | 쟁점 주제에 반론·한계 1블록 이상 (reference-only는 중립 서술) |
| **S4** | 시의성·권위 | 자료가 주제에 적합한가? | 시점·기관·1차/2차 구분 기록 |
| **S5** | 인용 정합 | 본문 인용이 출처와 일치하는가? | 왜곡·과장 없음; 불일치 시 `needs_clarification` |

---

## 3. Tier (강도)

| Tier | 대상 | 강도 | force_rework |
|------|------|------|--------------|
| **ST1** | S1 핵심 주장 무출처 | 매우 엄격 | policy/information + 수치 주장 시 강제 |
| **ST2** | S2·S3·S4 미달 | 중간 | 단일 출처 편향 · 반론 전무 |
| **ST3** | S5·메타 불완전 | 관대 | 출처 형식 미흡 — 권고 |

---

## 4. Context 주입 형식 (Orchestrator → write/solve)

```markdown
## Source Validation (from research)
sourceReport: struct-docs/06-researching/...
overall: pass | partial | fail
claimsCatalogued: {n}
sourcesCatalogued: {n}

Validated claims:
- claim: "{주장}"
  sources: ["{출처1}", "{출처2}"]
  status: corroborated | single-source | unattributed | disputed

Gaps (downstream 반영 필수):
- {미확인 주장·추가 출처 필요}

Balance notes:
- {반론·한계 요약}

Instruction:
인용·수치는 위 catalog를 우선 사용. unattributed는 본문에 가정 또는 '출처 미확인' 표기. disputed는 양쪽 병기.
```

---

## 5. Review 연계

- Verification type: `source-quality` (단독) 또는 deliverable `F3` 보조
- Review Data: `sourceQuality`, `sourceTiers.st1_violations`

---

## 6. 유형별 최소 기대

| deliverableType | S1 | S2 | S3 |
|-----------------|----|----|-----|
| `policy-planning` | 필수 | 권장 2+ | 반론·대안 균형 |
| `information` | 필수 | 필수 2+ | 쟁점 양면 |
| `policy-reference` | 필수 | 다수 사례 | 중립 |
| `situation` | 사실별 출처 | 해당 시 | 해당 시 |
| `general` | 핵심 주장 | 선택 | 선택 |