---
type: design
status: planned
related_project: "[[MetaScale]]"
updated: 2026-08-04
---

# MetaScale ML Platform
## Shotgun Metagenomics 기반 대규모·고차원 ML 엔지니어링 프로젝트 설계서

---

## 1. 프로젝트 개요

### 1.1 프로젝트명

**MetaScale: Reproducible ML Platform for Large-Scale Shotgun Metagenomics Data**

### 1.2 목표

대규모 shotgun metagenomics 원시 데이터와 메타데이터를 입력으로 받아 다음 과정을 일관된 시스템으로 구축한다.

1. 원시 데이터 검증 및 병렬 전처리
2. 고차원 feature matrix 생성
3. 데이터 누수를 통제한 모델 학습 및 평가
4. 실험·데이터·모델 버전 관리
5. 비동기 배치 추론 및 profile 기반 온라인 추론
6. 입력 품질, 데이터 분포, 예측 분포 모니터링
7. 컨테이너 기반 실행 및 CI/CD 자동화

본 프로젝트의 핵심은 생물학적 발견이 아니라 다음 ML Engineering 역량을 증명하는 것이다.

- 대규모 파일 기반 데이터 파이프라인 설계
- 고차원·희소 데이터 모델링
- 재현 가능한 실험 관리
- 학습과 추론 코드의 일관성 보장
- 배치 및 온라인 서빙
- 데이터·모델 모니터링
- 테스트와 배포 자동화
- 시스템 성능 및 비용 측정

---

## 2. 문제 정의

### 2.1 ML 문제

여러 독립 코호트에서 수집된 shotgun metagenomics feature를 이용해 이진 분류 모델을 학습한다.

```text
Input:
- Taxonomic abundance features
- Functional abundance features
- Sample-level metadata
- Pipeline QC metrics

Target:
- Binary class label

Output:
- Predicted class
- Prediction probability
- Model version
- Feature schema version
- Input quality warnings
- Out-of-distribution score
```

### 2.2 프로젝트 범위

#### 포함

- 공개 shotgun metagenomics 데이터
- 원시 FASTQ 처리
- taxonomic/functional feature 생성
- cohort-aware 데이터 분할
- 고차원 feature 전처리
- baseline 및 tree-based 모델
- 실험 추적
- 모델 registry
- 배치 추론 API
- profile 기반 추론 API
- 모니터링
- Docker 및 CI/CD

#### 제외

- 임상 진단 성능 주장
- 웹 프론트엔드
- 실시간 FASTQ streaming
- 자체 reference database 구축
- 완전 자동화된 production retraining
- 다수의 복잡한 딥러닝 모델
- assembly/MAG 분석의 본 프로젝트 필수화

---

## 3. 성공 기준

### 3.1 시스템 성공 기준

- 동일한 manifest, config, container, reference DB로 동일한 feature matrix를 생성한다.
- 단일 명령으로 smoke-test 학습을 재현할 수 있다.
- 데이터 처리 pipeline이 실패 지점부터 재시작 가능하다.
- 학습과 서빙이 동일한 feature schema와 preprocessing artifact를 사용한다.
- 신규 샘플을 비동기 job으로 처리하고 결과를 조회할 수 있다.
- pipeline, API, model 지표를 대시보드에서 확인할 수 있다.

### 3.2 모델 성공 기준

- 무작위 분할과 cohort 분할 성능 차이를 정량화한다.
- 최소 3개 독립 hold-out cohort 평가 결과를 제공한다.
- metadata-only baseline과 비교한다.
- AUROC뿐 아니라 AUPRC, calibration, worst-cohort 성능을 보고한다.
- 입력 분포 이탈을 탐지하고 OOD 경고를 반환한다.

### 3.3 포트폴리오 성공 기준

README 첫 화면에서 다음을 확인할 수 있어야 한다.

- 처리한 샘플 수
- 원시 데이터 크기
- 생성된 feature 수
- cohort 수
- 최종 모델 성능
- 최악의 hold-out cohort 성능
- 전체 시스템 아키텍처
- 재현 명령
- 처리시간 및 메모리 benchmark
- API 예시
- 모니터링 화면

---

## 4. 데이터 규모와 가정

### 4.1 목표 규모

```text
Cohorts: 4개 이상
Samples: 1,000~5,000개
Raw data: 0.5~5 TB
Features:
- Taxonomic: 500~5,000
- Functional: 1,000~20,000
Input format:
- FASTQ.gz
- TSV/CSV metadata
- Parquet feature matrix
```

### 4.2 자원 제약 대응

전체 원시 데이터를 로컬에서 처리하기 어려운 경우 다음 단계로 구현한다.

