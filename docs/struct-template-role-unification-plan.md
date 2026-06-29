# Struct Template Role Unification Plan
## Patterns as Primary Logic Core + Deliverables as Thin Packaging Profiles

**Status**: Proposed (2026-06-29)  
**Goal**: Reduce overlap/duplication between `struct-docs/templates/patterns/` and `deliverables/`. Make final output mostly follow patterns' logical formats, while deliverables provide type-specific supplements and packaging rules.  
**Principle**: Patterns = "how to reason". Deliverables = "what document shape + what extra rules for this audience/output type".

---

## 1. Current Problems (Observed from Testing & Audit)

| Problem | Evidence | Impact |
|---------|----------|--------|
| Deliverables duplicate pattern internal structure | `deliverable-policy-planning.md` §2.2.1~2.2.4 mirrors `iaej-pattern.md` almost 1:1 | Maintenance drift. Pattern improvement does not automatically apply |
| W1 rule forces deliverable to pre-declare pattern steps | `reference/writing-pipeline.md:13`: "deliverable skeleton의 `(logic: …)` 섹션 제목·하위 단계 제목을 누락 없이 배치" | Deliverable files become "fat" with logic details |
| Review D7 / logicSectionsFilled & L1~L4 compare against deliverable-expanded headings | `review.md:119~139`, `writing-pipeline.md` | Tight coupling; pattern changes require review rule sync |
| "패턴 가이드" duplication at end of almost every file | All patterns + most deliverables | Noise |
| Composition knowledge (which patterns in which deliverable) is scattered | `deliverable-brief-schema.md §2.3`, templates/README matrix, individual deliverable frontmatter | Hard to maintain |
| Many deliverables are mostly "wrapper + 1~3 patterns + type-specific tables" | coordination, meeting-result, event, policy-planning | The "skeleton" label over-promises independence |

**Root cause**: Original orthogonal design (skeleton vs logic) was sound on paper, but most real deliverableTypes turned out to be "canonical pattern compositions + packaging".

---

## 2. Target Model (Agreed Direction)

### 2.1 New Role Definitions

**`patterns/` (Primary Logic Source of Truth)**
- Owns the detailed logical development method and internal step structure.
- Can be used:
  - Standalone (`/struct-write ... template:scqa-pattern` or general case)
  - Embedded via `(logic: xxx-pattern)` anchors
- When embedded: The pattern's **core logic steps** (numbered sections + development guidance) are authoritative. Executive Summary / full document wrapper of the pattern is usually suppressed.
- Examples of good patterns: SCQA, IAEJ, incident-causal (ser/stad), objective-policy, FABE, PREP, case-measure.

**`deliverables/` (Thin Report Type Profile / Packaging Layer)**
- Declares the **high-level document outline** for a deliverableType.
- Declares **composition**: which logic patterns go into which major sections (via `(logic: )` anchors + `logicSections` frontmatter). Keep this high-level — do **not** pre-expand pattern internals.
- Owns **type-specific supplements** that are *not* pure logic:
  - Unique tables (이해당사자 대비표, 쟁점 표, meetingPurpose branches, eventPhase planning vs progress)
  - Placement and rules for 건의/결론/requestedActionRequired
  - subType / meetingPurpose / eventPhase / logicPatternMode branching instructions
  - Audience + submission nuances that differ by report type
  - Frontmatter contract keys specific to the type
- Provides the outer frame (표지 메타, 개요, 참고·첨부, overall section order).

**`shared/`**
- Remains for truly cross-cutting concerns (common meta 표지, submission-vs-working distinction, logicSectionsFilled rules).

**`express/`**
- Unchanged (separate concern).

### 2.2 New Core Rules (to be enforced after migration)

1. **Pattern Authority for Logic**: Inside any `(logic: xxx-pattern)` section, the corresponding `patterns/...-pattern.md` supplies the step headings and development method.
2. **Deliverable provides Container + Context**: Deliverable defines major section titles and the *purpose* of the logic block inside it. It does not duplicate the 4 sub-steps of IAEJ etc.
3. **W1 Skeleton Rule Change**:
   - Non-logic sections: follow deliverable skeleton.
   - `(logic: ...)` sections: read the pattern and place *its* logical steps under the deliverable's container heading. Adapt numbering locally if needed (e.g. 2.2.1 becomes local).
