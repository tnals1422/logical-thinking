# Minto Thinking Reuse & Handoff Improvement — Implementation Roadmap

**목표**: `thinking.md`의 고품질 Minto 피라미드 결과물이 `writing.md`, `problem-solving.md` (그리고 `expression.md`)에서 신뢰성 있게 재사용될 수 있도록 아키텍처를 개선한다.

**배경**: 
- `/think` 결과는 우수한 분석 산출물(Thinking Artifact)을 생성하지만, downstream 에이전트는 항상 raw input에서부터 thinking을 재수행한다.
- Orchestrator context 전달이 손실적이다 (coreClaim + 짧은 pyramidSummary만).
- usage 문서의 의도("think로 정리한 생각을 문서로 만들 때")와 실제 구현이 불일치.

**아키텍처 원칙**:
1. Thinking = 재사용 가능한 Analysis Artifact
2. Specialists = Transformer (prior pyramid가 있으면 변환, 없으면 self-contained thinking)
3. Orchestrator = Context & Artifact Router (Read를 적극 사용해 실제 내용 주입)
4. Backward compatible: 기존 standalone 호출은 그대로 동작해야 함
5. Write와 Solve는 소비 방식이 다름 (Write: 직접 매핑, Solve: 문제 구조화 품질 차용)

**참고 산출물**:
- 현재 유일한 post-개선 증거: `minto-docs/01-thinking/20260618-hyundai-physical-ai-kospi-three-horses.md` + writing report

---

## Phase 1: Core Handoff Infrastructure (즉시, 높은 ROI)

### 1.1 thinking.md 출력 포맷 강화 (소비 최적화)
- [x] User-Facing Output Format 끝에 `## Pyramid Data (for downstream agents)` 섹션 추가
- [x] `coreClaim`, `level1` (배열), `structuringType`, `meceStatus`를 쉽게 추출 가능한 형태로 제공
- [x] 기존 풍부한 CS/GPS/피라미드/MECE 섹션은 그대로 유지 (사람용)
- [x] Non-negotiables 위반 없이 추가 (추가 섹션은 부가 정보)

### 1.2 Memory 모델 강화 (ThoughtEntry)
- [x] `.minto-memory.json` ThoughtEntry 스키마 확장
  - `level1Claims: string[]`
  - `structuringPath: "forward" | "reverse"`
  - `meceStatus: "passed" | "failed" | "partial"`
  - `savedTo` 유지
- [x] orchestrator.md의 "think 결과" 메모리 업데이트 로직 수정
- [x] 기존 데이터와 호환성 유지 (선택 필드 + pyramidSummary 유지)

