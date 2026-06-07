---
title: "Cloud AI Serving Platform"
excerpt: >
  Kubernetes-native, multi-tenant model serving platform using KServe, Helm, and ArgoCD.
  Supports real-time and batch inference with autoscaling, canary rollouts, and Prometheus metrics.
date: 2024-03-01
header:
  teaser      : /assets/images/platform-thumb.png
  overlay_color: "#0d1117"
  overlay_filter: "0.5"
categories:
  - Platform Engineering
  - MLOps
  - Kubernetes
tags:
  - kserve
  - kubernetes
  - helm
  - argocd
  - prometheus
  - gitops
  - istio
sidebar:
  - title   : "Stack"
    text    : "KServe · Kubernetes (EKS) · Istio · Helm · ArgoCD · Prometheus · Grafana"
  - title   : "Repository"
    text    : "[github.com/kogantisharma/cloud-ai-platform](https://github.com/kogantisharma/cloud-ai-platform)"
  - title   : "Status"
    text    : "🟢 Complete"
  - title   : "Role"
    text    : "Solo architect & engineer"
---

## Overview

An internal developer platform (IDP) for model serving — designed to let data scientists deploy
models to production without requiring Kubernetes knowledge. Engineers submit an `InferenceService`
manifest; the platform handles routing, scaling, TLS, canary rollouts, and observability.

---

## Architecture

```
Git (model manifests)
      │
      ▼
ArgoCD (GitOps) ── syncs ──► EKS Cluster
                               ├── Istio Service Mesh
                               ├── KServe InferenceService (per model)
                               │     ├── Transformer (pre/post processing)
                               │     └── Predictor (model server: Triton / TorchServe / sklearn)
                               ├── KEDA (event-driven autoscaling on queue depth)
                               └── Prometheus + Grafana (metrics per model endpoint)
```

### Namespace-per-team Isolation
Each team gets a dedicated Kubernetes namespace with:
- Resource quotas (CPU/memory/GPU limits)
- RBAC scoped to their namespace (can deploy, cannot modify cluster resources)
- Network policies preventing cross-namespace traffic
- Separate Prometheus scrape targets for per-team dashboards

---

## Key Features

| Feature | Implementation |
|---------|---------------|
| **Canary rollouts** | KServe `canaryTrafficPercent` + Istio VirtualService weights |
| **Autoscaling** | KEDA scaling on SQS queue depth for async inference; HPA on RPS for sync |
| **GPU node pools** | Separate EKS node group with NVIDIA device plugin; GPU requested only when needed |
| **Model versioning** | Each `InferenceService` references a versioned S3 URI; rollback = git revert |
| **mTLS** | Istio enforces mutual TLS for all service-to-service communication |
| **Cost attribution** | Kubecost labels map spend to team and model namespace |

---

## GitOps Deployment Flow

A data scientist deploying a new model submits a PR:

```yaml
# models/fraud-detector/v2.yaml
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: fraud-detector
  namespace: team-risk
spec:
  predictor:
    model:
      modelFormat:
        name: sklearn
      storageUri: s3://ml-models/fraud-detector/v2/
  canaryTrafficPercent: 10   # Start at 10% → promote to 100% after validation
```

ArgoCD detects the diff, syncs to EKS, and the canary goes live. After validation, the
`canaryTrafficPercent` is updated to 100 via another PR — giving a full audit trail in git.

---

[View on GitHub →](https://github.com/kogantisharma/cloud-ai-platform){: .btn .btn--primary}
[View all projects →](/projects/){: .btn .btn--inverse}
