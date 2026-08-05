---
title: "Projects"
permalink: /projects/
layout: default
---

A selection of AI and MLOps projects that demonstrate how I apply enterprise platform engineering practices to modern ML systems.

Each project emphasizes production readiness: reproducible workflows, CI/CD, observability, and explicit architecture decisions.

---

## Featured Projects

### [RAG-Based Career Assistant](/projects/rag-career-assistant/)

Production-grade Retrieval-Augmented Generation assistant that answers career questions with grounded context and source-aware responses.

- **Stack:** Python, FastAPI, LangChain, OpenAI embeddings and GPT-4, Pinecone, Docker, AWS ECS
- **Focus:** Retrieval quality, prompt design, containerized deployment, and automated delivery
- **Why it matters:** Demonstrates practical LLM application engineering beyond notebook prototypes

[View project details](/projects/rag-career-assistant/){: .btn .btn--primary}

---

### [End-to-End MLOps Pipeline on AWS](/projects/mlops-aws-pipeline/)

Automated ML lifecycle pipeline from data validation and training to model registration, deployment, monitoring, and drift-triggered retraining.

- **Stack:** SageMaker Pipelines, MLflow, Terraform, GitHub Actions, Evidently
- **Focus:** Quality gates, model governance, reproducibility, and feedback loops
- **Why it matters:** Shows how to run ML systems with platform-level controls and operational discipline

[View project details](/projects/mlops-aws-pipeline/){: .btn .btn--primary}

---

### [Cloud AI Serving Platform](/projects/cloud-ai-platform/)

Kubernetes-native internal platform for multi-tenant model serving with namespace isolation, autoscaling, observability, and progressive rollout support.

- **Stack:** Kubernetes (EKS), KServe, Istio, Helm, ArgoCD, Prometheus, Grafana
- **Focus:** Self-service model deployment, policy-driven multi-tenancy, and GitOps operations
- **Why it matters:** Reflects the type of AI platform architecture required in enterprise environments

[View project details](/projects/cloud-ai-platform/){: .btn .btn--primary}

---

## Looking Ahead

I am currently expanding this portfolio with additional applied AI engineering work, including:

- LLMOps guardrails and evaluation workflows
- Cost-aware inference architecture patterns
- AI reliability engineering playbooks for production teams

---