1. 소형 FASTQ subset으로 end-to-end pipeline 검증
2. 사전 계산 profile로 전체 ML pipeline 구현
3. 대표 cohort만 원시 데이터에서 재처리
4. 전체 처리 비용과 시간을 benchmark 기반으로 추정

합성 데이터는 성능·부하 테스트에만 사용하고 최종 모델 평가에는 사용하지 않는다.

---

## 5. 전체 아키텍처

```text
                ┌────────────────────────────┐
                │ Public Object Storage      │
                │ FASTQ / Metadata / Manifest│
                └──────────────┬─────────────┘
                               │
                               ▼
                ┌────────────────────────────┐
                │ Metadata & File Validation │
                │ Schema / Checksum / Pair   │
                └──────────────┬─────────────┘
                               │
                               ▼
                ┌────────────────────────────┐
                │ Workflow Orchestrator      │
                │ Nextflow DSL2              │
                └──────────────┬─────────────┘
                               │
             ┌─────────────────┴─────────────────┐
             ▼                                   ▼
┌────────────────────────┐          ┌────────────────────────┐
│ Raw Data Processing    │          │ Pipeline Observability │
│ QC / Filter / Profiling│          │ Logs / Runtime / Retry │
└────────────┬───────────┘          └────────────────────────┘
             │
             ▼
┌────────────────────────┐
│ Versioned Feature Store│
│ Parquet + Manifest     │
└────────────┬───────────┘
             │
             ▼
┌────────────────────────┐
│ Training Pipeline      │
│ Validation / CV / Tune │
└───────┬─────────┬──────┘
        │         │
        ▼         ▼
┌────────────┐  ┌──────────────────┐
│ MLflow     │  │ Model Evaluation │
│ Tracking   │  │ Cohort / Drift   │
└──────┬─────┘  └─────────┬────────┘
       │                   │
       └─────────┬─────────┘
                 ▼
        ┌──────────────────┐
        │ Model Registry   │
        │ Model + Schema   │
        └────────┬─────────┘
                 │
       ┌─────────┴───────────┐
       ▼                     ▼
┌───────────────┐     ┌────────────────┐
│ Batch Jobs API│     │ Profile API    │
│ FASTQ URI     │     │ Feature Input  │
└───────┬───────┘     └───────┬────────┘
        │                     │
        └──────────┬──────────┘
                   ▼
       ┌────────────────────────┐
       │ Monitoring             │
       │ Prometheus / Grafana   │
       │ QC / Drift / Latency   │
       └────────────────────────┘
```

---

## 6. 기술 스택

| 영역 | 선택 기술 | 목적 |
|---|---|---|
| Workflow | Nextflow DSL2 | 병렬 실행, 재시작, HPC/Cloud 호환 |
| Container | Docker, Apptainer | 실행 환경 고정 |
| 데이터 처리 | Python, Polars, PyArrow | 고성능 tabular 처리 |
| 저장 형식 | Parquet | columnar storage, predicate pushdown |
| 모델링 | scikit-learn, LightGBM | baseline 및 고차원 tabular ML |
| 설정 관리 | Hydra | 실험 설정 분리 |
| 검증 | Pandera, Pydantic | 데이터·API schema 검증 |
| 실험 관리 | MLflow | parameter, metric, artifact 추적 |
| 데이터 버전 | DVC 또는 manifest+checksum | 데이터 lineage |
| API | FastAPI | job 및 profile 추론 API |
| 작업 큐 | Celery + Redis 또는 Arq | 비동기 배치 처리 |
| Metadata DB | PostgreSQL | job, model, dataset 상태 저장 |
| Object Storage | MinIO/S3 | FASTQ, feature, artifact 저장 |
| 모니터링 | Prometheus, Grafana | 서비스 및 pipeline 지표 |
| Drift | Evidently 또는 자체 구현 | 입력·예측 분포 변화 |
| 테스트 | pytest | unit, integration, contract |
| 품질 관리 | Ruff, mypy, pre-commit | 정적 검사 |
| CI/CD | GitHub Actions | 테스트, image build, 배포 |
| 배포 | Docker Compose, Kubernetes | 로컬 및 staging 배포 |
| 부하 테스트 | Locust 또는 k6 | latency, throughput 측정 |

---

## 7. 저장소 구조

