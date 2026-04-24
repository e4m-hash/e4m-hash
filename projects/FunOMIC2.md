---
title: "FunOMIC2 Nextflow Pipeline"
layout: single
author_profile: true
---

# FunOMIC2 Nextflow Pipeline

<div class="project-showcase" markdown="1">

## 프로젝트 개요
**기존 메타게놈 분석 파이프라인의 현대적 재구현**

FunOMIC2는 원래 Shell script, R 기반의 메타게놈 기능 분석 파이프라인이었습니다.
이 프로젝트는 해당 파이프라인을 Nextflow로 완전히 재구현하여
재현성, 확장성, 그리고 사용성을 대폭 개선했습니다.

## 주요 특징

### 재현 가능한 워크플로우
- **Nextflow DSL2**: 최신 워크플로우 언어 활용
- **완전한 모듈화**: 각 분석 단계별 독립적 모듈
- **버전 관리**: Git 기반 파이프라인 버전 추적

### 컨테이너화
- **Docker 통합**: 모든 도구의 컨테이너화
- **Singularity 지원**: HPC 환경 호환성
- **환경 격리**: 의존성 충돌 방지

### 멀티 플랫폼 지원
- **로컬 실행**: 개발 및 소규모 분석
- **HPC 클러스터**: SLURM, PBS 지원
- **클라우드**: AWS, GCP, Azure 배포

</div>

## 기술 스택

<div class="tech-grid">
<div class="tech-category">
<h3> 워크플로우</h3>
<ul>
<li><strong>Nextflow</strong>: 파이프라인 엔진</li>
<li><strong>DSL2</strong>: 모던 문법</li>
<li><strong>nf-core</strong>: 모범 사례 준수</li>
</ul>
</div>

<div class="tech-category">
<h3> 컨테이너</h3>
<ul>
<li><strong>Docker</strong>: 컨테이너화</li>
<li><strong>Singularity</strong>: HPC 호환</li>
<li><strong>Conda</strong>: 패키지 관리</li>
</ul>
</div>


</div>
## 성능 향상

| 항목    | 기존 파이프라인 | Nextflow 버전 |
| ----- | -------- | ----------- |
| 재현성   | 수동 설정 필요 | 100% 자동화    |
| 확장성   | 단일 서버    | 멀티 플랫폼      |
| 유지보수  | 복잡한 스크립트 | 모듈화         |
| 오류 처리 | 수동 디버깅   | 자동 재시작      |

## 사용 방법

```bash
nextflow run main \
  --input samplesheet.csv \
  --outdir results \
  -profile docker
```

## 링크
- **GitHub Repository**: [e4m-hash/nf-core-funomic](https://github.com/e4m-hash/nf-core-funomic)
- **Original FunOMIC2**: [ManichanhLab/FunOMIC2](https://github.com/ManichanhLab/FunOMIC2)
- **Documentation**: [파이프라인 사용 가이드](https://github.com/e4m-hash/nf-core-funomic/wiki)
