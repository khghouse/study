### 실습 목표

<br />

### 1. EC2 인스턴스 기본 세팅

#### 자바 설치

```shell
sudo yum update -y
sudo yum install java-17-amazon-corretto -y
java -version
```

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