```text
metascale/
├── README.md
├── Makefile
├── pyproject.toml
├── uv.lock
├── nextflow.config
├── main.nf
├── docker-compose.yml
├── .env.example
├── .pre-commit-config.yaml
│
├── conf/
│   ├── local.config
│   ├── test.config
│   ├── slurm.config
│   └── cloud.config
│
├── workflows/
│   ├── raw_processing.nf
│   ├── taxonomic_profile.nf
│   ├── functional_profile.nf
│   └── end_to_end.nf
│
├── modules/
│   ├── validate_input/
│   ├── raw_qc/
│   ├── host_filter/
│   ├── taxonomic_profile/
│   ├── functional_profile/
│   └── aggregate_features/
│
├── configs/
│   ├── data/
│   │   ├── local.yaml
│   │   └── production.yaml
│   ├── features/
│   │   ├── taxonomy.yaml
│   │   └── function.yaml
│   ├── model/
│   │   ├── logistic_regression.yaml
│   │   ├── lightgbm.yaml
│   │   └── mlp.yaml
│   └── experiment/
│       ├── baseline.yaml
│       ├── cohort_cv.yaml
│       └── final.yaml
│
├── src/
│   └── metascale/
│       ├── metadata/
│       │   ├── schema.py
│       │   ├── validation.py
│       │   └── manifest.py
│       ├── features/
│       │   ├── loader.py
│       │   ├── filtering.py
│       │   ├── transform.py
│       │   ├── alignment.py
│       │   └── build_matrix.py
│       ├── training/
│       │   ├── split.py
│       │   ├── pipeline.py
│       │   ├── train.py
│       │   ├── tune.py
│       │   ├── calibrate.py
│       │   └── registry.py
│       ├── evaluation/
│       │   ├── metrics.py
│       │   ├── cohort_cv.py
│       │   ├── confounding.py
│       │   ├── drift.py
│       │   └── report.py
│       ├── serving/
│       │   ├── app.py
│       │   ├── schemas.py
│       │   ├── jobs.py
│       │   ├── predictor.py
│       │   └── middleware.py
│       ├── monitoring/
│       │   ├── metrics.py
│       │   ├── input_monitor.py
│       │   ├── prediction_monitor.py
│       │   └── alerts.py
│       └── common/
│           ├── logging.py
│           ├── exceptions.py
│           ├── paths.py
│           └── versioning.py
│
├── deployment/
│   ├── docker/
│   │   ├── Dockerfile.pipeline
│   │   ├── Dockerfile.train
│   │   └── Dockerfile.api
│   ├── kubernetes/
│   │   ├── api-deployment.yaml
│   │   ├── worker-deployment.yaml
│   │   ├── service.yaml
│   │   ├── configmap.yaml
│   │   ├── secret.yaml
│   │   ├── hpa.yaml
│   │   └── ingress.yaml
│   ├── prometheus/
│   └── grafana/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── contract/
│   ├── pipeline/
│   └── load/
│
├── scripts/
│   ├── build_manifest.py
│   ├── generate_test_data.py
│   ├── run_training.py
│   ├── run_evaluation.py
│   ├── submit_job.py
│   └── benchmark.py
│
├── reports/
│   ├── data_card.md
│   ├── model_card.md
│   ├── reproducibility_report.md
│   ├── cohort_validation_report.md
│   ├── load_test_report.md
│   └── limitations.md
│
└── .github/
    └── workflows/
        ├── ci.yaml
        ├── pipeline-smoke-test.yaml
        ├── build-images.yaml
        └── deploy-staging.yaml
```

---

## 8. 데이터 계약

### 8.1 Manifest schema

```yaml
sample_id: string
subject_id: string
cohort_id: string
read_1_uri: string
read_2_uri: string
label: int
checksum_r1: string
checksum_r2: string
sequencing_platform: string
metadata_version: string
```

### 8.2 Feature matrix schema

```text
sample_id: string
subject_id: string
cohort_id: string
label: int
feature_000001: float32
feature_000002: float32
...
pipeline_version: string
database_version: string
feature_schema_version: string
```

### 8.3 Model artifact contract

```text
model.pkl
preprocessor.pkl
feature_manifest.json
class_mapping.json
training_config.yaml
metrics.json
model_card.md
environment.json
```

### 8.4 검증 규칙

- `sample_id`는 전체 manifest에서 유일해야 한다.
- paired-end 입력은 R1/R2가 모두 존재해야 한다.
- checksum이 일치하지 않으면 pipeline을 중단한다.
- label 누락 샘플은 학습 데이터에서 제외한다.
- train/test 간 `subject_id` 중복을 금지한다.
- 추론 입력의 feature schema version이 모델과 다르면 요청을 거부한다.
- feature 순서가 다르면 이름 기준으로 정렬하고 누락·추가 feature를 기록한다.

---

## 9. 데이터 처리 파이프라인

### 9.1 실행 단계

