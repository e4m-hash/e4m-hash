# ML Engineer Portfolio

대규모 원시 데이터를 재현 가능한 feature로 만들고, 누수를 통제해 모델을 평가한 뒤,
versioned artifact와 API로 운영 환경까지 연결하는 ML Engineer를 목표로 합니다.

이 포트폴리오는 [[MetaScale]]을 중심으로 세 가지를 증명하는 과정입니다.

| 기준 | 질문 | 현재 근거 |
| --- | --- | --- |
| Scale | 큰 파일과 고차원 feature를 제한된 자원에서 처리할 수 있는가 | [[FunOMIC2 Nextflow Pipeline]], Nextflow, container |
| Reliability | 데이터 계약, split, 재현성과 실패 복구를 설명할 수 있는가 | workflow 재시작, cohort-aware 평가 설계 |
| Production Readiness | 학습 결과를 서빙하고 관측할 수 있는가 | [[KT Aivle Big Project]], MetaScale 구현 계획 |

## Flagship Project

### [[MetaScale]] — 구현 예정

Shotgun metagenomics를 실험용 데이터셋이 아니라 운영해야 할 ML workload로 다룹니다.

```text
FASTQ / metadata
→ validation and feature pipeline
→ leakage-safe training and evaluation
→ versioned model bundle
→ online / batch inference
→ monitoring
```

현재 공개된 것은 시스템 설계와 완료 조건입니다. 코드, 처리량, 모델 성능,
latency는 구현하고 측정한 뒤에만 결과로 기록합니다.

→ [MetaScale 설계 보기](projects/MetaScale.md)

## Supporting Evidence

- [[FunOMIC2 Nextflow Pipeline]] — 원시 데이터 workflow, container, 실패 지점 재시작
- [[KT Aivle Big Project]] — 데이터 수집, tabular ML, 애플리케이션 연동
- [[KLAS Macro]] — 반복 업무 자동화와 사용자 도구 개발

→ [프로젝트 전체 보기](projects/index.md)

## Knowledge Base

- [ML Engineering](notes/ml-engineering/index.md) — pipeline, evaluation, serving, monitoring
- [AI](notes/ai/index.md) — 모델과 학습 방법
- [Statistics](notes/statistics/index.md) — 검증과 해석의 기반
- [Domain / Bioinformatics](domain/bioinformatics/index.md) — MetaScale 입력 데이터와 도메인 제약

## More

- [소개](about.md)
- [블로그](blog/index.md)
