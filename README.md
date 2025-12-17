# 두들링ENT 방송인 모집 랜딩 페이지

이 저장소에는 방송인 모집 신청을 받는 정적 랜딩 페이지가 포함되어 있습니다. 신청 데이터는 Google Apps Script 웹앱으로 전송되어 구글 시트에 저장되고, 지정한 이메일 주소로 알림을 발송할 수 있습니다.

---

## 파일 구성
- `index_broadcaster.html` — 방송인 모집 신청 폼 (현재 메인 페이지)
- `두들링이엔티 로고.png` — 페이지 로고 이미지

원래 페이지(`index.html`)가 있다면, 필요 시 백업/아카이브 후 `index_broadcaster.html`을 메인으로 사용하세요.

---

## 빠르게 실행
- Windows에서 파일을 더블 클릭하여 브라우저로 열기
- VS Code 사용 시 Live Server 확장으로 로컬 미리보기 권장

---

## 데이터 수집/알림 연결 (Google Apps Script)
이 페이지는 신청 시 JSON을 **Google Apps Script 웹앱 URL**로 전송합니다.

1. 구글 시트 열기: https://docs.google.com/spreadsheets/d/1-f3t4KcYTeiURpWM4ckm7r5NFMXz9IsJGJdqh4TJUzE/edit
2. 시트에서 "도구 > 스크립트 편집기"로 이동
3. 제공된 Apps Script 코드(데이터 저장 + 이메일 발송)를 붙여넣고 저장
4. "배포 > 새 배포 > 유형: 웹 앱"으로 배포
   - 다음으로 실행: 본인 계정
   - 접근 권한: 누구나
5. 발급된 **웹 앱 URL**을 페이지에 반영

현재 HTML에 설정된 URL: 
`https://script.google.com/macros/s/AKfycbwD8KRXdTqg-4aKkz1mxJTjS4f-CLlYouNIZIB6UjPcI9vFjflqgK1o_CAZc-w0tWt4/exec`

---

## HTML에서 웹훅 설정 위치
`index_broadcaster.html` 하단 스크립트의 "webhookURL" 상수:
```html
// Google Apps Script 웹훅 URL
const webhookURL = 'https://script.google.com/macros/s/AKfycbwD8KRXdTqg-4aKkz1mxJTjS4f-CLlYouNIZIB6UjPcI9vFjflqgK1o_CAZc-w0tWt4/exec';
```
웹앱을 재배포하여 URL이 바뀌면 여기 값을 새 URL로 교체하세요.

---

## 시트 컬럼 매핑
작성된 Apps Script는 아래 순서로 데이터를 시트에 추가합니다.

A. 접수날짜 및 시간  
B. 이름 (`name`)  
C. 나이/연령대 (`age`)  
D. 지역 (`region`)  
E. 방송경험 여부 (`broadcast_experience`)  
F. 지원하게 된 이유 (`reason`, 다중 선택 → 쉼표 구분)  
G. 본인 SNS 계정 (`platform`, 다중 선택 → 쉼표 구분)  
H. 활동 원하는 플랫폼 (`desired_platform`, 다중 선택 → 쉼표 구분)  
I. 원하는 방송 컨셉 (`concept`, 다중 선택 → 쉼표 구분)  
J. 방송가능 시간대 (`available_time`, 다중 선택 → 쉼표 구분)  
K. SNS계정 링크/아이디 (`handle`)  
L. 문의사항 (`note`)  
M. 개인정보 수집·이용 동의 (`consent`: 체크 시 "동의합니다", 미체크 시 "동의하지 않습니다")  
N. 마케팅 및 추가 활용 동의 (`marketing`: 체크 시 "동의합니다", 미체크 시 "동의하지 않습니다")

---

## 이메일 알림 설정
Apps Script에서 아래 배열에 수신 이메일을 설정합니다.
```javascript
const emails = [
  'hyunwook.cho@renaultkoreamotors.com',
  'kellyvibe1109@gmail.com',
];
```
원하는 주소를 추가/수정 가능하며, 각 신청마다 지정한 모든 주소로 알림 메일을 발송합니다.

---

## 테스트 체크리스트
1. 페이지에서 필수 항목(이름/연락처/지역/연령대/동의 등)을 채워서 "▶ 신청하기" 클릭
2. 시트 첫 번째 시트에 새로운 행이 추가되는지 확인
3. 지정한 이메일로 알림 수신 확인
4. 동의 항목 체크/미체크에 따라 시트의 M/N 컬럼 값이 각각 "동의합니다"/"동의하지 않습니다"로 기록되는지 확인

참고: 페이지는 `fetch(..., { mode: 'no-cors' })`로 동작하므로 성공/실패 응답을 읽지 않고, 전송 후 성공 메시지를 표시합니다. 실제 기록 여부는 시트를 통해 확인하세요.

---

## 문제 해결 가이드
- 웹앱이 배포되지 않았거나 권한이 "누구나"가 아니면 기록되지 않습니다.
- 웹앱 URL이 바뀐 경우, HTML의 `webhookURL` 값을 새 URL로 교체하세요.
- 스크립트 권한 요청(첫 배포 시)을 허용하지 않으면 이메일이 전송되지 않습니다.

---

## 유지보수 팁
- 폼 항목 추가/변경 시 Apps Script의 컬럼 매핑을 함께 업데이트하세요.
- 수신 이메일 주소는 보안/운영 정책에 맞춰 주기적으로 점검하세요.
- 데이터 백업 및 시트 권한(편집/조회) 관리를 통해 개인정보 보호를 준수하세요.