```text
validate_manifest
→ stage_input
→ raw_qc
→ host_filter
→ generate_taxonomic_profile
→ generate_functional_profile
→ validate_profile
→ aggregate_profiles
→ build_feature_matrix
→ write_manifest
→ publish_metrics
```

### 9.2 실행 특성

- sample 단위 병렬 처리
- task별 CPU·memory 설정
- retry와 timeout 설정
- 실패 task resume 지원
- container digest 고정
- reference DB version 및 checksum 기록
- task별 runtime과 peak memory 수집
- 산출물을 object storage에 저장
- 성공·실패 상태를 metadata DB에 기록

### 9.3 출력 파티션

```text
s3://metascale/features/
  pipeline_version=v1/
  database_version=db-2026-01/
  cohort_id=cohort-a/
  part-00001.parquet
```

### 9.4 데이터 처리 benchmark

| 실험 | 비교 |
|---|---|
| sample 병렬성 | 1, 4, 8, 16 workers |
| 저장 형식 | TSV vs Parquet |
| DataFrame 엔진 | Pandas vs Polars |
| matrix 표현 | dense vs sparse |
| feature 수 | 1K, 5K, 20K |
| 데이터 크기 | 10, 100, 1,000 samples |

측정 지표:

- wall-clock time
- CPU time
- peak memory
- read/write throughput
- output size
- 실패율
- 재시작 시간
- 샘플당 비용 추정

---

## 10. Feature Engineering

### 10.1 처리 단계

```text
raw abundance
→ schema validation
→ prevalence filtering
→ zero handling
→ transformation
→ optional feature selection
→ feature alignment
→ model input
```

### 10.2 누수 방지

다음 연산은 반드시 train fold 내부에서만 학습한다.

- prevalence threshold 계산
- imputation parameter
- scaling parameter
- transformation parameter
- feature selection
- class weighting
- probability calibration

### 10.3 비교 실험

| 실험 | 후보 |
|---|---|
| 입력 feature | taxonomy / function / combined |
| transformation | raw / log1p / CLR |
| feature filtering | prevalence 1%, 5%, 10% |
| feature selection | none / L1 / mutual information |
| representation | dense / sparse |
| metadata | none / metadata-only / combined |

### 10.4 Feature schema 관리

모델별로 다음 정보를 저장한다.

```json
{
  "feature_schema_version": "features-v3",
  "feature_count": 4821,
  "feature_names_hash": "sha256:...",
  "transformation": "clr",
  "prevalence_threshold": 0.05,
  "unknown_feature_policy": "drop",
  "missing_feature_policy": "fill_zero"
}
```

---

## 11. 데이터 분할 및 검증 전략

### 11.1 금지 전략

- 단순 row-level random split
- 동일 subject가 train/test에 동시 포함
- 전체 데이터에서 feature selection 후 CV
- test cohort를 이용한 threshold 조정
- test 성능에 기반한 반복적 모델 선택

### 11.2 내부 검증

```text
StratifiedGroupKFold
group = subject_id
```

### 11.3 외부 일반화 검증

```text
Leave-One-Cohort-Out

Iteration 1:
Train = Cohort B + C + D
Test  = Cohort A

Iteration 2:
Train = Cohort A + C + D
Test  = Cohort B
```

### 11.4 보고 지표

- 평균 성능
- cohort별 성능
- 최악 cohort 성능
- cohort 간 표준편차
- bootstrap confidence interval
- calibration
- inference latency
- 입력 OOD 비율

---

## 12. 모델링

### 12.1 Baseline

1. Majority class
2. Metadata-only Logistic Regression
3. Feature-only Logistic Regression
4. Feature + metadata Logistic Regression

### 12.2 후보 모델

- Elastic Net Logistic Regression
- LightGBM
- XGBoost
- 제한적 MLP

### 12.3 모델 선택 원칙

모델은 평균 AUROC만으로 선택하지 않는다.

```text
selection_score =
  cross_cohort_performance
  + calibration_quality
  + worst_cohort_robustness
  + inference_efficiency
  + reproducibility
```

실제 구현에서는 가중 합산 점수를 사용할 필요는 없지만, 평가 기준을 명시적으로 문서화한다.

### 12.4 Hyperparameter tuning

- nested CV 또는 train/validation 분리
- Optuna 사용 가능
- trial 수 제한
- seed 고정
- pruning 적용
- 최종 test cohort는 tuning에 사용하지 않음

### 12.5 Calibration

후보:

- Platt scaling
- isotonic regression

calibration model도 training artifact에 포함한다.

---

## 13. 평가 설계

### 13.1 예측 성능

- AUROC
- AUPRC
- Macro F1
- balanced accuracy
- sensitivity
- specificity
- Matthews correlation coefficient

