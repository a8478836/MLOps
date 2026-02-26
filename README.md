계속 수정(Chat GPT 적극 활용하여 요약 중)

# MLOps
Ready for MLOps(feat. LLM, VLM etc.)

On-premise의 kubernetes에서 API 기반의 간단한 챗봇 구현하기

## 목적
On-premise 환경에서 Kubernetes를 구성하고, Nvidia Container를 사용하여 시중 제품들과 비슷하게 챗봇을 구현해본다.


# Envs
OS: Windows 11
GPU: RTX 5080 16GB
Memory: 32GB
Virtual env: WSL2 + Anaconda3

## Architecture

GitLab (Source Code)
   ↓
Jenkins (CI)
   - Kubernetes Agent (Kaniko)
   - Docker Image Build
   - Docker Private Registry Push
   ↓
Docker Private Registry (registry.local:5000)
   ↓
Argo CD (CD)
   - GitOps 방식
   - Kubernetes Deployment 자동 동기화
   ↓
Kubernetes Cluster


# Tools
1. Pytorch (dev. platform)
2. Kubernetes (container orchestration): v1.30
3. Helm (for charts)
4. Kaniko (for Container Image build)
5. Docker Private Registry
6. Nvidia Container plugin
7. Jenkins(for CI)
8. Argo CD(for CD)
9. Redis (Chat history)
10. Chorma (embedding DB)
11. git + gitlab
12. Penpot (architecture)
13. Grafana (visualization)
14. Vue3
15. Tailwind
16. Huggingface

# Current Works

# 🚀 MLOps / Backend CI·CD 인프라 구성 현황

## 🧭 전체 아키텍처 흐름

```text
Git Push
   ↓
Jenkins (CI)
   ↓
Docker Image Build (Kaniko)
   ↓
Private Registry Push (registry.local:5000)
   ↓
Argo CD (Image Updater)
   ↓
Kubernetes Deploy/Update
   ↓
Ingress → FastAPI 서비스 외부 접근
```

---

# 🧱 Kubernetes 환경

| 항목 | 내용 |
|------|------|
| 클러스터 환경 | WSL2 기반 Kubernetes |
| Ingress Controller | ingress-nginx |
| Service Type | NodePort |
| HTTP 포트 | **31302** |
| HTTPS 포트 | **31084** |

---

# ⚙ Jenkins (CI 영역)

## 설치 구조

| 항목 | 구성 |
|------|------|
| 설치 방식 | Helm Chart |
| 워크로드 타입 | StatefulSet |
| 설정 관리 | JCasC (Jenkins Configuration as Code) |
| Agent 실행 방식 | Kubernetes Plugin (동적 Pod 생성) |
| 빌드 컨테이너 | **Kaniko Agent Pod** |

## CI 동작 흐름

```text
GitLab Push
   → Webhook Trigger
   → Jenkins Job 실행
   → Kubernetes가 Kaniko Pod 생성
   → Dockerfile 기반 이미지 빌드
   → registry.local:5000 로 Push
```

## 해결된 주요 이슈

| 문제 | 해결 내용 |
|------|-----------|
| Webhook 200 응답이나 빌드 미실행 | Webhook URL에 Jenkins 포트 명시 |
| Kaniko OOM 발생 | Pod 리소스 제한 조정 |
| Harbor 인증/신뢰 문제 | Docker Private Registry로 변경 |
| 이미지 Push 실패 | Kaniko에서 registry 접근 설정 완료 |
| K8S gpu 인식 불가 | containerd config.toml Nvidia runtime 추가 |

---

# 📦 Docker Private Registry

| 항목 | 내용 |
|------|------|
| 레지스트리 주소 | **registry.local:5000** |
| 이미지 Push 주체 | Jenkins (Kaniko Agent) |
| 이미지 Pull 주체 | Kubernetes, Argo CD |
| 특징 | Docker daemon 없이 클러스터 내부 빌드/푸시 |

---

# 🚀 CD 영역 — Argo CD

## 구성

| 항목 | 내용 |
|------|------|
| 배포 방식 | GitOps |
| 자동 이미지 반영 | **Argo CD Image Updater** |
| 대상 Application 패턴 | `backend*`, `frontend*` |
| 배포 대상 | WSL2 Kubernetes 클러스터 |

## CD 동작 흐름

```text
1. Jenkins가 새 이미지 Push
2. Argo CD Image Updater가 태그 변경 감지
3. Git Repo의 K8s Manifest 이미지 태그 자동 수정
4. Argo CD가 변경 감지 후 Sync
5. Pod Rolling Update 수행
```

> ✅ 완전 자동 CD 파이프라인 구성 완료

---

# 🧠 애플리케이션 영역 — FastAPI

## Backend 컨테이너 구성

| 항목 | 내용 |
|------|------|
| 프레임워크 | **FastAPI** |
| 컨테이너 포트 | 8000 |
| GPU 사용 | `nvidia.com/gpu` 리소스 요청 |
| 모델 파일 | PVC 마운트 (사전 다운로드) |

## 배포 후 검증 결과

| 테스트 항목 | 상태 |
|-------------|------|
| Pod 생성 | 정상 |
| 컨테이너 실행 | 정상 |
| API 엔드포인트 호출 | 응답 성공 |
| Ingress 통한 외부 접근 | 성공 |

---

# 🌐 네트워크 구조

```text
Client
   ↓
NodePort (31302 / 31084)
   ↓
Ingress-Nginx
   ↓
Service (ClusterIP)
   ↓
FastAPI Pod
```

---

# 📊 현재 인프라 성숙도

| 영역 | 상태 |
|------|------|
| Kubernetes 환경 | 안정 |
| CI 자동화 | 완료 |
| 이미지 빌드 | Kaniko 기반 무상태 빌드 |
| 레지스트리 | Private Registry 운영 |
| CD 자동 배포 | 완료 (Image Updater 포함) |
| 애플리케이션 검증 | FastAPI 동작 확인 |
| GPU 워크로드 준비 | 구성 완료 |

---

# ✅ 최종 요약

> **Git Push → 자동 빌드 → 이미지 푸시 → 자동 배포 → FastAPI 서비스 외부 접근까지 완전 자동화된 CI/CD 파이프라인이 구축된 상태**

---

# 🔜 향후 확장 가능 영역

1. HTTPS + 도메인 + 인증 체계
2. 모니터링 스택 (Prometheus / Grafana)
3. 모델 버전 관리 전략
4. Blue-Green / Canary 배포 전략
5. Agentic AI --> 확장 방향
