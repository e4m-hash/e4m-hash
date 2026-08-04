# 프로젝트

MetaScale의 세 평가 축에 따라 프로젝트를 정리합니다. 완료된 구현과 계획을 구분하고,
성능·처리량·배포 상태는 실제 측정값이 있을 때만 적습니다.

| 프로젝트 | 상태 | Scale | Reliability | Production Readiness |
| --- | --- | --- | --- | --- |
| [[MetaScale]] | 구현 예정 | raw data·high-dimensional feature | contract·cohort evaluation | serving·monitoring·CI |
| [[FunOMIC2 Nextflow Pipeline]] | 완료 | sample-parallel workflow | container·resume | 운영 배포는 범위 밖 |
| [[KT Aivle Big Project]] | 완료 | tabular data pipeline | 모델 후보 비교 | Spring·React 연동 |
| [[KLAS Macro]] | 완료 | 해당 없음 | 외부 DOM 경계 처리 | 사용자 실행 도구 |

> [!note] 증거 기준
> 설계는 의도, 코드는 구현, demo는 동작, benchmark는 측정 결과를 뜻합니다.
> 서로를 대신하지 않도록 project frontmatter의 `evidence`에 구분해 기록합니다.