### 13.2 확률 품질

- log loss
- Brier score
- expected calibration error
- calibration curve

### 13.3 일반화 성능

```text
mean_holdout_auroc
worst_holdout_auroc
holdout_auroc_std
mean_holdout_auprc
```

### 13.4 시스템 성능

- training runtime
- peak memory
- model size
- cold-start time
- p50/p95/p99 latency
- throughput
- batch processing rate
- API error rate

### 13.5 편향·교란 진단

- cohort ID 예측 성능
- metadata-only baseline
- cohort별 feature distribution
- label/cohort 연관성
- permutation test
- cohort별 calibration

---

## 14. 실험 추적 및 버전 관리

### 14.1 MLflow parameter

```text
dataset_version
manifest_hash
pipeline_version
database_version
feature_schema_version
split_version
model_type
hyperparameters
random_seed
transformation
feature_selection
cohort_train_list
cohort_test
```

### 14.2 MLflow metric

```text
mean_cv_auroc
mean_cv_auprc
holdout_auroc
holdout_auprc
worst_cohort_auroc
ece
brier_score
training_seconds
peak_memory_mb
inference_ms
feature_count
```

### 14.3 Artifact

- model
- preprocessing pipeline
- calibration model
- feature manifest
- split manifest
- configuration
- confusion matrix
- calibration plot
- cohort report
- drift baseline
- environment information
- model card

### 14.4 버전 단위

```text
application_version: 1.2.0
pipeline_version: pipeline-0.4.0
dataset_version: dataset-2026-08
feature_schema_version: features-v3
model_version: model-20260804-001
database_version: db-2026-01
```

애플리케이션, 데이터, feature schema, pipeline, reference DB, model 버전을 분리한다.

---

## 15. 재현성

### 15.1 고정 항목

- Git commit
- container digest
- Python lockfile
- Nextflow version
- reference DB checksum
- source file checksum
- dataset manifest
- split manifest
- random seed
- model config
- preprocessing config
- hardware 정보

### 15.2 실행 명령

```bash
make setup
make test-data
make pipeline-smoke-test
make build-features
make train
make evaluate
make serve
```

전체 재현:

```bash
make reproduce
```

### 15.3 실행 manifest

```yaml
run_id: run-20260804-001
git_commit: abc1234
pipeline_version: pipeline-0.4.0
dataset_version: dataset-2026-08
feature_schema_version: features-v3
database_version: db-2026-01
split_version: split-v2
random_seed: 42
container_digests:
  pipeline: sha256:...
  trainer: sha256:...
  api: sha256:...
```

---

## 16. 서빙 설계

원시 FASTQ 분석은 장시간 실행되므로 동기식 HTTP 요청으로 처리하지 않는다.

### 16.1 Batch Job API

#### Endpoint

```text
POST /v1/jobs
GET  /v1/jobs/{job_id}
GET  /v1/jobs/{job_id}/result
POST /v1/jobs/{job_id}/cancel
GET  /v1/models/current
GET  /health/live
GET  /health/ready
GET  /metrics
```

#### 요청

```json
{
  "sample_id": "sample-001",
  "read_1_uri": "s3://input/sample-001_R1.fastq.gz",
  "read_2_uri": "s3://input/sample-001_R2.fastq.gz",
  "metadata": {
    "cohort_id": "external",
    "platform": "illumina"
  }
}
```

#### 응답

```json
{
  "job_id": "job-20260804-001",
  "status": "queued",
  "pipeline_version": "pipeline-0.4.0",
  "model_version": "model-20260804-001"
}
```

### 16.2 Profile Prediction API

사전 계산된 feature를 저지연으로 추론한다.

```text
POST /v1/predict/profile
```

#### 응답

```json
{
  "prediction": 1,
  "probability": 0.83,
  "model_version": "model-20260804-001",
  "feature_schema_version": "features-v3",
  "ood_score": 0.12,
  "warnings": []
}
```

### 16.3 API 요구사항

- Pydantic schema validation
- request ID
- structured JSON logging
- timeout
- idempotency key
- model version 반환
- feature schema 검증
- 오류 응답 표준화
- readiness/liveness 분리
- job retry
- job cancellation
- signed URL 또는 object storage URI 사용

---

## 17. 학습-서빙 일관성

### 17.1 공유 artifact

학습 시스템은 다음 artifact를 등록한다.

```text
model
preprocessor
feature manifest
class mapping
calibrator
OOD detector
input schema
model metadata
```

서빙 시스템은 registry에서 동일 artifact bundle을 읽는다.

### 17.2 Contract test