4. **D7 / ST1 Validation**: Validate that the anchor exists + that the expected pattern steps are present and filled (cross-reference the pattern file content when possible). Do not require exact heading text match from an old duplicated copy in the deliverable.
5. **Composition lives primarily in deliverables frontmatter** (`logicSections` list + comments). Orchestrator and review continue to load it from `deliverableTemplate`.

---

## 3. Before / After Example (Policy-Planning §2.2)

### Before (current duplication)
```markdown
### 2.2 원인분석 (logic: iaej-pattern — **필수 전개**)

> **논리 흐름**: 기반구조(I) → 행동(A) → 사건(E) → 판단(J)

#### 2.2.1 기반구조 (Infrastructure)
- {변하지 않는 제도·인프라·환경·구조}
...

#### 2.2.2 행동 (Activity)
...
#### 2.2.3 사건 (Event)
...
#### 2.2.4 판단 (Judge) 및 제언
- **이런 까닭에 →** ...
```

### After (thin deliverable + pattern authority)
```markdown
### 2.2 원인분석 (logic: iaej-pattern — **필수 전개**)

> iaej-pattern의 4단계(기반구조 → 행동 → 사건 → 판단 + '이런 까닭에 →' 제언)를 전개하라.
> 패턴 단계 제목과 구조는 `patterns/reports/iaej-pattern.md`를 따른다.
```

During W2 the writing agent will:
- Place the container `### 2.2 원인분석`
- Inside it, render the iaej-pattern's 1. 기반구조 / 2. 행동 ... (or renumbered as 2.2.1 etc.)
- Fill with content.

Similar cleanup for:
- coordination §3 (SER)
- meeting-result 안건별 (SCQA)
- event A.3 / B.2
- policy-reference §2.2 lite
- meeting-material decision §3.C.4

---

## 4. Phased Implementation Plan

### Phase 0 — Preparation (1~2 days work, no behavior change yet)
- [ ] Create this plan file + review with stakeholders.
- [ ] Add "New Role Contract" section to `struct-docs/templates/README.md`.
- [ ] Update `Claude.md` (templates section) with the agreed principle.
- [ ] Create `struct-docs/templates/patterns/EMBEDDING-GUIDE.md` (how patterns should be consumed when embedded; what to drop vs keep).
- [ ] Snapshot current state (git tag or branch `before-unification`).

**Gate**: Plan approved + docs updated.

### Phase 1 — Make Patterns Embed-Ready (patterns first — low risk)
For every pattern in `patterns/`:
- [ ] Add or strengthen "When embedded inside a deliverable" guidance in the 패턴 가이드 section.
- [ ] Clearly identify the **core logic block** (the numbered development steps) that should be dropped in.
- [ ] Document what should usually be omitted on embed (standalone Executive Summary, standalone 결론 when the deliverable already has one).
- [ ] Add a machine-readable hint if useful (e.g. frontmatter `embeddable: true`, `coreSteps: ["1. ...", ...]` — optional, can start with human comments).

Deliverables that reference the pattern stay unchanged in this phase.

**Deliverables touched lightly**: none (only documentation).

**Gate**: All 8 patterns have clear embedding notes. No behavior change yet.

### Phase 2 — Thin the Deliverables (surgical, per-file)
For each `deliverables/*.md`:

| File | Main cleanup | Keep / Add |
|------|--------------|------------|
| `deliverable-policy-planning.md` | Remove 2.2.1~2.2.4 detailed sub-headings. Reduce §3 and §4 to high-level + anchors | Keep 1~5 major sections, tables in 2.1/2.3/3.1/4.1, 건의 rules, frontmatter logicSections update |
| `deliverable-coordination.md` | Remove detailed §3.1~3.3 Structure/Event/Response expansion | Keep §2 쟁점·이해당사자 tables, §4 건의+선정 이유, §3 container with (logic: incident-causal-pattern, mode=ser) |
| `deliverable-meeting-result.md` | Reduce 안건별 mini-SCQA expansion | Keep 안건별 반복 구조 + `(logic: scqa-pattern)` + optional SER, 제3자 서술 규칙 |
| `deliverable-meeting-material.md` | Reduce §3.C.4 / §3.C.5 expansion | Keep meetingPurpose 3-way branching (info / opinion / decision), unique tables |
| `deliverable-event.md` | Reduce A.3 / B.2 detailed pattern steps | Keep eventPhase branching + planning vs progress tables |
| `deliverable-policy-reference.md` | Remove lite IAEJ sub-steps | Keep "건의 없음" contract, 사례/시사점 섹션 |
| `deliverable-situation-info.md` | Reduce STAD expansion (information case) | Keep subType branching + 5W 도입문 |
| `deliverable-common-meta.md` (shared) | Review for further extraction | — |

