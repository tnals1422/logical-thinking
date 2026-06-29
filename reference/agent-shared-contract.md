# Agent Shared Contract (Minto Struct Team)

> **목적**: .claude/agents/ 전체에 반복되는 Mode, Brief/Prior/Source 소비, 기본 스타일 규칙을 한 곳에 집중.
> **사용법**: 모든 specialist는 본 파일을 최우선 적용. Orchestrator는 주입 시 이 규칙을 따른다.
> **Non-specialist 내용은 절대 agent별로 재기재 금지.**

## 1. Default Output Style (모든 산출물)

- **개조식 우선**: bullet / sub-bullet / numbered list 중심. 장문 narrative는 최소화.
- **시각 자료 적극**: 표, ASCII/Mermaid diagram (피라미드·흐름·인과).
- **한눈에 파악**: 계층·관계·우선순위 명확.
- 사용자 요청(narrative, 문단식, 도표 없이 등)이 있으면 즉시 반영.

## 2. Mode Contract (두 모드 운영 원칙)

기본은 **Collaborative** (참여형). Orchestrator가 가장 먼저 판단해 specialists에 전달.

### Collaborative (참여형)
- 중간 판단 공개 + 사용자 확인 루프 필수.
- 단계별(또는 주요 포인트)에서 "이 방향 맞습니까?" 확인.
- 판단 근거·대안 공개.
- 최종 저장 전 승인.

### Autonomous (자율형)
- 전체 자동 실행.
- 최소 요약 + 결과만 반환.
- 변경 요약(3~5 bullet) + 저장 경로만 언급.
- "강제" 수준(Tier 1 / DT1 / ST1) 외에는 사용자 확인 생략.

**공통**:
- Mode는 Orchestrator가 `## Mode` 로 주입.
- 두 모드 모두 Default Output Style 적용.
- Prior / Review Feedback 주입 시 모드에 따라 처리 강도 조절 (Collaborative 더 많은 설명+확인).

**Mode별 상세 행동은 각 specialist의 "Mode Handling"에서 최소한으로만 기술**. 본 섹션 기본 원칙을 따른다.

## 3. Consumption Order (write/solve/express 전용)

Orchestrator가 주입할 때 다음 순서로 Context 구성. Specialist는 이 순서대로 우선 적용:

1. **## Brief (Report Operations)** + **## Deliverable Spec** (Phase 1)
   - audience, deliverableType, requestedActionRequired, submissionTarget 등으로 출력 형태·톤·분량·섹션 조절.
   - `deliverableTemplateStatus: available` 시 skeleton 최우선 따름.
   - `summaryDetailRatio: split-1-5` → Express Package 연계.

2. **## Source Validation** (Phase 6, research 선행 시)
   - `corroborated` 주장만 그대로 사용.
   - `unattributed` → "출처 미확인" 또는 `가정:` 명시.
   - `disputed` → 양쪽 병기.
   - Gaps → 표·각주·한계 단락으로 보강.
   - 하단 또는 부록에 `## 출처·가정` 카탈로그 요약 (해당 유형).

3. **## Previous Thinking Pyramid** (또는 Review Feedback)
   - Pyramid Consumption 규칙 적용 (writing은 deliverable별 매핑, solve는 framing 품질 향상, expression은 메시지·근거 매핑).
   - Review Feedback이 있으면 Pyramid Consumption보다 **우선**.
   - `logic_embed.status: fail` (D7) → W2 draft에서 임베드 섹션 보강.
   - Prior 미사용 신호("ignore prior", "raw") 또는 없으면 raw input에서 처음부터 수행.

**공통 주입 지침**:
- `## Mode`, `## Brief`, `## Deliverable Spec`, `## Previous Thinking Pyramid`, `## Source Validation`, `## Review Feedback`, `## Full Report Source` (express package) 순서.
- Specialist는 다른 Agent 호출 금지 (자기 완결).

## 4. Review Feedback 소비 (모든 write/solve/express 후 review 연계)

- Review Feedback 블록이 주입되면 해당 이슈를 최우선 교정.
- DT1 / force_rework / D7 → 강제 수준 반영.
- ST3 (Working 잔존) → W4, ST1 → W2 draft 등 fix_stage 준수.
- autonomous: 변경 요약만 + 강제 시 자동 진행.
- collaborative: 상세 결과 공개 후 확인.

## 5. Output & Frontmatter 기본

- Markdown + Obsidian Frontmatter.
- 필수: `tags`, `created`, `type`, `coreClaim`.
- write 시 추가: `deliverableType`, `audience`, `submissionTarget`, `draftStage`, `submissionReady` (해당 시).
- `submissionTarget: true` (기본): W4 후 **Working 섹션 완전 제거** (`## Audience Pass`, MECE, Pipeline Meta, Appendix 품질, 내부 메타 문장, HTML 주석 등 삭제). `draftStage: submission-ready`, `submissionReady: true`.
- `submissionTarget: false`: W3까지, Working 흔적 유지.
- 저장 경로: Orchestrator가 `struct-docs/{category}/...` 처리.

## 6. Language & Constraints 공통

- 사용자 입력 언어로 출력.
- 가정 필요 시 명시.
- Constraints는 각 agent 파일 끝에 **전문가 고유 제약**만 기재 (공통은 이 파일 참조).

## 7. Reference Files (필요 시 Read)

- Minto 핵심: `reference/minto-thinking-core-checklist.md`
- 품질 기준: `reference/minto-thinking-quality-rubric.md`
- Gold 예시: `reference/minto-thinking-gold-examples.md`
- Brief/Spec: `reference/deliverable-brief-schema.md`
- 제출 게이트: `reference/submission-ready-checklist.md`
- Source: `reference/source-validation-checklist.md`
- 템플릿: `struct-docs/templates/README.md` + shared/
- 표현 패키지: expression 전용 템플릿

**이 파일이 source of truth** for cross-agent behavior. Agent별 파일은 "이 규칙 + 본 agent 고유 로직"만 기술.