다음 조건을 CI에서 검증한다.

- 학습 artifact를 API가 정상적으로 로드한다.
- feature 순서 변경에도 동일 결과를 반환한다.
- 누락 feature 정책이 일관된다.
- 추가 feature는 정의된 정책대로 제거한다.
- model version과 feature schema version이 일치한다.
- 동일 입력이 batch와 online에서 동일 예측을 반환한다.

---

## 18. 모니터링

### 18.1 Pipeline 지표

- job count
- success/failure count
- retry count
- task duration
- queue wait time
- CPU/memory usage
- sample processing time
- output size
- pipeline version별 실패율

### 18.2 API 지표

- request count
- status code
- p50/p95/p99 latency
- throughput
- active requests
- worker utilization
- model load time
- cold-start time
- payload size

### 18.3 데이터 품질 지표

- 입력 파일 checksum 실패
- read 수
- 입력 파일 크기
- profile 생성 실패율
- feature 누락률
- zero feature 비율
- schema mismatch count
- unknown feature count

### 18.4 Drift 지표

- feature별 PSI
- Wasserstein distance
- prediction distribution
- probability distribution
- OOD score
- cohort distance
- missing rate 변화

### 18.5 운영 경고

```text
pipeline failure rate > threshold
p95 latency > threshold
queue wait time > threshold
schema mismatch detected
feature missing rate > threshold
OOD ratio > threshold
prediction distribution shift detected
```

---

## 19. Drift 및 재학습 정책

### 19.1 Drift simulation

테스트 데이터에 다음 변화를 인위적으로 적용한다.

- 일부 feature 평균 이동
- 특정 feature 결측률 증가
- feature cardinality 변화
- cohort 비율 변화
- class prior 변화
- 낮은 품질 샘플 비율 증가

### 19.2 재학습 후보 조건

다음 조건 중 복수 조건이 충족될 때 candidate training job을 생성한다.

- 핵심 feature drift가 일정 기간 지속
- OOD 비율이 기준 초과
- 정답 확보 후 성능이 기준 대비 일정 수준 하락
- 신규 데이터가 최소 샘플 수 이상 누적
- 기존 모델의 calibration이 악화

자동 production 승격은 수행하지 않는다.

```text
drift detected
→ candidate training
→ offline evaluation
→ registry registration
→ manual approval
→ staging deployment
→ smoke test
→ production promotion
```

---

## 20. 테스트 전략

### 20.1 Unit Test

- manifest validation
- schema validation
- feature filtering
- transformation
- feature alignment
- metric calculation
- split logic
- OOD score
- API request validation

### 20.2 Integration Test

```text
small input files
→ workflow execution
→ feature matrix
→ model training
→ registry
→ API load
→ prediction
```

### 20.3 Contract Test

- pipeline output schema
- feature matrix schema
- model artifact schema
- 학습-서빙 feature 일치
- batch-online 예측 일치
- version compatibility

### 20.4 Smoke Test

```text
Samples: 4~10
Features: 50~200
Reference DB: 축소 버전
CV folds: 2
Model: Logistic Regression
```

### 20.5 Failure Test

- 손상된 입력 파일
- checksum 불일치
- R1/R2 누락
- 빈 profile
- NaN/Inf feature
- 잘못된 schema version
- 손상된 model artifact
- job worker 종료
- object storage timeout
- metadata DB 연결 실패

### 20.6 Load Test

- 동시 profile 추론 요청
- batch job 대량 등록
- worker 수 변화
- model preload 유무
- API replica 증가

---

## 21. CI/CD

### 21.1 Pull Request

```text
ruff
→ mypy
→ unit test
→ integration smoke test
→ contract test
→ Docker build
→ dependency vulnerability scan
```

### 21.2 Main Branch

```text
CI success
→ version tag
→ container image build
→ image registry push
→ staging deploy
→ health check
→ API smoke test
```

### 21.3 Model Release

```text
candidate registered
→ evaluation gate
→ manual approval
→ staging model load
→ contract test
→ shadow/canary validation
→ production alias update
```

### 21.4 배포 전략

- rolling update
- readiness probe
- liveness probe
- graceful shutdown
- model preload
- resource request/limit
- horizontal autoscaling
- rollback 가능한 image/model tag

---

## 22. Docker Compose 서비스

```text
api
worker
redis
postgres
minio
mlflow
prometheus
grafana
```

실행:

```bash
docker compose up -d
```

접속:

```text
API Docs:    http://localhost:8000/docs
MLflow:      http://localhost:5000
MinIO:       http://localhost:9001
Prometheus:  http://localhost:9090
Grafana:     http://localhost:3000
```

