# 🚀 MLOps (LLM / VLM 기반 On-Premise AI 시스템)

## 🎯 프로젝트 개요

On-premise Kubernetes 환경에서 GPU 기반 LLM/VLM을 활용하여
**실제 서비스 수준의 챗봇 시스템을 구축하고, CI/CD + MLOps 파이프라인을 완성하는 것**을 목표로 한다.

---

# 🧠 핵심 목표

* Kubernetes 기반 AI 서비스 운영
* GPU 활용 LLM 서빙 환경 구축
* 완전 자동화된 CI/CD 파이프라인 구성
* RAG + Chat Memory 기반 챗봇 구현
* LangGraph 기반 Agentic AI 구조 확장

---

# 🖥️ Environments

| 항목         | 내용               |
| ---------- | ---------------- |
| OS         | Windows 11       |
| GPU        | RTX 5080 16GB    |
| Memory     | 32GB             |
| 가상환경       | WSL2 + Anaconda3 |
| Kubernetes | v1.30            |

---

# 🏗️ 전체 아키텍처

```text
GitLab (Source Code)
   ↓
Jenkins (CI)
   ↓
Kaniko (Docker Build)
   ↓
Private Registry (registry.local:5000)
   ↓
Argo CD (CD / GitOps)
   ↓
Kubernetes Cluster
   ↓
FastAPI (LLM Backend)
   ↓
LangGraph (AI Workflow Engine)
   ↓
Frontend (Vue3)
```

---

# 🧰 Tech Stack

## Infra

* Kubernetes
* Helm
* Jenkins
* Argo CD
* Docker Private Registry
* Kaniko
* Nvidia Container Toolkit

## AI / Backend

* FastAPI
* PyTorch
* Huggingface
* Redis (Chat Memory)
* Chroma (Vector DB)
* LangChain (LLM Toolkit)
* LangGraph (Workflow Orchestration)

## Frontend

* Vue3
* Tailwind

## Observability

* Grafana (예정)

---

# ⚙️ CI/CD 파이프라인

```text
Git Push
   ↓
Jenkins (Webhook Trigger)
   ↓
Kaniko Pod 생성 (Kubernetes Agent)
   ↓
Docker Image Build
   ↓
registry.local:5000 Push
   ↓
Argo CD Image Updater
   ↓
Git Manifest 자동 수정
   ↓
Kubernetes 자동 배포
```

---

# 🧱 Kubernetes 구성

| 항목           | 내용             |
| ------------ | -------------- |
| Ingress      | ingress-nginx  |
| Service Type | NodePort       |
| HTTP         | 31302          |
| HTTPS        | 31084          |
| GPU          | nvidia.com/gpu |

---

# 📦 Backend (FastAPI)

## 구성

| 항목        | 내용      |
| --------- | ------- |
| Framework | FastAPI |
| Port      | 8000    |
| GPU       | 활성화     |
| Model     | PVC 마운트 |

---

## API 흐름 (현재)

```text
Client
   ↓
Ingress
   ↓
FastAPI
   ↓
LLM 호출
   ↓
Markdown 처리
   ↓
Frontend 렌더링
```

---

# 🧠 AI 시스템 구조 (업데이트)

## 기존 구조

```text
LLM → Markdown → Render
```

---

## 개선 구조 (LangGraph 도입)

```text
[User Input]
   ↓
[Intent 분석]
   ↓
[RAG (Chroma)]
   ↓
[LLM]
   ↓
[Markdown Normalize]
   ↓
[Syntax Highlight (Shiki)]
   ↓
[Sanitize]
   ↓
[Redis Memory 저장]
   ↓
[Frontend 전달]
```

---

# 🔥 LangGraph 도입 (핵심 변화)

## 목적

* LLM 처리 로직을 **단일 함수 → 그래프 기반 구조로 확장**
* Markdown / Rendering / Memory / RAG 분리
* Agent 구조 확장 기반 확보

---

## Graph 구조

```text
intent → retrieve → llm → markdown_fix → render → memory
```

---

## State 구조

```ts
type GraphState = {
  input: string
  messages: any[]
  retrieved_docs?: string[]
  llm_output?: string
  normalized_markdown?: string
  final_html?: string
  intent?: "chat" | "code" | "rag"
}
```

---

## Node 구성

| Node         | 역할           |
| ------------ | ------------ |
| intent       | 사용자 요청 분석    |
| retrieve     | Chroma 검색    |
| llm          | 응답 생성        |
| markdown_fix | markdown 안정화 |
| render       | HTML 변환      |
| memory       | Redis 저장     |

---

# 📊 현재 성숙도

| 영역           | 상태       |
| ------------ | -------- |
| Kubernetes   | ✅ 안정     |
| CI           | ✅ 완료     |
| CD           | ✅ 자동화 완료 |
| GPU 활용       | ✅ 가능     |
| FastAPI 서비스  | ✅ 정상     |
| Redis Memory | ✅ 적용     |
| Chroma RAG   | ⚙ 진행 중   |
| LangGraph    | 🚀 도입 시작 |
| Frontend 렌더링 | ⚙ 개선 중   |

---

# 🚨 해결한 주요 문제

* Jenkins Webhook 미동작
* Kaniko OOM
* Registry 인증 문제
* Kubernetes GPU 인식 문제
* Markdown 코드블럭 깨짐
* Syntax Highlight 문제

---

# 🔜 향후 확장 계획

## 1. AI 기능 확장

* RAG 고도화 (Re-ranker)
* Multi-turn memory 강화
* Tool calling (DB / API)

---

## 2. Agentic AI

```text
Planner → Executor → Critic 구조 도입
```

---

## 3. 배포 전략

* Blue-Green Deployment
* Canary Deployment

---

## 4. Observability

* Prometheus
* Grafana
* LLM latency / token metrics

---

## 5. 보안 / 네트워크

* HTTPS (Let's Encrypt)
* 인증 / 인가 (JWT / OAuth)

---

# 🎯 최종 목표

> 단순 챗봇이 아니라
> **Kubernetes 기반 Agentic AI 플랫폼 구축**

---

# 💡 한 줄 요약

> **CI/CD + GPU + LLM + LangGraph까지 결합된 “실서비스 수준 AI 시스템” 구축 완료 단계**
