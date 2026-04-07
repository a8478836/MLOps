# 🚀 MLOps (LLM / VLM 기반 On-Premise AI 시스템)

---

# 🎯 프로젝트 개요

On-premise Kubernetes 환경에서 GPU 기반 LLM/VLM을 활용하여 실제 서비스 수준의 챗봇 시스템을 구축하고, CI/CD + MLOps 파이프라인을 완성하는 것을 목표로 한다.

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
* Redis (Chat Memory, Async)
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

Client
↓
Ingress
↓
FastAPI
↓
LangGraph 실행 (router → memory_check → summarize → prompt / rag → prompt)
↓
LLM Streaming (vLLM)
↓
Frontend 렌더링
↓
Redis Memory Async 저장

---

# 🧠 AI 시스템 구조 (업데이트)

## 기존 구조

LLM → Markdown → Render

## 개선 구조 (LangGraph 도입)

[User Input]
↓
[Router (Intent)]
↓
[RAG (조건부 실행)]
↓
[Memory Check]
↓
[Summarization (조건부)]
↓
[Prompt 구성 (Short-term + Summary + RAG Context)]
↓
[LLM Streaming]
↓
[Frontend 전달]
↓
[Redis Memory (Async 저장)]

---

# 🔥 LangGraph 도입 (핵심 변화)

## 목적

* LLM 처리 로직을 단일 함수 → 그래프 기반 구조로 확장
* Memory / RAG / Prompt 완전 분리
* Agent 구조 확장 기반 확보

---

## Graph 구조 (최종)

router → (rag | memory_check)
rag → prompt
memory_check → (summarize | prompt)
summarize → prompt
prompt → END

---

## State 구조 (업데이트)

```python
class GraphState(TypedDict, total=False):
    messages: Annotated[List[BaseMessage], add_messages]
    context: str
    formatted_prompt: str
    route: str
    request_id: str
    summary: str
```

---

## Node 구성 (최종)

| Node           | 역할                       |
| -------------- | ------------------------ |
| router         | 사용자 요청 분석 및 RAG 여부 판단    |
| retrieve (rag) | Chroma 기반 문서 검색          |
| memory_check   | 메시지 길이에 따른 요약 여부 판단      |
| summarize      | 장기 대화 요약 (token 절약)      |
| prompt         | 최종 prompt 생성 + tokenizer |

---

# 🧠 Memory 구조 (핵심)

## Short-term Memory

* Redis Checkpointer 기반
* thread_id 기준으로 메시지 자동 누적
* LangGraph `add_messages`로 append 관리

## Long-term Memory

* Redis Store 기반
* user_id 기준으로 사용자 단위 저장 가능
* 현재는 구조만 확보, 확장 가능 상태

## Memory 처리 전략

* 최근 메시지 N개 유지 (trimming)
* 일정 threshold 초과 시 summarize 실행
* summary를 system prompt에 삽입하여 context 유지 (Running Summary)

---

# 📊 현재 성숙도

| 영역           | 상태                      |
| ------------ | ----------------------- |
| Kubernetes   | ✅ 안정                    |
| CI           | ✅ 완료                    |
| CD           | ✅ 자동화 완료                |
| GPU 활용       | ✅ 가능                    |
| FastAPI 서비스  | ✅ 정상                    |
| Redis Memory | ✅ 안정 (Short/Long 분리 완료) |
| Chroma RAG   | ⚙ 진행 중                  |
| LangGraph    | ✅ 안정화 완료                |
| Streaming    | ✅ 완전 async 처리           |
| Frontend 렌더링 | ⚙ 개선 중                  |

---

# 🚨 해결한 주요 문제

* LangGraph async/sync 충돌 문제
* `await dict` 에러 (비동기/동기 혼용 문제)
* HumanMessage / dict 타입 불일치 문제
* tokenizer 입력 포맷 문제
* Redis 인증 및 index 문제
* RedisSaver sync → async 전환
* Graph node TypeError
* lambda / partial async 인식 문제
* streaming + memory update 충돌 문제
* message duplication / trimming 이슈
* memory 반영 안 되는 문제 (prompt 구조 문제)
* summarize 도입으로 context 유지 문제 해결

---

# 🔜 향후 확장 계획

## 1. AI 기능 확장

* RAG 고도화 (Re-ranker)
* Memory importance scoring
* Tool calling (DB / API)

## 2. Agentic AI

* Planner → Executor → Critic 구조 도입

## 3. 배포 전략

* Blue-Green Deployment
* Canary Deployment

## 4. Observability

* Prometheus
* Grafana
* LLM latency / token metrics

## 5. 보안 / 네트워크

* HTTPS (Let's Encrypt)
* 인증 / 인가 (JWT / OAuth)

---

# 🎯 최종 목표

단순 챗봇이 아니라 Kubernetes 기반 Agentic AI 플랫폼 구축

---

# 💡 한 줄 요약

CI/CD + GPU + LLM + LangGraph + Async Memory까지 결합된
**실서비스 수준 AI 시스템 완성 단계**