---

## 23. Kubernetes 구성

### 23.1 리소스

- API Deployment
- Worker Deployment
- Service
- ConfigMap
- Secret
- PersistentVolumeClaim
- HorizontalPodAutoscaler
- Ingress
- CronJob for monitoring

### 23.2 API Deployment

- replicas: 2
- readiness probe
- liveness probe
- resource request/limit
- rolling update
- graceful termination
- model cache volume

### 23.3 Worker Deployment

- pipeline job worker
- queue 기반 autoscaling
- CPU/memory class별 worker 분리
- job timeout
- retry policy
- temporary volume cleanup

### 23.4 로컬 검증 환경

- kind
- minikube
- k3d

---

## 24. 핵심 실험 계획

### 실험 1. 검증 방식 비교

```text
Random Stratified CV
vs.
Subject Grouped CV
vs.
Leave-One-Cohort-Out CV
```

목표:

- 무작위 분할이 외부 일반화 성능을 얼마나 과대평가하는지 정량화

### 실험 2. Feature 종류 비교

```text
Taxonomic only
Functional only
Combined
```

평가:

- 성능
- feature 수
- 학습시간
- 추론시간
- 모델 크기
- cohort 안정성

### 실험 3. 전처리 비교

```text
Raw abundance
Log1p
CLR
```

### 실험 4. 모델 비교

```text
Elastic Net Logistic Regression
LightGBM
XGBoost
MLP
```

### 실험 5. 데이터 처리 엔진 비교

```text
Pandas
vs.
Polars
```

측정:

- feature matrix 생성시간
- peak memory
- output size

### 실험 6. Dense vs Sparse

- 학습시간
- 메모리
- 모델 성능
- inference latency

### 실험 7. 서빙 benchmark

- worker 수
- API replica 수
- batch size
- model preload
- payload 크기

### 실험 8. Drift detection

- feature shift
- missing-rate shift
- cohort shift
- prediction shift

---

## 25. 12주 구현 계획

| 주차 | 작업 | 산출물 |
|---|---|---|
| 1 | 요구사항, 데이터 계약, 저장소 구성 | Project specification |
| 2 | manifest validator, test dataset | Data contract tests |
| 3 | Nextflow smoke pipeline | Pipeline smoke report |
| 4 | profile 집계, Parquet feature matrix | Versioned feature dataset |
| 5 | baseline, grouped CV | Baseline experiment |
| 6 | leave-one-cohort-out 평가 | Generalization report |
| 7 | 모델 비교, calibration, OOD | Model card |
| 8 | MLflow, registry, artifact contract | Registered candidate model |
| 9 | profile prediction API | Online inference demo |
| 10 | batch job API 및 worker | Async processing demo |
| 11 | Prometheus, Grafana, drift | Monitoring dashboard |
| 12 | CI/CD, Kubernetes, 문서화 | Reproducible release |

---

## 26. 단계별 완료 조건

### Phase 1. Data Pipeline MVP

- manifest validation 구현
- 소형 FASTQ smoke pipeline 동작
- feature matrix Parquet 저장
- pipeline resume 확인
- runtime 및 memory 기록

### Phase 2. Training MVP

- leakage-safe preprocessing
- grouped CV
- logistic regression baseline
- MLflow tracking
- model artifact 저장

### Phase 3. Generalization Evaluation

- leave-one-cohort-out 평가
- metadata-only baseline
- calibration
- worst-cohort metric
- cohort confounding report

### Phase 4. Serving MVP

- profile prediction API
- batch job API
- model registry 연동
- contract test
- structured logging

### Phase 5. Operations

- Prometheus/Grafana
- drift simulation
- alert rule
- load test
- Docker Compose

### Phase 6. Deployment

- GitHub Actions
- Kubernetes manifests
- staging deployment
- HPA
- rollback 검증

---

## 27. 최종 산출물

### 코드

- Nextflow pipeline
- Python package
- FastAPI application
- asynchronous worker
- monitoring configuration
- Kubernetes manifests
- CI/CD workflow

### 문서

- `README.md`
- `data_contract.md`
- `system_design.md`
- `data_card.md`
- `model_card.md`
- `reproducibility_report.md`
- `cohort_validation_report.md`
- `load_test_report.md`
- `failure_scenarios.md`
- `limitations.md`

### 시각 자료

- 시스템 아키텍처
- pipeline DAG
- MLflow experiment 화면
- cohort별 성능 그래프
- calibration plot
- latency 및 throughput 그래프
- drift dashboard
- Grafana dashboard

### 데모 시나리오

