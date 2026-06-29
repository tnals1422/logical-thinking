# patterns/ — 논리 패턴 카테고리

이 디렉토리는 desktop 자료(`C:\Users\박수민\Desktop\생각정리, 문서 작성법 구축`) 구조와 유사하게 **사용 목적별**로 그룹화되어 있습니다.

육안으로 한눈에 파악하기 쉽도록 설계:

```
patterns/
├── education/          # 교육·규칙·지침 패턴
│   └── case-measure-pattern.md     (조건 → 조치/판단)
│
├── proposals/          # 기획서·제안서 패턴
│   ├── fabe-pattern.md             (Feature-Advantage-Benefit-Evidence)
│   └── prep-pattern.md             (Point-Reason-Evidence-Point)
│
├── reports/            # 보고서·분석용 패턴
│   ├── iaej-pattern.md
│   ├── incident-causal-pattern.md  (ser / stad 모드)
│   └── objective-policy-pattern.md (목적-방침)
│
└── general/            # 범용 / fallback
    └── scqa-pattern.md
```

## 로딩 규칙 (에이전트)
- Glob: `struct-docs/templates/patterns/**/*.md` (재귀)
- 특정 패턴 지정 시 frontmatter의 `pattern: xxx` 키로 매칭
- `logicPattern: fabe-pattern` → `patterns/proposals/fabe-pattern.md` 로 찾음

## Embed 사용 시 (Phase 3+)
deliverable 내부 `(logic: xxx-pattern)`으로 임베드할 때는 다음을 반드시 읽으세요:
- `patterns/EMBEDDING-GUIDE.md` (공통 embed 규칙 — W1 단계 주입 방식)
- 각 패턴 파일의 **"When embedded inside a deliverable"** 섹션
- `struct-docs/templates/README.md` Role Contract

deliverables는 thin container 역할만 하며, 단계 구조는 패턴이 제공한다.

## 추가 시
새 패턴 추가 시 해당 카테고리 폴더에 넣고, `templates/README.md` 의 표와 매트릭스, `reference/deliverable-brief-schema.md`, 그리고 `EMBEDDING-GUIDE.md` 를 함께 업데이트하세요.

이 구조 덕분에 "보고서 관련 패턴은 reports/ 폴더를 보면 된다"처럼 직관적으로 이해할 수 있습니다.
