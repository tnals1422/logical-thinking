# Orchestrator — Review Gate & Regeneration Control

> **목적**: Review Gate 실행 조건, Step R1~R4, force_rework 규칙, Review Feedback 블록 생성 상세를 중앙화.
> **사용**: orchestrator.md는 고수준 흐름만 유지하고 상세는 이 파일 Read.

## Review 게이트 실행 조건

- 명시적 review 명령
- 사용자 입력에 "review", "검증", "fidelity", "점검", "마지막 확인" 등이 포함
- write/solve/express 옵션에 `review: true` 또는 `auto-regenerate` 관련 플래그
- Autonomous 모드에서 "review까지" 또는 고품질 요구가 감지된 경우 (선택적)

## Step R1: 대상 및 Source·Brief 결정

- review 대상 파일 경로를 결정 (명시적 경로 > 최근 write/solve/express savedTo)
- 관련 prior thinking을 `.struct-memory.json`의 `previousThoughts` 또는 대상 파일의 출처 정보에서 로드.
- **Brief 복원** (Phase 4): `briefs` 배열에서 `linkedDocument`가 대상과 일치하는 항목 → `## Brief` 블록 재구성; 없으면 대상 frontmatter `deliverableType`·`audience`·`submissionTarget`으로 최소 Brief
- 대상 파일·prior·(가능 시) 최근 write의 Deliverable Spec 맥락을 Read.
- **Logic embed 기준 로드**: `deliverableTemplate` Read (high-level anchors) + 해당 `logicTemplate`(패턴) Read — review에 D7 검증용 전달 (Phase 3: patterns가 단계 권위).
- **DT-Submission 기준 로드** (write + `submissionTarget: true`): `reference/submission-ready-checklist.md` + `shared/submission-vs-working.md` — review 프롬프트에 ST1~ST6 체크 지시.

## Step R2: Review Agent 호출

- `subagent_type: "review"` 호출
- 프롬프트에 다음 포함 (요약):

```
## Task
review 수행

## Verification Type
{ thinking-compliance | writing-consumption | deliverable-quality | both | fidelity | full | source-quality | deliverable+source }

## Target
{대상 파일 전체 내용 또는 경로 + Read 결과 요약}

## Brief (Report Operations)
{...}

## Deliverable Spec
{...}

## Previous Thinking Pyramid (필요 시)
{...}

## Mode
{collaborative | autonomous}

## Logic Pattern Embed Check (필수 시)
D7 ... fix_stage W2 draft

## DT-Submission Check (submissionTarget true 시)
ST1~ST6 ...

## Output Requirements
검증 유형에 맞는 Report + ... + Review Data JSON 필수
```

**Verification Type 기본값 (Phase 4)**:
- think: thinking-compliance
- write (prior): both
- write (no prior): deliverable-quality
- solve / express: deliverable-quality
- information/policy-planning + 출처: deliverable+source
- research: source-quality

## Step R3: Review 결과 처리

- Review 반환 후 `## Review Data` JSON 파싱 (fidelity, compliance, deliverableQuality, logicPatternEmbedding, submissionReady, force_rework, ... 추출)
- logicPatternEmbedding.status === "fail" 또는 logicSectionsFilled === "fail" → DT1 D7, force_rework true
- submissionReady fail → DT-Submission fail, force_rework true
- 저장: struct-docs/05-reviewing/...

## Step R4: 재생성 제어 (Orchestrator 전용)

Orchestrator는 Review 판단 + Mode + Tier에 따라 최종 강도 결정.

### force_rework / 재호출 결정 규칙

| 상황 | Autonomous | Collaborative |
|------|------------|---------------|
| Tier 1 / Deliverable DT1 또는 force_rework true | 강제 자동 재호출 | 강제 권고 (사용자 확인 후 재호출) |
| D7 logicSectionsFilled fail | 강제 write 재호출 (W2 draft) | 강제 권고 |
| DT-Submission submissionReady fail (ST3/ST4) | 강제 (W4 external-face) | 강제 권고 |
| DT-Submission ST1 (logic) | 강제 (W2 draft) | 강제 권고 |
| 기타 DT2 / Tier 2 결합 | 강제 고려 | 권고~강제 권고 |
| Tier 2 미준수만 | 권고 | 권고 |

**추가 규칙**:
- force_rework true 또는 "강제" 명시 시 기본 존중.
- Autonomous: Tier 1 우선 강제.
- Collaborative: Tier 1이라도 사용자 veto 기회 ("강제 권고" 명확 표시).
- 단일 Tier 2만 → 거의 항상 권고.

### 재생성 수행 방식

1. Review Feedback 블록 생성 (예시 구조):

```
## Review Feedback
compliance: { thinking/writing: {tier, issues} }
deliverable: { status, dt1, failures, fix_stage }
logic_embed: { status: fail, logicSectionsFilled: fail, missing_sections: [...], fix_stage: "W2 draft", instruction: "..." }
submission: { status, submissionReady, st_violations, fix_stage }
fidelity: ...
recommendation_strength: "강제" | "권고"
```

2. 해당 specialist를 **같은 커맨드**로 재호출.
3. 기존 Prior + 위 블록 주입.

- Autonomous: 주요 이슈 + "강제/권고" 한 줄 + 변경 요약.
- Collaborative: 상세 결과 + 구분 제시 + 확인.

**중요**: Review 게이트는 **Orchestrator만** 제어. review agent는 재생성 직접 호출 금지.

참조: `reference/agent-shared-contract.md`, review.md, submission-ready-checklist.md
