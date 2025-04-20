### 실습 목표

- AWS EC2 서버에 Docker 기반 Jenkins를 설치
- Spring Boot 프로젝트를 CI/CD 파이프라인으로 빌드 및 배포할 수 있는 환경 구축

<br />

### 1. EC2 인스턴스 기본 세팅

#### Docker & Docker Compose 설치

```shell
# Docker 설치
sudo yum install docker -y
sudo service docker start
sudo systemctl enable docker
sudo usermod -aG docker ec2-user

# Docker Compose 설치
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 설치 확인
docker --version
docker-compose --version
```
