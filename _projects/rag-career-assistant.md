---
title: "RAG-Based Career Assistant"
excerpt: >
  Production-grade LLM application using Retrieval-Augmented Generation.
  Built with LangChain, Pinecone, GPT-4, and deployed to AWS ECS via GitHub Actions.
date: 2024-09-01
header:
  image       : /assets/images/rag-career-banner.png
  teaser      : /assets/images/rag-thumb.png
  overlay_color: "#0d1117"
  overlay_filter: "0.5"
categories:
  - MLOps
  - LLM
  - AWS
tags:
  - langchain
  - rag
  - gpt-4
  - pinecone
  - aws-ecs
  - docker
  - github-actions
  - python
sidebar:
  - title   : "Stack"
    text    : "Python · LangChain · GPT-4 · Pinecone · FastAPI · Docker · AWS ECS · GitHub Actions"
  - title   : "Repository"
    text    : "[github.com/kogantisharma/rag-career-assistant](https://github.com/kogantisharma/rag-career-assistant)"
  - title   : "Status"
    text    : "🟢 Live"
  - title   : "Role"
    text    : "Solo architect & engineer"
gallery:
  - url        : /assets/images/rag-arch-full.png
    image_path : /assets/images/rag-arch-full.png
    alt        : "Full RAG pipeline architecture diagram"
    title      : "End-to-end RAG pipeline architecture"
---

## Overview

The **RAG-Based Career Assistant** is a production-deployed conversational AI application that
answers questions about my professional background by grounding GPT-4 responses in retrieved
documents from a vector database. It demonstrates the full MLOps and LLM engineering stack:
from document ingestion pipelines through to containerised deployment with automated CI/CD.

The project was designed with production constraints in mind — not just a Jupyter notebook demo,
but a maintainable, observable, and safely deployable system.

