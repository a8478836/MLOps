계속 수정

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
6. Nvidia Container
7. Jenkins(for CI)
8. Argo CD(for CD)
9. DB
10. git + gitlab
11. Penpot (architecture)
12. Grafana (visualization)

# Current Works

## 현재 인프라 구성 상태
### 🧱 Kubernetes 환경

WSL2 기반 Kubernetes 클러스터

ingress-nginx 사용 (NodePort)

HTTP : 31302

HTTPS : 31084

### ⚙ Jenkins

Helm chart로 설치

StatefulSet 구성

JCasC(Configuration as Code) 사용

Kubernetes Plugin으로 Pod Agent 생성

Kaniko Agent Pod 사용


### 📦 Docker Private Registry

초반 Harbor 적용 중 도메인 기반 인증/신뢰 이슈 문제로 Docker Private Registry로 변경

Address: registry.local:5000

Jenkins(Kaniko agent 생성)에서 이미지 push

Kubernetes / Argo CD에서 이미지 pull
