# reference/

이 디렉토리는 Minto 에이전트 팀이 **런타임에 Read 도구로 참조**하는 핵심 참조 자료를 모아둔 곳입니다.

## 활성 참조 파일 (에이전트가 실제로 사용하는 파일)

에이전트 프롬프트에서 명시적으로 Read를 지시하거나 참조하는 파일만 이곳에 유지합니다.

| 파일명 | 설명 | 주요 참조 에이전트 |
|--------|------|------------------|
| `minto-thinking-core-checklist.md` | Minto Thinking의 Non-negotiables, 6-Step Structured Reasoning, MECE Validation, Critique Pass, Reverse Structuring 등 핵심 규칙 | thinking.md, writing.md, problem-solving.md, expression.md, orchestrator.md |
| `minto-thinking-quality-rubric.md` | Thinking 결과물의 품질 평가 기준 (Excellent / Good / Acceptable / Poor) | thinking.md |
| `minto-thinking-gold-examples.md` | 최고 품질(Excellent) 수준의 예시 모음 | thinking.md |

### 사용 규칙
- 에이전트가 판단이 어려울 때 위 파일들을 `Read` 도구로 적극 참조하도록 설계되어 있습니다.
- 이 3개 파일 외의 문서는 에이전트가 직접 읽지 않습니다.

## 관리 규칙 (향후 유지보수)

1. **루트(reference/)** 에 둘 수 있는 파일
   - 에이전트가 런타임에 `Read` 해야 하는 핵심 규칙/체크리스트/품질 기준/예시
   - 파일이 추가될 때는 반드시 이 README를 함께 업데이트해야 합니다.

2. **archive/** 로 이동해야 하는 파일
   - 개발 과정 문서 (아키텍처 설계, 리뷰 리포트, 경량화 검토 등)
   - 구버전 (thinking_revised.md 등)
   - 에이전트가 직접 참조할 필요가 없는 메타 문서
   - CUSTOMIZATION-GUIDE.md와 같은 외부 링크 문서

3. **절차**
   - 새로운 참조 파일을 만들었을 때 → 루트에 배치 + 이 README 업데이트
   - 더 이상 사용되지 않는 파일이 생기면 → `reference/archive/` 로 이동
   - README의 활성 파일 목록과 archive 목록을 항상 최신으로 유지

## 현재 archive/ 내용 (참고용)

- `CUSTOMIZATION-GUIDE.md`
- `minto-thinking-prompt-architecture.md`
- `minto-thinking-prompt-lightweight-review.md`
- `minto-thinking-quality-reverification.md`
- `thinking_revised.md` (v2 구버전)

이 파일들은 기록 보존 목적으로만 보관되며, 에이전트 프롬프트에서는 참조하지 않습니다.

---

**마지막 업데이트**: 2026-06-18 (에이전트 사용 파일 기준으로 정리)