# Minto Thinking Agent — 품질 재검증 보고서

> **일자**: 2026-06-18  
> **목적**: thinking.md 경량화(285 → 154 lines) 이후 기존 산출물의 품질이 유지되는지 Rubric 기준으로 재평가  
> **대상 출력**: minto-docs/01-thinking/ 하위 2개 파일  
> **평가 기준**: `reference/minto-thinking-quality-rubric.md` (v0.1)

---

## 1. Non-negotiables 준수 여부 (전체)

| Non-negotiable | semiconductor-supercycle | fed-warsh-korea-market | 비고 |
|----------------|---------------------------|-------------------------|------|
| coreClaim 정확히 1문장 | ✅ 통과 | ✅ 통과 | 둘 다 단일 문장 |
| Level 1 2~5개 | ✅ 4개 | ✅ 3개 | 양호 |
| Level 1 ME/CE 없음 | ✅ 통과 (상세 검증) | ✅ 통과 | - |
| 피라미드 ≥2레벨 | ✅ | ✅ | - |
| MECE 검증 수행 + 명시 | ✅ 상세 | ✅ 명시 | - |
| 역방향 시 structuringPath 기록 | N/A (정방향) | N/A | - |

**결론**: 두 출력 모두 Non-negotiables를 위반하지 않음. 경량화 전후로 이 부분은 안정적.

---

## 2. 개별 출력 상세 평가

### 출력 1: 20260618-semiconductor-supercycle-portfolio-strategy.md

**입력 특징**: 한국경제TV 판도라 방송 내용 — 반도체 슈퍼사이클 + 금리/지정학 리스크 복합 주제. 비교적 복잡.

**평가**:

| 차원 | 등급 | 근거 |
|------|------|------|
| **CS Analysis Quality** | **Excellent** | 6개 범주로 세밀 분해. "거시 리스크 내성 판단"을 inferredCategories로 명시적으로 보충 + 근거 설명. 추상화 수준 우수. |
| **GPS Structuring Quality** | **Excellent** | Branch 구조도 + Parallel(금리 이슈 비교표) 훌륭 + Series(의사결정 순서 + gaps 탐지). typeRationale이 구체적. |
| **Pyramid Integrity** | **Good** | coreClaim 1문장 명확. Level 1 4개. 상위가 하위 요약. 다만 Level 1이 4개라 약간 복잡해 보일 수 있음. |
| **MECE Rigor** | **Good** | 상세 검증 수행. 근거 간 경계 설명 + 한 가지 누락 관점(매도 타이밍)을 명시하고 흡수 처리. |
| **Logical Soundness** | **Good** | 인과관계 명확. 반론 가능 지점(유증/IPO 신호)을 issues에 반영. |
| **Transparency & Usefulness** | **Excellent** | typeRationale, gaps, issues, inferred 모두 실질적 내용. 투자 의사결정에 바로 활용 가능. |
| **Reverse Structuring** | N/A | 정방향 |

**전체 등급**: **Excellent** (7차원 중 3 Excellent + 4 Good, Non-negotiable 0 위반)

**강점**:
- GPS Parallel 표가 특히 훌륭함 (실전적 가치 높음)
- MECE 검증이 형식적이 아닌 실질적 분석 포함
- inferredCategories를 제대로 활용

**약점**:
- Pyramid Level 1이 4개라 약간 산만할 수 있음 (3개가 더 깔끔했을 수도)

---

### 출력 2: 20260618-fed-warsh-korea-market.md

**입력 특징**: Fed Warsh 체제 + 금리 + 한국 시장 영향 분석.

**평가**:

| 차원 | 등급 | 근거 |
|------|------|------|
| **CS Analysis Quality** | **Acceptable** | 출력에 CS 분석 섹션 자체가 없음. 내부적으로는 수행했을 수 있으나 사용자에게 드러나지 않음. |
| **GPS Structuring Quality** | **Poor** | GPS 구조화 섹션 없음. 구조적 사고 과정이 사용자에게 보이지 않음. |
| **Pyramid Integrity** | **Good** | coreClaim 1문장. Level 1 3개. 상위-하위 연결은 양호. |
| **MECE Rigor** | **Good** | MECE 통과 명시 + 간단한 검증 내역 있음. |
| **Logical Soundness** | **Good** | 인과 흐름 설명 + "AI 민감도 감소"에 대한 가정 명시. |
| **Transparency & Usefulness** | **Acceptable** | 핵심 주장은 명확하나, "어떻게 그런 결론에 도달했는가" (CS/GPS 과정)가 생략되어 투명성이 떨어짐. |
| **Reverse Structuring** | N/A | - |

**전체 등급**: **Acceptable ~ Good** (Non-negotiable은 통과했으나, 출력 포맷 준수도가 낮음)

**문제점**:
- User-Facing Output Format에서 요구하는 **CS 분석**과 **GPS 구조화** 섹션이 누락됨.
- 이는 `/minto-think` 호출 시 기대되는 "사고 과정 공개" 가치가 크게 손실된 형태.
- 경량화 이전에도 이미 이런 출력이 나왔다는 점은, **프롬프트가 "CS/GPS는 항상 출력에 포함"하라는 지시를 충분히 강제하지 못하고 있음**을 시사.

---

## 3. condensed thinking.md vs 이전 버전 품질 위험 분석

### 여전히 강하게 유지된 부분 (경량화 성공)
- Non-negotiables 최상단 배치
- Mandatory Critique Pass ("직후 무조건", "생략하면 생성하지 않는다")
- coreClaim 1문장, Level 1 2~5개, 상위 MECE 엄격 반복 강조
- Reference Materials Read 지시 (core-checklist, rubric, gold-examples)