Actions per file:
- Strip duplicated internal headings under `(logic: )`.
- Update the anchor comment to point to pattern authority.
- Update frontmatter `logicSections` to higher-level description if needed.
- Remove or trim the duplicated "패턴 가이드" if it is now only in the pattern file.
- Keep "패턴 가이드 (에이전트 참고)" only when it adds deliverable-specific mapping notes.

**Also**:
- Move any remaining common table formats or meta to `shared/` if truly reusable.

**Gate**: All 7 deliverables are "thin". Run a manual diff review. No agent changes yet — tests would still use old rules.

### Phase 3 — Update Core Rules & Agents (highest impact)
Update in this order (dependencies matter):

1. **`reference/writing-pipeline.md`** (biggest)
   - Rewrite "Logic Pattern Embedding" section.
   - Change W1 rule: deliverable provides containers + anchors; patterns provide logic steps inside anchors.
   - Update W2 description: always Read the logicTemplate for the anchor content.
   - Update deliverable별 임베드 앵커 table to high-level only.
   - Add note about suppressing pattern wrapper when embedded.

2. **`reference/review.md`** (D7 section) and **`.claude/agents/review.md`**
   - Update L1~L4 conditions: check for anchor existence + pattern steps presence (read pattern when possible).
   - Update "유형별 필수 단계" tables to be anchor + pattern-based rather than expanded headings from deliverable.
   - Clarify that W4-removed Appendix still requires direct comparison to `deliverableTemplate` frontmatter `logicSections` + pattern.

3. **`reference/deliverable-brief-schema.md`**
   - Update language: "deliverableTemplate defines high-level outline and composition. logicTemplate (pattern) defines the detailed reasoning inside anchors."
   - Clarify "임베드는 deliverable skeleton이 주도" → "deliverable defines placement and context; pattern defines structure and development".

4. **`.claude/agents/orchestrator.md`** (Step DS)
   - Minor: when building Deliverable Spec, note that logic sections will be filled from patterns.
   - Ensure `logicTemplate` resolution remains pattern-first.

5. **`reference/submission-ready-checklist.md`** (ST1)
   - Align ST1 language with new D7 rule (anchor + pattern steps filled).

6. **`.claude/agents/writing.md`** (light)
   - Reference the updated writing-pipeline.md.

7. **`struct-docs/templates/README.md`**
   - Update the big matrix (2D deliverable × pattern).
   - Update "Agent Glob·선택" and "2차원 매트릭스".
   - Add "Thin Profile" explanation for deliverables.

8. **`reference/orchestrator-review-gate.md`**
   - Update Logic embed 기준 로드 description.

**Gate**: All rule files updated. Old duplicated headings in deliverables have been removed in Phase 2, so rules now match reality.

### Phase 4 — Validation & End-to-End Testing (mandatory)
Must-do tests (both modes):

- `/struct-write` for all 7 deliverableTypes (policy-planning, coordination, meeting-material, meeting-result, policy-reference, event, situation/information).
- At least one with `submissionTarget: true` (full W4).
- At least one with complex logic embed (policy-planning).
- At least one meeting-result (multiple anchors).
- Collaborative (step-by-step) + Autonomous.
- Review after write (`verification: both` or default).
- Express follow-up on one (split-1-5).
- Change one pattern (e.g. add a required bullet or Mermaid example in iaej) and verify that a re-generated policy-planning and policy-reference pick it up without editing the deliverable.

**Artifacts to produce**:
- Test output files in `struct-docs/02-writing/` (or temp).
- Before/after comparison notes for 2~3 representative cases.
- Updated "logicSectionsFilled" pass examples.

**Gate**: All tests pass with new behavior. No logic step duplication remains. D7 / ST1 still catch real missing embeds.

### Phase 5 — Documentation, Memory & Cleanup
- [ ] Update all `struct-docs/usage/*.md` (especially write.md, review.md).
- [ ] Update `Claude.md` (templates, writing pipeline, review sections).
- [ ] Update any design docs that hardcode old skeleton assumption (`docs/struct-deliverable-system.design.md`, `docs/struct-deliverable-template-priority.md`).
- [ ] Update `patterns/README.md` if categorization guidance needs refresh.
- [ ] Clean up old comments / duplicated 가이드 in deliverables.
- [ ] Consider adding a small "composition" example or table in templates/README.
- [ ] Revise agent "패턴 가이드" sections if they became redundant.
- [ ] Optional: Add a short "Migration Notes" section in templates/README for future contributors.

