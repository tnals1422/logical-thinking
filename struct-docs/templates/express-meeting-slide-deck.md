---
tags: [struct, template, express]
pattern: express-meeting-slide-deck
name: "Meeting Slide Deck"
useWhen: "deliverableType이 meeting-material이고 Full Report(회의자료) 후속 slide가 필요할 때."
outputStyle: "Marp 호환. 5~12장. 회의 시간·meetingPurpose 반영."
marp: true
reference: "reference/president/대통령 보고서.md Ch.4 회의자료"
---

---
marp: true
theme: default
paginate: true
---

# {회의 제목}

{날짜} · {예상 시간}분 · {meetingPurpose 한글}

---

## 회의 목적

> {coreClaim}

- **안건**: {1~2줄}
- **기대**: {회의 후 상태}

---

## 안건 1 — {제목}

- {핵심 bullet}
- {핵심 bullet}
- {핵심 bullet}

---

## 안건 2 — {제목} (해당 시)

- {bullet}
- {bullet}

---

## 논의 포인트 / 의사결정 사항

| 구분 | 내용 |
|------|------|
| {info-share / opinion-gathering / decision} | {참석자가 알아야 할·결정할 것} |

---

## 결론·다음 단계

> {회의에서 확정·공유할 메시지}

- {후속 조치 1}
- {후속 조치 2}

---

## 참고 — 상세 자료

Full Report: [{제목}]({fullReportPath})

---

## 패턴 가이드 (출력 제외)

- `meetingPurpose: info-share` → 정보 전달 슬라이드 비중
- `opinion-gathering` → 논의 질문·선택지 슬라이드
- `decision` → 결정안·비교표 슬라이드
- Full Report §3 안건 구조와 1:1 매핑 권장