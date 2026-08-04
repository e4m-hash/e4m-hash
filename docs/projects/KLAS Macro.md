---
type: project
status: completed
featured: false
position: supporting
focus:
  - automation
  - application
evidence:
  - code
updated: 2026-08-04
---

# KLAS 서지정보 매크로

| 항목 | 내용 |
| --- | --- |
| 기간 | 2023.04 ~ 2024.02 |
| 역할 | 기획 · 개발 |
| 기술 | Python, PyQt5, Selenium |
| 코드 | [e4m98/Klas_Macro](https://github.com/e4m98/Klas_Macro) |

공립도서관 서지정보 관리 페이지에서 반복되는 입력 작업을 자동화한 데스크톱 도구입니다.

## 구현

- PyQt5로 비개발자가 실행할 수 있는 GUI 구성
- Selenium과 Chrome WebDriver로 웹 페이지 입력·클릭 자동화
- 서지정보 입력 순서를 코드로 옮겨 반복 작업 축소

## ML Engineer 전환에서 남는 경험

모델링 프로젝트는 아니지만 실제 사용자의 반복 작업을 관찰하고, 브라우저라는
불안정한 외부 경계를 다루며, 실행 가능한 도구로 전달한 경험입니다.

## 한계

- 대상 웹 페이지의 DOM 변경에 영향을 받습니다.
- 작업 시간 절감과 오류 감소를 정량 측정한 기록은 없습니다.