**Gate**: All docs consistent. `templates/README.md` is the single source of truth again.

---

## 5. Files Expected to Change (Summary)

**Templates (content structure)**
- `struct-docs/templates/deliverables/*.md` (7 files — thin out)
- `struct-docs/templates/patterns/**/*.md` (8 files — embed guidance)
- `struct-docs/templates/shared/*.md` (possible extraction)
- `struct-docs/templates/README.md`
- `struct-docs/templates/patterns/README.md` + new `EMBEDDING-GUIDE.md`

**Reference (rules)**
- `reference/writing-pipeline.md` (major)
- `reference/deliverable-brief-schema.md`
- `reference/submission-ready-checklist.md`
- `reference/orchestrator-review-gate.md`
- `reference/agent-shared-contract.md` (minor)

**Agents**
- `.claude/agents/writing.md` (light)
- `.claude/agents/review.md` (D7 section)
- `.claude/agents/orchestrator.md` (light)

**Usage & Top-level**
- `struct-docs/usage/write.md`, `review.md`, `index.md`
- `Claude.md`
- `docs/struct-deliverable-system.design.md` (historical note)
- New: `docs/struct-template-role-unification-plan.md` (this file)

---

## 6. Success Criteria (Measurable)

1. **No duplication of logic step headings**: Searching for "기반구조 (Infrastructure)" finds it only inside `iaej-pattern.md` (and generated outputs), not in deliverable templates.
2. **Pattern change propagation**: Editing a pattern's step description or adding a visual example immediately appears in any deliverable that embeds it (verified by test write).
3. **Review still works**: D7 logicSectionsFilled and L1~L4 correctly flag missing or under-filled logic anchors on test cases.
4. **W4 / submissionReady** still produces clean 제출본.
5. **Cognitive simplicity**: A developer reading a deliverable file can see "this is the container + these 2-3 logic blocks are required" in < 30 seconds.
6. **Matrix in templates/README.md** accurately reflects new thin model.

---

## 7. Risks & Mitigations

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| Review / D7 false negatives after heading removal | High initially | Phase 3 updates review rules *after* Phase 2 cleanup. Run full test matrix in Phase 4. |
| W1 produces wrong nesting | Medium | Make W1 rule change explicit with examples in writing-pipeline.md. Add test for numbering. |
| Orchestrator Deliverable Spec or Brief schema language becomes outdated | Medium | Update schema language in Phase 3. |
| Future new deliverableType forgets the thin model | Low | Strong "Role Contract" section in templates/README + EMBEDDING-GUIDE. |
| Express or other downstream assumes old expanded structure | Low | Express works on final Markdown, not templates. |
| Rollback difficulty | Low | Git branch + "before-unification" tag. Phase gates allow stopping. |

**Rollback plan**: If Phase 4 reveals unrecoverable issues, revert to the commit before Phase 2 edits and document learnings.

---

## 8. Recommended Execution Order (Strict)

1. Phase 0 (plan + docs)
2. Phase 1 (patterns embed-ready)
3. Phase 2 (thin deliverables) — commit frequently per file or per 2 files
4. Phase 3 (update rules & agents) — one reference/agent at a time, with small tests
5. **Phase 4 (validation) — do not skip or shorten**
6. Phase 5 (docs sync + cleanup)

Do **not** batch Phase 2+3 without intermediate validation.

---

## 9. Open Decisions (to resolve before or during Phase 0/1)

- Should we add a small `logicSections` YAML or structured comment in deliverable frontmatter for easier machine parsing of composition?
- Do we want a "pattern fragment" output mode (e.g. pattern file can have a `## Embed Fragment` section)?
- How strictly do we suppress the pattern's own "Executive Summary" / "결론" on embed? (Current practice already suppresses a lot.)
- Should `shared/deliverable-common-meta.md` absorb more (e.g. common 건의 wording guidance)?

---

**Next step after plan approval**: Start with Phase 0, then proceed to Phase 1.

This plan turns the observed overlap into a deliberate, cleaner architecture while preserving all existing quality gates (D7, ST1~ST6, W4, Review, Brief).