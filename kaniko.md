# Kaniko
Docker 없이 이미지를 빌드할 수 있는, google에서 만든 컨테이너 이미지 빌드 도구로 docker 데몬 없이 dockerfile로 이미지를 빌드할 수 있음.  
일반적으로 dockerd를 사용하여 이미지를 빌드하지만 다음과 같은 상황에서는 dockerd를 사용할 수 없음(굳이굳이)  
- Kubernetes Pod 안 (보안상 root 권한이 없음)
- CI/CD 환경 (GitLab CI, Jenkins, GitHub Actions 등)  <-- 현재 상황
- 서버에 Docker가 설치되어 있지 않음
- containerd만 있는 환경 (요즘 Kubernetes 표준) <-- 현재 상황

  rootless 상황에서 container registry에 push 가능하게 함

## Dockerfile 파싱
1. Dockerfile을 읽어서, FROM, RUN, COPY, ADD 등의 명령어를 분석.

2. Layer별 빌드 수행
Docker처럼 각 명령어를 “레이어” 단위로 실행하면서 rootfs를 쌓아감.
하지만 dockerd 대신 Kaniko가 직접 filesystem을 조작함.

3. 이미지 생성 및 push. 모든 layer를 합쳐서 .tar 이미지로 만들고, 이를 registry(예: gcr.io/my-project/myimage:tag)에 push.

## 실행 방식
Kubernetes에서는 보통 컨테이너로 실행해서 Job이나 pod로 수행  

## 인증 (registry 접근)
Kaniko는 이미지를 push 하기 위해 registry에 접근 가능한 인증 정보를 필요로 함.  
docker 처럼 $HOME/.docker/config.json을 참고