### 잠재적 위험 / 약화된 부분
1. **CS/GPS 출력 강제력 저하**
   - User-Facing Output Format 템플릿은 여전히 포함하고 있으나, "반드시 출력하라"는 강조가 이전보다 약함.
   - 결과: fed-warsh처럼 CS/GPS를 생략한 출력이 나올 여지가 있음.

2. **Self-Check와 Critique Pass의 실행 강제**
   - 이전 버전은 각 Step마다 상세 Self-Check + 별도 Critique Pass 섹션이 길었음.
   - 현재는 매우 압축. 모델이 실제로 "무조건" 실행할 확률이 약간 낮아질 수 있음.

3. **typeRationale 구체성**
   - "1문장 이상 구체적으로"는 여전히 있으나, 예시가 적어 모델이 형식적으로 처리할 위험이 있음.

4. **긴 입력에서의 상세도**
   - semiconductor 출력은 복잡 주제에서도 높은 품질을 보였으나, 이는 **이전 긴 프롬프트**로 생성된 결과일 가능성이 높음.

---

## 4. 전체 종합 평가

| 지표 | 평가 | 설명 |
|------|------|------|
| Non-negotiables 준수율 | 양호 | 두 출력 모두 통과 |
| Excellent 수준 출력 비율 | 현재 1/2 (50%) | 목표(30%+)는 달성 중이지만 샘플 적음 |
| 출력 포맷 일관성 (CS/GPS 포함) | 불안정 | 한 출력은 우수, 한 출력은 누락 |
| 투명성 (사고 과정 공개) | 편차 큼 | GPS/Parallel 같은 고부가가치 요소가 선택적으로만 나옴 |
| Critique Pass 증거 | 간접적 | MECE 검증은 있으나 "재구성 시도" 기록은 없음 |

**현재 품질 수준**: **Mixed (Acceptable ~ Excellent)**

경량화 자체가 품질을 크게 떨어뜨린 증거는 아직 없으나, **기존에도 존재하던 "일관성 문제"**가 여전히 해결되지 않았고, 경량화로 인해 이 문제가 더 악화될 위험이 있다.

---

## 5. 추천 조치 (condensed prompt 개선)

### 즉시 권장 (High Priority)
1. **User-Facing Output Format** 앞에 다음을 추가:
   ```
   ## 출력 필수 구조
   /minto-think 결과는 아래 구조를 **반드시** 따라야 한다:
   - CS 분석
   - GPS 구조화 (ASCII + Parallel/Series 필요 시)
   - 핵심 주장
   - 피라미드 구조
   - MECE 검증
   - 요약
   CS/GPS 생략은 금지 (전문 에이전트 내부 사용 시에만 압축 허용).
   ```

2. **Mandatory Critique Pass**를 더 강조:
   - "Critique Pass를 수행한 흔적(issues, 개선 여부)을 MECE 섹션에 반드시 남겨라."

3. **typeRationale** 강제 문구 강화:
   - "typeRationale은 '서로 다르기 때문' 같은 형식적 표현을 절대 사용하지 말고, 구체적 판단 근거를 1문장 이상 써라."

### 중기 권장
- thinking.md에 **1~2개의 고품질 풀 예시** (간단 버전)를 다시 넣는 것을 고려 (현재는 거의 제거됨).
- orchestrator 또는 skill에서 "think" 호출 시 "full thinking mode" 플래그를 전달하는 방식 검토.

### 검증 방법 개선
- 매번 새로운 /minto-think 호출 후 이 Rubric으로 자동/수동 평가하는 프로세스 정립.
- `reference/minto-thinking-quality-reverification.md`를 누적 관리.

---

## 6. 결론

- **Non-negotiables**와 **Pyramid + MECE 기본 품질**은 경량화 후에도 잘 유지되고 있다.
- 가장 큰 문제는 **"사고 과정(CS/GPS)의 투명한 공개"**가 출력에서 일관되게 나타나지 않는다는 점이며, 이는 경량화 이전부터 존재하던 문제다.
- condensed prompt는 **핵심 제약은 잘 살렸으나**, "무엇을 반드시 출력으로 드러낼 것인가"에 대한 지시가 약화되었다.

**다음 단계 추천**:
1. 위 "즉시 권장" 1~3번을 thinking.md에 반영 (소량 추가).
2. 신규 테스트 입력 2~3개로 직접 /minto-think 실행 후 재평가.
3. 우수 출력(반도체 스타일)을 gold-examples에 추가 고려.

---

## 7. 재검증 후 즉시 조치 (2026-06-18)

재검증 과정에서 발견된 "CS/GPS 출력 누락" 및 "Critique Pass 흔적 부족" 문제를 완화하기 위해 다음을 thinking.md에 추가 반영:

1. User-Facing Output Format 앞에 **"CS 분석과 GPS 구조화 생략 금지"** 명시 (direct think 호출 시).
2. MECE 검증 섹션에 "Critique Pass 수행 결과 (개선/재구성 여부 포함)" 항목 추가.

이 조치로 출력 포맷 일관성과 Critique Pass 증거 기록이 강화될 것으로 기대.

**현재 thinking.md 라인 수**: 156 lines (경량화 후 소량 보강).

---

**검증자**: Grok (경량화 작업 후)  
**참고 파일**:
- `reference/minto-thinking-quality-rubric.md`
- `reference/minto-thinking-core-checklist.md`
- `.claude/agents/thinking.md` (154 lines 버전)