**Live demo:** [rag-assistant.kogantisharma.io](https://rag-assistant.kogantisharma.io)

---

## Problem Statement

Standard LLM responses about a person's career are hallucinated or generic. Recruiters and
hiring managers asking "What cloud cost savings has Sri delivered?" should receive a grounded,
cited answer from actual project documentation — not a fabricated one.

**Solution:** A RAG pipeline that retrieves semantically relevant document chunks from a
Pinecone vector store and injects them into a structured GPT-4 prompt, with source citation
in every response.

---

## System Architecture

```
                 ┌─────────────────────────────────────────────────────┐
                 │              INGESTION PIPELINE (offline)           │
                 │                                                     │
                 │  PDF / Markdown docs                                │
                 │       │                                             │
                 │       ▼                                             │
                 │  LangChain Document Loader                          │
                 │       │                                             │
                 │       ▼                                             │
                 │  RecursiveCharacterTextSplitter (512t / 50 overlap) │
                 │       │                                             │
                 │       ▼                                             │
                 │  OpenAI text-embedding-3-small                      │
                 │       │                                             │
                 │       ▼                                             │
                 │  Pinecone (Index: career-docs, 1536-dim)            │
                 └─────────────────────────────────────────────────────┘

                 ┌─────────────────────────────────────────────────────┐
                 │              QUERY PIPELINE (runtime)               │
                 │                                                     │
                 │  User question (HTTP POST /ask)                     │
                 │       │                                             │
                 │       ▼                                             │
                 │  OpenAI text-embedding-3-small                      │
                 │       │                                             │
                 │       ▼                                             │
                 │  Pinecone similarity search (top-k=5)               │
                 │       │                                             │
                 │       ▼                                             │
                 │  Prompt template injection (system + context + q)   │
                 │       │                                             │
                 │       ▼                                             │
                 │  GPT-4 (gpt-4-turbo-preview)                        │
                 │       │                                             │
                 │       ▼                                             │
                 │  Response + source citations (JSON)                 │
                 └─────────────────────────────────────────────────────┘
```

### Key Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Vector DB** | Pinecone (managed) | Eliminates ops overhead; serverless tier fits cost model |
| **Embedding model** | `text-embedding-3-small` | 5x cheaper than ada-002, comparable quality for this domain |
| **Chunk strategy** | Recursive character split, 512 tokens, 50 overlap | Balances context preservation with retrieval precision |
| **LLM** | GPT-4-turbo-preview | Best factual accuracy; temperature=0 for deterministic responses |
| **API framework** | FastAPI | Async-native, automatic OpenAPI docs, lightweight for containers |
| **Container runtime** | AWS ECS (Fargate) | Serverless containers — no Kubernetes overhead for a single-service app |

---

## CI/CD Pipeline

The entire delivery pipeline is codified in `.github/workflows/deploy.yml`. Every push to `main`
triggers a full build, test, and deployment sequence with no manual steps.

```yaml
# .github/workflows/deploy.yml
name: Build, Test & Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  AWS_REGION      : eu-west-1
  ECR_REPOSITORY  : rag-career-assistant
  ECS_SERVICE     : rag-assistant-svc
  ECS_CLUSTER     : rag-assistant-cluster
  CONTAINER_NAME  : rag-assistant

jobs:
  # ── 1. Quality gate ──────────────────────────────────────────────────
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          cache: pip

      - name: Install dependencies
        run: pip install -r requirements-dev.txt

      - name: Lint (ruff)
        run: ruff check .

      - name: Type check (mypy)
        run: mypy app/

      - name: Unit tests (pytest)
        run: pytest tests/unit/ -v --cov=app --cov-report=xml
        env:
          OPENAI_API_KEY  : ${{ secrets.OPENAI_API_KEY_TEST }}
          PINECONE_API_KEY: ${{ secrets.PINECONE_API_KEY_TEST }}

      - name: Upload coverage
        uses: codecov/codecov-action@v4

  # ── 2. Build & push container image ──────────────────────────────────
  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    outputs:
      image: ${{ steps.build-image.outputs.image }}

    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id    : ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region           : ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build, tag & push image to ECR
        id: build-image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG   : ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG \
                       -t $ECR_REGISTRY/$ECR_REPOSITORY:latest .
          docker push $ECR_REGISTRY/$ECR_REPOSITORY --all-tags
          echo "image=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG" >> $GITHUB_OUTPUT

  # ── 3. Deploy to ECS ─────────────────────────────────────────────────
  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id    : ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region           : ${{ env.AWS_REGION }}

      - name: Update ECS task definition with new image
        id: task-def
        uses: aws-actions/amazon-ecs-render-task-definition@v1
        with:
          task-definition: ecs/task-definition.json
          container-name : ${{ env.CONTAINER_NAME }}
          image          : ${{ needs.build.outputs.image }}

      - name: Deploy to ECS (rolling update)
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition   : ${{ steps.task-def.outputs.task-definition }}
          service           : ${{ env.ECS_SERVICE }}
          cluster           : ${{ env.ECS_CLUSTER }}
          wait-for-service-stability: true
```

### Pipeline Characteristics

- **Zero-downtime deploys:** ECS rolling update strategy — new tasks start before old ones drain
- **No secrets in code:** All API keys injected via GitHub Actions secrets → ECS task environment variables (backed by AWS Secrets Manager)
- **PRs gated:** `test` job runs on all PRs; build/deploy only triggers on `main` merges
- **Full audit trail:** Every deployment tagged with the git commit SHA; ECR image retention policy retains last 10 images

---

## Repository Structure

```
rag-career-assistant/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI app entrypoint
│   ├── routes/
│   │   └── ask.py           # POST /ask endpoint
│   ├── services/
│   │   ├── retriever.py     # Pinecone similarity search
│   │   ├── generator.py     # GPT-4 chain with prompt template
│   │   └── embedder.py      # OpenAI embedding wrapper
│   └── models/
│       └── schemas.py       # Pydantic request/response models
├── ingestion/
│   ├── ingest.py            # Document ingestion pipeline
│   └── documents/           # Source documents (gitignored for private data)
├── tests/
│   ├── unit/                # Mocked unit tests (no API calls)
│   └── integration/         # End-to-end tests (run manually)
├── ecs/
│   └── task-definition.json # ECS task definition (Terraform-rendered)
├── terraform/
│   ├── main.tf              # ECS cluster, service, ALB, ECR
│   ├── variables.tf
│   └── outputs.tf
├── .github/
│   └── workflows/
│       └── deploy.yml       # Full CI/CD pipeline
├── Dockerfile
├── requirements.txt
├── requirements-dev.txt
└── README.md
```

---

## Key Technical Details

### Prompt Engineering

The system prompt is structured to enforce grounding and citation:

```python
SYSTEM_PROMPT = """You are a professional career assistant for Sri Koganti,
a Senior Cloud & MLOps Engineer. Answer questions using ONLY the provided context.
If the context does not contain sufficient information, say so — do not fabricate.
Always cite the source document name at the end of your response in [brackets]."""

def build_prompt(context_chunks: list[str], question: str) -> list[dict]:
    context = "\n\n---\n\n".join(context_chunks)
    return [
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user",   "content": f"Context:\n{context}\n\nQuestion: {question}"}
    ]
```

### Retrieval Quality

After testing multiple strategies, **hybrid search** (dense + sparse) was found to improve
recall by ~18% on domain-specific queries vs. dense-only. Implemented using Pinecone's
sparse-dense hybrid search with BM25 for the sparse component.

### Observability

- **Structured logging:** Every request/response logged as JSON to CloudWatch Logs
- **Metrics:** Custom metrics pushed to CloudWatch: `retrieval_latency_ms`, `llm_latency_ms`, `token_usage`
- **Tracing:** AWS X-Ray instrumented on the FastAPI app for end-to-end request tracing
- **Alerts:** CloudWatch alarm triggers PagerDuty if error rate exceeds 1% over 5 minutes

---

## Infrastructure (Terraform)

The AWS infrastructure is fully codified in Terraform. Core resources:

```hcl
# ECS Fargate service with ALB
module "ecs_service" {
  source  = "terraform-aws-modules/ecs/aws"
  version = "~> 5.0"

  cluster_name = "rag-assistant-cluster"

  fargate_capacity_providers = {
    FARGATE_SPOT = { default_capacity_provider_strategy = { weight = 100 } }
  }
}

# Secrets injected at runtime — never in task definition plaintext
resource "aws_secretsmanager_secret" "openai_key" {
  name = "rag-assistant/openai-api-key"
}
```

**Security posture:**
- Secrets Manager for all API keys (never plaintext in task definitions or environment variables in code)
- ECS task role with least-privilege IAM policies
- ALB with WAF and rate limiting (100 req/min per IP)
- Private subnets for ECS tasks; ALB is the only public-facing resource

---

## Lessons Learned

1. **Chunk size matters more than model choice.** Early versions with 1024-token chunks had poor precision — the retrieved context was too diluted. Dropping to 512 with overlap significantly improved answer quality.
2. **Temperature=0 is non-negotiable for factual Q&A.** Any temperature >0 introduced subtle hallucinations even with grounding.
3. **Test the retrieval layer independently.** Unit testing the LLM chain is hard; mocking the retriever and asserting on prompt construction caught 80% of bugs before hitting the API.
4. **ECS Fargate Spot cuts costs by 70%** for this workload (non-time-critical, stateless). Implemented Spot with fallback to on-demand for zero-downtime.

---

## What's Next

- [ ] Streaming responses via Server-Sent Events (SSE) for perceived latency improvement
- [ ] Multi-turn conversation with session memory (DynamoDB-backed)
- [ ] Evaluation framework using RAGAS for automated retrieval and generation quality scoring
- [ ] Fine-tuned embedding model on domain-specific documents

---

[View on GitHub →](https://github.com/kogantisharma/rag-career-assistant){: .btn .btn--primary}
[View all projects →](/projects/){: .btn .btn--inverse}