### 1.3 Orchestrator — Context & Artifact Router 강화 (가장 핵심)
- [x] Context 준비 로직에 다음 추가:
  - 이전 `previousThoughts`가 존재하고 write/solve/express 호출 시 관련성 판단
  - `Read` 도구로 `savedTo` 파일 실제 내용 로드
  - Context에 구조화된 블록 주입 (## Previous Thinking Pyramid + Instruction)
- [x] "이전 컨텍스트 없음" 케이스에 대한 fallback 명확히
- [x] Orchestrator 프롬프트 템플릿 + Responsibilities + Process 상세 지침 업데이트

### 1.4 writing.md에 Pyramid Consumption Contract 도입
- [x] `## Pyramid Consumption (Prior Thinking 활용 규칙)` 섹션 신규 작성
- [x] Prior pyramid가 있으면:
  - coreClaim → 4.1 핵심 주장 (우선 사용)
  - Level 1 → 4.2 근거 구조 기반
  - GPS 산출물(표, 시퀀스) 적극 재활용
  - Situation/Complication은 narrative 보강 역할
- [x] Prior가 없을 때만 기존 "Integrated Thinking" 실행
- [x] 투명성 및 prior 활용 규칙 상세 명시

### 1.5 Skills 및 호출 인터페이스 보강 (minto-write 중심)
- [x] `minto-write/SKILL.md` Context 전달 부분을 더 구체적으로 업데이트
- [x] Orchestrator가 Prior Thinking Pyramid를 주입한다는 점 명시
- [x] Fallback 로직에도 영향 최소화 (기본 동작 유지)

### 1.6 Phase 1 검증 체크리스트
- [x] thinking.md에 Pyramid Data 섹션 추가 완료 (1.1)
- [x] orchestrator에 Read + Prior Thinking Pyramid 주입 로직 상세 지침 추가 (프롬프트/설명 수준, 1.3)
- [x] writing.md에 Pyramid Consumption 규칙 명시 (1.4)
- [x] memory ThoughtEntry 스키마에 level1Claims 등 확장 (1.2)
- [x] 기존 standalone 호출 하위 호환성 유지 (기존 로직 건드리지 않음)
- [x] minto-write / minto-solve SKILL.md도 context 주입 관련 안내 보강

**Phase 1 구현 완료 (2026-06-18)**: 핵심 handoff 인프라 (thinking 출력, memory, orchestrator router, writing consumption contract) 구축. 실제 Read 실행은 orchestrator가 호출될 때 런타임에 동작.

---

## Phase 2: Full Coverage & Documentation

### 2.1 problem-solving.md에 소비 계약 추가
- [x] `## Prior Thinking 활용 규칙 (Problem Framing)` 섹션 추가
- [x] 소비 방식 정의:
  - thinking 결과물을 "주장"이 아니라 문제의 구조/원인 축으로 활용
  - CS 분석 + GPS MECE 엄격성을 문제 정의 단계에 차용
  - 가설 생성 시 Level 1을 가능한 원인 후보로 참고 (직접 이식 금지 명시)
- [x] 가설 MECE와 thinking의 MECE를 연결하는 지침 포함

### 2.2 expression.md 보강 (일관성)
- [x] Prior thinking 활용 규칙 추가 (coreClaim → 핵심 메시지, Level 1 → 근거 매핑)

### 2.3 Orchestrator 로직 완성
- [x] 실제 Read 호출 예시와 에러 처리 (파일 없음, 오래된 파일 등)
- [x] 관련성 판단 로직 강화 (timestamp + 키워드 겹침 + 최근 우선)
- [x] 여러 이전 thinking 중 가장 최근/관련성 높은 것 선택 규칙 (명확한 우선순위 정의)

### 2.4 Documentation 업데이트
- [x] `minto-docs/usage/write.md`, `think.md`, `solve.md`에 권장 워크플로우 반영
- [x] `CLAUDE.md` (프로젝트 루트) 업데이트 완료
- [x] `reference/minto-thinking-core-checklist.md` 에 "Downstream Consumption" 가이드 추가

### 2.5 Phase 2 검증
- [x] problem-solving이 prior를 올바른 방식으로 소비하도록 명시 (Problem Framing 규칙 상세)
- [x] 실제 파일 기반 context 주입이 orchestrator 프롬프트에 상세 절차 + Read + 에러처리 + 구체 예시로 잘 나타남
- [x] 모든 usage 문서 + CLAUDE.md + core-checklist 일치하도록 업데이트 완료

---

## Phase 3: Advanced Features & Polish

### 3.1 Skill 레벨 옵션 지원
- [x] minto-write, minto-solve 스킬에 옵션 추가 예시: `use-prior:true` 또는 `source: thinking|raw`
- [x] Orchestrator 옵션 해석 로직 (Step 0: 옵션 파싱) 추가

### 3.2 더 정교한 자동 연결
- [x] Escape hatch 지원 (writing에 "ignore prior" 명시 규칙 추가)
- [x] 주제 유사도 자동 판단 고도화 (키워드 점수 + 맥락 유사 계산, 옵션 존중)

### 3.3 Expression까지 완전 일관 적용 (기본 완료)
- [x] expression.md에 Prior 활용 규칙 추가

### 3.4 품질 가드레일
- [x] writing consumption 규칙에 "MECE 검증은 여전히 수행" 명시
- [x] problem-solving에도 Critique Pass 연계 명시
- [x] 더 명시적 Critique Pass 연계 완료 (prior 사용 시에도 수행 강제 + 기록)

### 3.5 테스트 / 예시 산출물 가이드
- [x] `docs/minto-thinking-reuse-examples.md` 신규 작성 완료
- [x] usage 문서에 워크플로우 반영 완료

### 3.6 Phase 3 완료 체크
- [x] Escape hatch + expression 일관성 기본 적용
- [x] 주요 문서화 완료 (usage + CLAUDE.md + examples + architecture ref)
- [x] 옵션 파싱 + 주제 유사도 고도화 + 품질 가드레일 완료
- [x] End-to-End 가이드 문서(`minto-thinking-reuse-examples.md`)로 검증 기준 문서화 (실제 호출 검증은 사용자 환경에서 수행 권장)

---

## 추가 작업 항목 (Cross-cutting)

- [x] `minto-express/SKILL.md` 업데이트 완료 (옵션 + prior pyramid 주입 안내 추가)
- [x] 하위 호환성 유지 (모든 변경은 additive)
- [x] Reference 파일 접근성 개선 (orchestrator enrichment 시 core-checklist Read 고려 주석 추가)
- [x] 아키텍처 업데이트 기록: `reference/minto-thinking-prompt-architecture.md`에 Phase 3 내용 추가

**전체 구현 현황 (2026-06-18)**:
- Phase 1 (Core Handoff): 완료
- Phase 2 (Full Coverage + Docs): 완료
- **Phase 3 (Advanced Features & Polish): 완료**
  - 3.1: 스킬 레벨 use-prior 옵션 + Orchestrator 파싱
  - 3.2: 주제 유사도 판단 고도화
  - 3.4: 명시적 Critique Pass 가드레일
  - 3.5: `docs/minto-thinking-reuse-examples.md` 작성
  - Cross-cutting: reference 접근성 + 아키텍처 문서 업데이트

---

## 성공 기준 (전체 완료 시)

1. `/think` 수행 후 바로 `/write` (같은 주제) 호출 시, writing 결과물의 핵심 주장과 Level 1 근거가 thinking pyramid를 기반으로 생성됨.
2. Orchestrator context에 항상 `## Previous Thinking Pyramid` 블록이 주입 (해당 시).
3. writing / problem-solving 프롬프트에 "prior가 있을 때 어떻게 할지" 명확한 규칙 존재.
4. Standalone 호출 (`/write`만) 은 기존과 동일하게 동작.
5. Memory에 구조화된 pyramid 정보 저장.
6. 모든 주요 문서(agents, skills, usage, CLAUDE.md)가 업데이트됨.

---

## 실행 추적

이 파일의 체크박스를 실제 작업 완료 시점에 업데이트합니다.

**시작일**: 2026-06-18 (현재 세션)

**현재 단계**: Phase 3 Advanced Features & Polish 완료 (2026-06-18)

**전체 로드맵 구현 상태**: 대부분 완료. 실제 동작 검증은 후속 `/think` + `/write` 호출로 확인 권장.

---

## 참고 링크
- Agents: `.claude/agents/{thinking,writing,problem-solving,expression,orchestrator}.md`
- Skills: `.claude/skills/minto-{think,write,solve,express}/SKILL.md`
- Memory schema: orchestrator.md 내부 정의
- 기존 설계: `docs/archive/2026-06/minto-agent-team/`
- Evidence: `minto-docs/01-thinking/20260618-hyundai-physical-ai-kospi-three-horses.md` + report
