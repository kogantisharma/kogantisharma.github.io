---
title: "End-to-End MLOps Pipeline on AWS"
excerpt: >
  Automated ML training, evaluation, and deployment pipeline using SageMaker Pipelines,
  MLflow model registry, and Terraform-managed infrastructure. Full observability and
  automated retraining on data drift.
date: 2024-06-01
header:
  teaser      : /assets/images/mlops-thumb.png
  overlay_color: "#0d1117"
  overlay_filter: "0.5"
categories:
  - MLOps
  - AWS
  - SageMaker
tags:
  - sagemaker
  - mlflow
  - terraform
  - github-actions
  - model-monitoring
  - python
  - evidently
sidebar:
  - title   : "Stack"
    text    : "Python · SageMaker Pipelines · MLflow · Terraform · GitHub Actions · Evidently AI"
  - title   : "Repository"
    text    : "[github.com/kogantisharma/mlops-aws-pipeline](https://github.com/kogantisharma/mlops-aws-pipeline)"
  - title   : "Status"
    text    : "🟢 Complete"
  - title   : "Role"
    text    : "Solo architect & engineer"
---

## Overview

A production-grade, end-to-end MLOps pipeline demonstrating the full ML lifecycle on AWS:
from raw data ingestion through to model serving, monitoring, and automated retraining.
Designed to reflect the standards I would enforce on an enterprise ML platform team.

---

## Pipeline Architecture

```
S3 (raw data)
      │
      ▼
SageMaker Pipeline ──────────────────────────────────────────────────
      ├── Step 1: Data Validation (Great Expectations)
      ├── Step 2: Preprocessing (SKLearn Processing Job)
      ├── Step 3: Training (XGBoost Training Job)
      ├── Step 4: Evaluation (custom metrics → MLflow)
      ├── Step 5: Conditional approval gate (F1 ≥ 0.85)
      └── Step 6: Model registration (MLflow Model Registry → Staging)
                                   │
                      Manual approval (or auto if >0.90 F1)
                                   │
                                   ▼
                     SageMaker Endpoint (real-time inference)
                     + A/B traffic split (CodeDeploy hooks)
                                   │
                                   ▼
                     Evidently AI monitoring (data drift)
                                   │
                     Drift detected? → EventBridge → retrigger pipeline
```

---

## Key Components

### Infrastructure as Code
All AWS resources provisioned with Terraform. Modules cover: S3 buckets, IAM roles, SageMaker domain, MLflow tracking server (on ECS), and EventBridge rules for retraining triggers.

### Model Registry
MLflow Model Registry tracks all experiments. Models transition through `Staging → Production → Archived` stages via both automated gates (metric thresholds) and optional manual approval.

### Drift Monitoring
Evidently AI generates HTML reports and JSON metrics comparing production inference data against the training baseline. EventBridge ingests CloudWatch metric alarms and triggers a new pipeline execution when PSI (Population Stability Index) exceeds threshold.

### A/B Deployment
New model versions receive 10% of traffic via SageMaker endpoint variant weighting. After 24h with error rate <0.5%, traffic shifts to 100% on the new variant using a CodeDeploy lifecycle hook.

---

## CI/CD for the Pipeline Itself

The pipeline *definition* is also version-controlled and deployed via GitHub Actions:

```yaml
- name: Upsert SageMaker Pipeline definition
  run: |
    python pipeline/definition.py --upsert
  env:
    AWS_REGION          : eu-west-1
    SAGEMAKER_ROLE_ARN  : ${{ secrets.SAGEMAKER_ROLE_ARN }}
```

This means the pipeline DAG itself is reviewed in PRs, tested in CI, and deployed atomically — treating ML infrastructure with the same rigour as application code.

---

[View on GitHub →](https://github.com/kogantisharma/mlops-aws-pipeline){: .btn .btn--primary}
[View all projects →](/projects/){: .btn .btn--inverse}
