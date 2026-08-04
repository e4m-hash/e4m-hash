# ML Engineering

좋은 offline metric 하나를 만드는 것보다, 데이터가 바뀌어도 다시 학습하고
검증하며 안전하게 내보낼 수 있는 시스템을 만드는 데 초점을 둡니다.

Google의 MLOps 가이드는 실제 ML 시스템에서 모델 코드가 차지하는 부분보다
데이터 검증, 자동화, 테스트, metadata, serving, monitoring 같은 주변 요소가
더 크다고 설명합니다. 이 구분을 현재 학습 순서의 기준으로 사용합니다.

## 생명주기

1. 문제와 성공 기준을 먼저 정의한다.
2. 입력 schema와 데이터 lineage를 기록한다.
3. 재실행 가능한 pipeline에서 feature를 만든다.
4. 누수를 통제한 split으로 baseline부터 평가한다.
5. preprocessing과 model을 versioned artifact로 묶는다.
6. batch 또는 online 경계에 맞춰 서빙한다.
7. 데이터 품질, latency, 오류, drift를 관측한다.

## 노트

- [[데이터와 학습 파이프라인]]
- [[평가와 신뢰성]]
- [[서빙과 운영]]

## 이 저장소의 증거

| 생명주기 | 현재 프로젝트 | 상태 |
| --- | --- | --- |
| 원시 데이터 workflow | [[FunOMIC2 Nextflow Pipeline]] | 완료 |
| 모델과 서비스 연결 | [[KT Aivle Big Project]] | 완료 |
| end-to-end ML system | [[MetaScale]] | 구현 예정 |

## 기준 자료

- [Google Cloud — MLOps: Continuous delivery and automation pipelines](https://docs.cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning?hl=en)
- [Google for Developers — ML pipelines](https://developers.google.com/machine-learning/managing-ml-projects/pipelines)
- [AWS — ML Solution Monitoring, Maintenance, and Security](https://docs.aws.amazon.com/aws-certification/latest/machine-learning-engineer-associate-01/machine-learning-engineer-associate-01-domain4.html)
- [Hyperconnect — Machine Learning Software Engineer](https://career.hyperconnect.com/job/a8d9b01f-f11c-44f3-8e8b-a4c91e1331c8/)
- [당근 — 머신러닝을 위한 모든 프로세스를 경험할 수 있어요](https://about.daangn.com/jobs/article/recruit24-interview-aio/)
