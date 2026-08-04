# 소개

안녕하세요. 백희선입니다.

현재 BDLS Lab에서 metagenomics와 microbiome 데이터를 다루고 있습니다.
분석 도구를 사용하는 것에서 한 단계 더 나아가, 데이터 처리와 모델 학습을
재현 가능한 소프트웨어 시스템으로 만드는 ML Engineer를 목표로 하고 있습니다.

## 지금 하는 일

- 연구실: [BDLS Lab](https://bdsl.jbnu.ac.kr/blog/)
- 도메인: shotgun metagenomics, microbiome analysis
- 워크플로: Nextflow, nf-core, Docker/Podman, Apptainer
- 모델링: scikit-learn, XGBoost/CatBoost, PyTorch
- 자격: KT AI Associate

## 전환 과정에서 가져가는 강점

### 도메인 데이터를 끝까지 다뤄 본 경험

FASTQ와 메타데이터를 입력으로 받아 QC, taxonomic/functional profiling,
통계 분석까지 이어지는 흐름을 다뤘습니다. 데이터 생성 과정과 reference DB가
결과에 미치는 영향을 알고 있다는 점을 ML 데이터 검증과 lineage 관리로 확장하고 있습니다.

### 재현 가능한 파이프라인

Nextflow DSL2와 컨테이너를 사용해 분석 단계를 모듈로 나누고, 같은 입력과 설정으로
다시 실행할 수 있는 구조를 만들었습니다. [[FunOMIC2 Nextflow Pipeline]]에서 이 과정을 정리했습니다.

### 모델을 서비스와 연결한 경험

KT Aivle 팀 프로젝트에서 공공임대주택 데이터 수집, tabular 모델 비교,
Spring·React 서비스 연동을 경험했습니다. 현재는 [[MetaScale]]을 통해
데이터 계약, 실험 추적, 모델 registry, 서빙 contract와 모니터링까지 범위를 넓히고 있습니다.

## 기술

| 구분 | 사용 경험 | 확장 중 |
| --- | --- | --- |
| Language | Python, R, C++ | Java/Spring 코드 이해 |
| Data/ML | pandas, NumPy, SciPy, scikit-learn, XGBoost, CatBoost, PyTorch | Polars, PyArrow, MLflow |
| Workflow | Nextflow, nf-core, Selenium | 학습 pipeline, data/model validation |
| Runtime | Docker, Podman, Apptainer, Linux | Kubernetes, cloud deployment |
| Domain | metagenomics, microbiome statistics | cohort-aware ML evaluation |

## 이력

| 기간 | 내용 |
| --- | --- |
| 2025 ~ 현재 | BDLS Lab — metagenomics·microbiome 분석 |
| 2024 ~ 2025 | KT Aivle — AI 개발 과정 및 팀 프로젝트 |
| 2022 ~ 2024 | 병역 의무 이행 |
| 2023 ~ 2024 | KLAS 서지정보 입력 자동화 프로젝트 |

## Contact

- GitHub: [@e4m-hash](https://github.com/e4m-hash)
- Email: `e4m98s@gmail.com`
