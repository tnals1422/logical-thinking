# 대통령비서실 보고서 참고 자료

Struct Deliverable System 고도화의 **산출물 축(유형 템플릿)** 설계·구현 참고용.

> 에이전트 런타임 기본 참조는 `struct-docs/templates/deliverables/*.md`이다.  
> 본 폴더는 설계·gold example·항목부호 규칙 참고용.

## 텍스트 가이드

| 파일 | 내용 |
|------|------|
| [대통령 보고서.md](대통령%20보고서.md) | 보고서 작성 입문·전문 코스 요약 (Ch.1~6, 유형별 작성법) |

## 표준 양식

| 파일 | 용도 |
|------|------|
| `대통령비서실 보고서의 표준 용지 양식.pdf` | 표지·본문 레이아웃 |
| `항목부호 체계.pdf` | 항목 번호 체계 |
| `항목부호 위치.pdf` | 항목부호 배치 |

## 유형별 실물 예시 (Gold Reference)

우선순위는 [docs/struct-deliverable-template-priority.md](../../docs/struct-deliverable-template-priority.md) 참조.

| 유형 | Wave | 파일 |
|------|------|------|
| 정책기획 | 1 (P0) | `정책기획보고서1_청년실업 원인분석 보고.pdf` |
| 정책기획 | 1 | `정책기획보고서2_OO시스템실용성제고방안.pdf` |
| 상황 | 2 (P0) | `상황보고서1_집중호우관련 상황보고.pdf` |
| 상황 | 2 | `상황보고서2_주요 국정상황.pdf` |
| 정보 | 2 | `정보보고서_OOO OOO활동실태.pdf` |
| 조정과제 | 3 (P1) | `조정과제보고서_OO사업점검결과 및 조정방향 보고.pdf` |
| 회의자료 | 4 (P1) | `회의자료보고서1_중소기업 정책정보 전달체계 혁신방안 추진.pdf` |
| 회의자료 | 4 | `회의자료보고서2_상반기 중소기업 현장체험단 실적 및 계획.pdf` |
| 회의결과 | 5 (P2) | `회의결과보고서1_과학기술자문회의 보고결과.pdf` |
| 회의결과 | 5 | `회의결과보고서2_제32회 국무회의 주요내용.pdf` |
| 정책참고 | 6 (P2) | `정책참고보고서_독일노동시장개혁방안 검토보고.pdf` |
| 행사기획 | 7 (P3) | `행사기획보고서_정부 업무관리시스템.pdf` |
| 행사진행 | 7 | `행사진행보고서_정부업무관리시스템 참고자료.pdf` |

## deliverable 템플릿 매핑

| deliverable 템플릿 파일 | 포함 유형 | 상태 |
|------------------------|----------|------|
| `deliverable-policy-planning.md` | 정책기획 | **available** |
| `deliverable-situation-info.md` | 상황 · 정보 | **available** |
| `deliverable-coordination.md` | 조정과제 | **available** |
| `deliverable-meeting-material.md` | 회의자료 (`meetingPurpose` 3변형) | **available** |
| `deliverable-meeting-result.md` | 회의결과 | **available** |
| `deliverable-policy-reference.md` | 정책참고 | **available** |
| `deliverable-event.md` | 행사기획·진행 (`eventPhase`) | **available** |