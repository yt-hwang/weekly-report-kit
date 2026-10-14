# REPORT 데이터 형식

`template.html` 의 `REPORT` 객체. 모든 글자는 화면에서 escape 되므로 HTML 을 넣지 않는다.

```js
REPORT = {
  brand: '상단 바 이름', title: '본문 큰 제목', brandColor: '#hex 또는 빈 값',
  current: '처음 열 기간 id',
  periods: [{ id: 'w1', label: '1주차', range: '9/30–10/6' }, …, { id: 'all', label: '전체 누적', range: '9/30–' }],
  data: { <기간 id>: Period }
}
```

## Period
| 키 | 형식 | 비고 |
|---|---|---|
| eyebrow | 문자열 | "2주차 · 10/7(수) – 10/13(화) KST · 확정" |
| headline | 문자열 | 2~3문장 요약 |
| kpis | `[[값, 이름, 설명], …]` 4개 | 값은 문자열('1,234', '₩98,000') |
| funnel.stages | `[[단계 이름, 숫자], …]` 3~7개 | 숫자는 꼭 number. 뒤로 갈수록 작거나 같아야 함 |
| funnel.drops | 문자열 배열(단계 수 − 1) | i번째 = i단계에서 다음으로 못 간 사람 이름("클릭 안 함") |
| funnel.bottleneck | 숫자(선택) | 빨강으로 표시할 칸 i(i단계→다음). 비우면 첫 칸을 뺀 가장 낮은 전환. headline 의 병목과 같게 |
| funnel.done / unit | 문자열 | 마지막 단계까지 간 사람 이름 / 단위('명') |
| daysTitle | 문자열(선택) | 막대 제목. 방문(visits)인지 방문자(visitors)인지 출처 숫자 그대로 — 예 '일별 방문(Squarespace)' |
| days | `[[날짜, 방문 수, 그날 한 일], …]` 또는 `'concat'` | 'concat' = 다른 기간 days 이어 붙이기(전체 누적용). 방문 수 모르면 null |
| sources | `{ title, rows: [[경로, 숫자, 메모], …], note }` | 숫자는 number. 없으면 rows: [] |
| content | `{ title, columns: [...], rows: [[...], …], note }` | 첫 열은 이름, 나머지는 숫자 칸(문자열 OK, 없으면 '–') |
| ads | `[{ platform, tiles: [[값, 이름, 설명], …], note }, …]` | 광고 플랫폼마다 한 묶음. 광고 안 하면 [] |
| next | 문자열 배열 | 다음 할 일 3~5개 |
| foot | 문자열 | 출처와 기준 시각 |

## 자주 하는 실수
- funnel 숫자가 앞 단계보다 크면(예: 광고 관리자 전환 > 실제 가입) 띠가 이상해진다 → 같은 출처 기준으로 맞추거나 메모로 분리.
- 전체 누적의 방문자는 주차 합이 아니라 기간 전체로 다시 센 값(같은 사람 중복 제거)이라 더 작을 수 있다 — foot 에 적는다.
- funnel 은 '이번 주 단계별 인원'이지 같은 사람을 끝까지 따라간 코호트가 아닐 수 있다(이번 주 결제자가 지난주 체험자일 수도). 그렇다면 foot 에 한 줄.
- 첫 주엔 기간 버튼을 이번 주 하나만 두고, 'all'(전체 누적)은 2주차부터 추가.
- 숫자가 없는 채널(예: 카톡 공지 조회수 모름)은 content 행에 '…' 로 두고 note 에 '수치 없음'.
