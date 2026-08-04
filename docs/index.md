# ML Engineer Portfolio

바이오인포매틱스에서 출발해, 원시 데이터 처리부터 모델 평가와 운영까지
연결하는 ML Engineer로 전환하고 있습니다.

도메인 데이터의 특성을 이해하는 데서 멈추지 않고 같은 입력을 다시 처리할 수 있는
파이프라인, 누수를 통제한 평가, 버전이 맞는 모델 산출물을 만드는 데 관심이 있습니다.

## 대표 프로젝트

### [[MetaScale]] — 구현 예정

Shotgun metagenomics 데이터를 대상으로 데이터 검증, 학습, 서빙, 모니터링을
하나의 재현 가능한 시스템으로 연결하는 12주 ML Engineering 프로젝트입니다.

### [[FunOMIC2 Nextflow Pipeline]] — 완료

Shell/R 기반 분석 절차를 Nextflow DSL2 워크플로로 옮기며 모듈화,
컨테이너 실행, 실패 지점 재시작을 다뤘습니다.

### [[KT Aivle Big Project]] — 완료

공공임대주택 데이터를 수집하고 CatBoost·XGBoost 계열 모델을 비교해
웹 서비스와 연결한 팀 프로젝트입니다.

→ [프로젝트 전체 보기](projects/index.md)

## ML Engineering 역량 지도

| 영역 | 현재 근거 | 다음 산출물 |
| --- | --- | --- |
| 데이터 파이프라인 | FunOMIC2, Nextflow, 컨테이너 | MetaScale 데이터 계약·smoke pipeline |
| 모델링·평가 | KT Aivle, 생물통계, microbiome ML 논문 | cohort-aware baseline·model card |
| 서빙 | Spring/React 연동 경험 | versioned model bundle·FastAPI contract |
| 운영 | Docker/Podman, Kubernetes 학습 | CI/CD·metrics·drift dashboard |

→ [ML Engineering 노트](notes/ml-engineering/index.md)

## 도메인 기반

- [생물정보](notes/bioinformatics/index.md) — metagenomics · microbiome
- [통계](notes/statistics/index.md) — 회귀 · 다변량 · 생물통계
- [AI](notes/ai/index.md) — 머신러닝 · 딥러닝 · LLM
- [파이프라인](notes/pipeline/index.md) — Nextflow · 재현성

## 더 보기

- [소개](about.md)
- [블로그](blog/index.md)