```text
1. manifest validation 실행
2. 소형 pipeline 실행
3. feature matrix 생성
4. 모델 학습
5. MLflow 결과 확인
6. model registry 등록
7. API 실행
8. profile 추론 요청
9. batch job 제출
10. Grafana 지표 확인
11. drift 시뮬레이션
12. candidate retraining job 생성
```

---

## 28. README 첫 화면 예시

```markdown
# MetaScale

A reproducible ML platform for large-scale, high-dimensional
shotgun metagenomics data.

## Key Results

- Processed X,XXX samples across X independent cohorts
- Managed XXX GB/TB of compressed raw input
- Generated XX,XXX versioned features
- Achieved mean hold-out AUROC of X.XX
- Achieved worst-cohort AUROC of X.XX
- Reduced feature-build memory by XX% using Polars/Parquet
- Served profile predictions at p95 latency of XX ms
- Detected simulated cohort and feature drift

## Quick Start

make setup
make pipeline-smoke-test
make train
docker compose up -d

## Architecture

[architecture diagram]

## Benchmarks

[benchmark table]
```

---

## 29. 이력서용 기술 문장

실제 측정값으로 교체해 사용한다.

- `X개 독립 코호트, X,XXX개 샘플, XXX GB 규모의 원시 데이터를 처리하는 Nextflow 기반 병렬 pipeline을 구축하고 task 재시작, container 고정, reference DB checksum 관리로 실행 재현성을 확보했습니다.`

- `수천~수만 개의 고차원 feature를 Parquet과 Polars 기반으로 처리하고, dense 대비 sparse representation을 적용해 peak memory를 XX% 절감했습니다.`

- `subject 및 cohort 단위 데이터 분할과 fold 내부 preprocessing을 적용하여 데이터 누수를 방지하고, leave-one-cohort-out 평가로 신규 데이터에 대한 일반화 성능을 검증했습니다.`

- `MLflow 기반 실험 추적과 model registry를 구성하고 모델, preprocessing, feature schema, calibration artifact를 하나의 versioned bundle로 관리했습니다.`

- `FastAPI, Redis, worker queue 기반 비동기 batch inference와 profile 기반 저지연 API를 구현하고 p95 latency, throughput, error rate를 측정했습니다.`

- `Prometheus와 Grafana를 이용해 pipeline 실패율, API latency, feature drift, OOD 비율, prediction distribution을 모니터링했습니다.`

---

## 30. 면접에서 설명할 핵심 설계 판단

### 왜 원시 데이터 처리를 포함했는가?

단순 모델링을 넘어 대규모 파일 처리, workflow orchestration, 리소스 관리, 실패 복구 및 데이터 lineage 역량을 보여주기 위해서다.

### 왜 무작위 분할만 사용하지 않았는가?

동일한 수집 환경의 데이터가 train/test에 함께 존재하면 실제 신규 환경 성능을 과대평가할 수 있기 때문이다.

### 왜 복잡한 딥러닝 모델을 중심으로 두지 않았는가?

이 프로젝트의 핵심 리스크는 모델 복잡도보다 데이터 누수, cohort shift, 고차원성, 재현성 및 운영 일관성이기 때문이다.

### 왜 원시 파일 추론을 비동기 job으로 설계했는가?

원시 데이터 처리는 실행시간과 메모리 사용량이 크며 HTTP 요청 수명 내에 완료하기 어렵기 때문이다.

### 왜 feature schema를 별도 버전으로 관리했는가?

reference DB와 pipeline 변경으로 feature 집합과 순서가 달라질 수 있으며, 이는 training-serving skew를 유발하기 때문이다.

### 왜 모델과 preprocessing을 하나의 bundle로 관리했는가?

서빙 환경에서 학습 시점과 동일한 변환을 보장하고 artifact 불일치로 인한 예측 오류를 방지하기 위해서다.

---

## 31. 최종 포지셔닝

본 프로젝트는 다음 역량을 가진 ML Engineer로 포지셔닝하는 것을 목표로 한다.

> 대규모 원시 데이터를 병렬 처리하고, 고차원 feature에 대한 누수 없는 학습·평가 pipeline을 구축하며, 데이터·모델 버전 관리부터 비동기 추론, 모니터링, CI/CD까지 확장할 수 있는 ML Engineer

핵심 평가 축은 다음 세 가지다.

1. **Scale**  
   대용량 파일, 고차원 feature, 병렬 workflow를 처리할 수 있는가.

2. **Reliability**  
   데이터 계약, 검증, 누수 방지, 재현성, 테스트를 설계할 수 있는가.

3. **Production Readiness**  
   학습 결과를 versioned artifact, API, batch job, monitoring, deployment로 연결할 수 있는가.
