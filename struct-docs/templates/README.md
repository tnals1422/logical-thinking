# Struct Write Templates

`struct-docs/templates/` 에 모여 있는 **writing agent 전용 문서 템플릿** 모음.

에이전트는 Glob → 내용 분석 → 가장 적합한 템플릿 Read → 구조 정확히 따르기 순으로 사용한다.

## 템플릿 목록 및 선택 기준

| 파일명                          | 패턴 이름                  | useWhen (언제 선택)                                      | 핵심 구조                     |
|--------------------------------|----------------------------|----------------------------------------------------------|-------------------------------|
| `report-default.md`                 | 기본 SCQA 보고서                  | 대부분의 일반 분석·보고서·제안. 가장 안전한 기본형       | 상황 → 문제 → 질문 → 답변     |
| `iaej-pattern.md`                   | I-A-E-J (기반-행동-사건-판단)     | 제안 중심, 계층적 원인 분석, '이런 까닭에 →' 논리가 필요한 경우 | 기반구조 → 행동 → 사건 → 판단 |
| `structure-event-response-pattern.md`      | Structure-Event-Response (구조-사건-대처) | 과거 사건 기록, '구조는 그대로인데 사건이 일어났고 대처했다'는 분석 | 구조 / 사건 / 대처 (3단)      |
| `state-trigger-accident-damage-pattern.md` | State-Trigger-Accident-Damage (상태-트리거-사고-손해) | 복잡한 인과관계, '평소 상태 + 작은 트리거 → 사고 → 손해' 분석 및 대책 수립 | 상태 → 트리거 → 사고 → 손해 + 대책 매핑 |
| `objective-policy-pattern.md`       | Objective-Policy (목적-방침)      | 행동 계획·방침·실행 항목을 "왜(목적) + 어떻게(방침)" 쌍으로 정리할 때 | 번호 항목 + 목적/방침 쌍      |

## Agent 사용 규칙 (중요)

1. **항상 Glob 먼저**
   ```
   Glob: struct-docs/templates/*.md
   ```

2. **pyramid (또는 입력) 분석 후 위 표 기준으로 선택**
   - 모호하면 `report-default.md` 우선.

3. **선택 후 반드시 Read**
   - 템플릿 파일 전체를 읽고, **최상단 skeleton(제목 + 섹션 구조 + placeholder)** 을 정확히 따른다.
   - 하단의 "패턴 가이드" 섹션은 참고용이며, 최종 출력에는 포함하지 않는다.

4. **출력에 반드시 포함**
   - 사용한 템플릿 파일명 (autonomous: 한 줄, collaborative: 선택 이유와 함께)
   - MECE 검증 결과 (대부분의 템플릿에서 권장)

5. **모드별 동작**
   - Collaborative: "이 내용에는 [패턴]이 적합합니다. (이유) 이 템플릿으로 진행할까요?" 확인
   - Autonomous: 자동 선택 후 결과 상단 또는 하단에 `선택된 템플릿: xxx.md (이유: ...)` 한 줄 명시

## 템플릿 파일 형식 규칙 (개발자/유지보수용)

- YAML frontmatter 필수: `pattern`, `name`, `useWhen`, `outputStyle`
- 상단부 = **에이전트가 따라야 할 실제 문서 skeleton** (`{placeholder}` 사용)
- 하단 `## 패턴 가이드` = 설명 + 예시 (출력 제외)
- 개조식 + 시각자료(표·Mermaid) 친화적으로 유지

## 추가 방법

사용자가 외부 vault 등에서 새로운 패턴을 가져오면:
1. `struct-docs/templates/` 에 `{영문-kebab-name}-pattern.md` 로 저장 (예: structure-event-response-pattern.md)
2. 위 README 표에 한 줄 추가
3. 필요 시 `.claude/agents/writing.md` 선택 로직 보강

## 이전 파일명 (참고)
2026-06 이전에는 일부 파일이 한국어로 되어 있었습니다:
- 구조사건대처-pattern.md → structure-event-response-pattern.md
- 목적방침-pattern.md → objective-policy-pattern.md
- 상태트리거사고손해-pattern.md → state-trigger-accident-damage-pattern.md

모든 활성 참조는 영문으로 통일되었습니다.
