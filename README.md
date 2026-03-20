# Ready for MLOps (feat. LLM, VLM etc.)
On-premise Kubernetes에서 API 기반 챗봇 구현하기

## 목적
On-premise 환경에서 Kubernetes + NVIDIA GPU를 활용하여  
시중 LLM/VLM 챗봇 서비스와 유사한 형태의 API를 구축하고,  
완전 자동화된 GitOps 기반 CI/CD 파이프라인을 완성한다.

## Environments
- **OS**: Windows 11 + WSL2
- **GPU**: RTX 5080 16GB
- **Memory**: 32GB
- **Virtual env**: WSL2 + Anaconda3
- **Kubernetes**: v1.30 (containerd + NVIDIA Container Toolkit)

## Current Architecture (2025.03 기준)
Git Push → GitLab Webhook
↓
Jenkins (CI)
↓ (Kubernetes Dynamic Agent + Kaniko)
Docker Image Build & Push → registry.local:5000
↓
Argo CD Image Updater (GitOps)
↓ (자동 manifest 이미지 태그 업데이트)
Argo CD Sync → Kubernetes Deployment
↓
Ingress-NGINX → FastAPI (외부 노출)


## Tools & Stack

| 카테고리          | 도구                          | 상태          | 비고                              |
|-------------------|-------------------------------|---------------|-----------------------------------|
| Source Control    | Git + GitLab                  | ✅ 완료       |                                   |
| CI                | Jenkins + Kubernetes Plugin   | ✅ 완료       | JCasC, Kaniko agent               |
| Image Build       | Kaniko                        | ✅ 완료       | Docker daemon 없이 빌드           |
| Registry          | Docker Private Registry       | ✅ 완료       | registry.local:5000               |
| CD / GitOps       | Argo CD + Image Updater       | ✅ 완료       | 자동 이미지 태그 반영             |
| Orchestration     | Kubernetes v1.30              | ✅ 완료       | WSL2 single node                  |
| Ingress           | ingress-nginx                 | ✅ 완료       | NodePort 31302 / 31084            |
| Backend           | FastAPI                       | ✅ 완료       | GPU 리소스 요청 가능              |
| GPU Support       | NVIDIA Container Toolkit      | ✅ 완료       | containerd config.toml 설정 완료  |
| Storage           | PVC (모델 파일 마운트)        | ✅ 완료       |                                   |
| Monitoring        | Prometheus + Grafana          | 🔜 계획       |                                   |
| Frontend          | Vue 3 + Tailwind              | 🔜 계획       |                                   |
| Vector DB         | Chroma                        | 🔜 계획       |                                   |
| Cache             | Redis (대화 이력)             | 🔜 계획       |                                   |
| Design            | Penpot                        | 🔜 계획       | 아키텍처 다이어그램용             |

## Kubernetes 환경 요약

| 항목                | 값                        |
|---------------------|---------------------------|
| 클러스터            | WSL2 single node          |
| Ingress Controller  | ingress-nginx             |
| Service Type        | NodePort                  |
| 외부 HTTP 포트      | 31302                     |
| 외부 HTTPS 포트     | 31084                     |
| GPU 리소스 요청     | nvidia.com/gpu: 1         |

## CI/CD 파이프라인 현황 (완료)

✅ Git Push → Webhook → Jenkins Job Trigger  
✅ Kaniko Pod 동적 생성 → Dockerfile 빌드  
✅ 이미지 Push → registry.local:5000  
✅ Argo CD Image Updater가 새 태그 감지  
✅ Git manifest 자동 업데이트 & Argo CD Sync  
✅ Pod Rolling Update 완료  
✅ Ingress 통한 외부 FastAPI 접근 성공[](http://localhost:31302)

### 해결했던 주요 이슈

- Jenkins Webhook 200 OK인데 빌드 안 됨 → URL에 포트 명시
- Kaniko OOMKilled → Pod 리소스 limit/memory 조정
- Private Registry 인증 문제 → insecure-registry 설정 + Kaniko credential
- GPU 인식 실패 → containerd config.toml에 nvidia runtime 추가
- Harbor → Docker Registry로 전환 (간단한 로컬 테스트 용이)

## FastAPI Backend 검증 현황

| 항목                     | 상태    | 비고                          |
|--------------------------|---------|-------------------------------|
| Pod 생성 및 Running      | ✅      |                               |
| GPU 리소스 할당 확인     | ✅      | nvidia-smi pod 내부 실행 OK   |
| 모델 파일 PVC 마운트     | ✅      |                               |
| /health, /chat 엔드포인트 | ✅      | 응답 정상                     |
| Ingress 외부 접근        | ✅      | http://localhost:31302/docs   |

## 현재 완료도 요약 (2025.03 기준)

- Kubernetes 클러스터 운영 : 100%
- GPU 워크로드 실행 가능 : 100%
- CI 파이프라인 (빌드 & 푸시) : 100%
- CD 파이프라인 (GitOps + Image Updater) : 100%
- FastAPI 기본 배포 & API 호출 : 100%
- 전체 자동화 흐름 (Push → Deploy) : 100%

## 🔜 다음 단계 (향후 계획)

1. HTTPS + 자체 서명 인증서 또는 로컬 CA 도입
2. Prometheus + Grafana + Loki 모니터링 스택 구축
3. 모델 버전 관리 (MLflow / DVC / HuggingFace Model Registry 연동 검토)
4. 대화 이력 저장 → Redis 또는 PostgreSQL
5. 벡터 DB (Chroma / Weaviate / Qdrant) 도입 → RAG 구현
6. Vue3 + Tailwind 프론트엔드 개발 및 배포
7. Blue-Green / Canary 배포 전략 도입
8. Agentic Workflow / Multi-Agent 시스템 확장 검토
9. Auto-scaling (HPA + GPU-aware scheduling)

---

**한 줄 요약**  
현재 On-premise WSL2 Kubernetes 위에서  
**완전 자동 GitOps CI/CD 파이프라인**이 동작하며  
GPU를 사용하는 FastAPI 챗봇 API가 외부에서 정상 호출 가능한 상태까지 구축 완료되었습니다.
