---
layout: splash
permalink: /
hidden: true
header:
  overlay_color: "#0d1117"
  overlay_filter: "0.6"
  overlay_image: /assets/images/hero-bg.jpg
  actions:
    - label: "<i class='fas fa-project-diagram'></i>&nbsp; View Projects"
      url: /projects/
      btn_class: "btn--primary btn--large"
    - label: "<i class='fas fa-file-pdf'></i>&nbsp; Download CV"
      url: /assets/files/Sri_Koganti_CV.pdf
      btn_class: "btn--inverse btn--large"
title: "Sri Koganti"
excerpt: >
  **Senior Cloud & MLOps Engineer** &mdash; Cork, Ireland<br>
  Architecting production AI/ML infrastructure.<br>
  10+ years in cloud platforms at Fidelity & Forcepoint.

intro:
  - excerpt: >
      I bridge the gap between **deep infrastructure engineering** and **modern AI/ML delivery**.
      With a decade of experience designing resilient cloud platforms for financial services and
      cybersecurity at enterprise scale, I now specialise in **MLOps pipelines**, **LLM-powered
      applications**, and **AI platform engineering**. I'm actively seeking MLOps and AI
      Engineering roles where architecture and automation intersect.

feature_row:
  - image_path : /assets/images/rag-thumb.png
    alt         : "RAG Career Assistant architecture diagram"
    title       : "RAG-Based Career Assistant"
    excerpt     : >
      Production-grade LLM application built with **LangChain, Pinecone, and GPT-4**.
      Full CI/CD pipeline via GitHub Actions. Containerised with Docker, deployed to AWS ECS.
    url         : /projects/rag-career-assistant/
    btn_label   : "View Project →"
    btn_class   : "btn--primary"

  - image_path : /assets/images/mlops-thumb.png
    alt         : "MLOps pipeline architecture"
    title       : "MLOps Pipeline on AWS"
    excerpt     : >
      End-to-end ML pipeline using **SageMaker Pipelines, MLflow, and Terraform**.
      Automated retraining triggers, model registry, A/B traffic splitting, and drift monitoring.
    url         : /projects/mlops-aws-pipeline/
    btn_label   : "View Project →"
    btn_class   : "btn--primary"

  - image_path : /assets/images/platform-thumb.png
    alt         : "Cloud AI platform on Kubernetes"
    title       : "Cloud AI Serving Platform"
    excerpt     : >
      Kubernetes-native model serving platform using **KServe, Helm, and ArgoCD**.
      Supports multi-model, multi-tenant deployments with autoscaling and Prometheus metrics.
    url         : /projects/cloud-ai-platform/
    btn_label   : "View Project →"
    btn_class   : "btn--primary"

feature_row2:
  - image_path : /assets/images/skills-banner.png
    alt         : "Technology stack"
    title       : "Technology Stack"
    excerpt     : >
      **Cloud:** AWS (EKS, SageMaker, Lambda, S3) · GCP (Vertex AI, GKE) · Azure (AKS, Azure ML)<br>
      **MLOps:** MLflow · Kubeflow · SageMaker Pipelines · DVC · Weights & Biases<br>
      **AI/LLM:** LangChain · HuggingFace Transformers · OpenAI API · Pinecone · FAISS<br>
      **DevOps:** Kubernetes · Docker · Terraform · ArgoCD · Helm · GitHub Actions<br>
      **Languages:** Python · Bash · HCL · YAML · Go (working proficiency)
    url         : /cv/
    btn_label   : "Full CV →"
    btn_class   : "btn--inverse"
---

{% include feature_row id="intro" type="center" %}

{% include feature_row %}

{% include feature_row id="feature_row2" type="left" %}
