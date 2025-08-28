### 실습 목표

AWS RDS MySQL 환경에서 운영 중인 데이터베이스를 EC2 기반 Docker 컨테이너 환경으로 마이그레이션하여 비용 절감 및 개발 환경 일관성 확보를 목표로 합니다.

<br />

### 1. 로컬 환경 MySQL 클라이언트 설치 (macOS)

```shell
# Homebrew 설치 (미설치 시)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# MySQL 클라이언트 설치
brew install mysql-client

# PATH 설정 (설치 후 안내되는 명령어 실행)
# echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc

# 변경된 .zshrc 파일을 현재 터미널 세션에 즉시 적용
source ~/.zshrc

# 설치 확인
mysql --version
mysqldump --version
```

<br />

### 2. 네트워크 환경 접근

#### 2-1. Port Forwarding 설정 (Termius 사용 시)

- Local Port: 8888 (임의 선택)
- Remote Host: [RDS 엔드포인트]
- Remote Port: 3306

#### 2-2. 연결 테스트

```shell
# 포트 포워딩 확인
nc -zv 127.0.0.1 8888

# MySQL 연결 테스트
mysql -h 127.0.0.1 -P 8888 -u [마스터 계정] -p
```

<br />

### 3. 데이터 백업

#### 3-1. 백업 옵션

- 데이터 범위 옵션
    - routines : 저장 프로시저와 함수 포함
    - triggers : 트리거 포함
    - events : 예약된 이벤트 포함
- RDS 호환성 옵션
    - single-transaction : 일관성 있는 백업 (InnoDB 필수)
    - no-tablespaces : RDS의 tablespace 제한 우회
    - set-gtid-purged=OFF : 복제 설정 정보 제외

#### 3-2. 데이터 백업 실행

```shell
# 데이터 백업
mysqldump -h 127.0.0.1 -P 8888 -u [마스터 계정] -p \
--single-transaction \
--routines \
--triggers \
--events \
--no-tablespaces \
--set-gtid-purged=OFF \
[데이터베이스 이름] > backup.sql
```

#### 3-3. 사용자 정보 백업 (선택사항)

사용자 백업이 가능하지만 수동으로 사용자를 재생성했습니다.

- RDS라는 특수한 환경에 맞춰진 사용자 정보를 일반 MySQL 환경 그대로 이식하는 과정에서 호환성 문제가 연쇄적으로 발생함

```shell
# 사용자 정보 백업
mysqldump -h 127.0.0.1 -P 8888 -u [마스터 계정] -p \
--system=users >> user.sql

# 위 명령어, 즉 --system=users 옵션을 인식 못할 경우
mysqldump -h 127.0.0.1 -P 8888 -u [마스터 계정] -p \
--single-transaction \
--set-gtid-purged=OFF \
mysql > user.sql
```

<br />

### 4. EC2 인스턴스 생성 및 Docker 설치

#### 4-1. EC2 인스턴스 권장 스펙

- 인스턴스 타입: t3.small 이상 (t3.micro는 메모리 부족 가능)
- 스토리지: 30GB 이상 (gp3 권장)
- 보안 그룹: SSH(22), MySQL(13306) 포트 열기

#### 4-2. Docker 환경 구성

```shell
# Docker 설치
sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
docker --version

# 현재 사용자(ec2-user)를 docker 그룹에 추가
sudo usermod -aG docker ec2-user

# Docker Compose 설치
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 변경된 그룹 권한을 현재 셀에 반영
newgrp docker
```

<br />

### 5. Docker Compose 설정

#### 5-1. docker-compose.yml 작성

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.4.6
    container_name: mysql
    restart: always
    ports:
      - "13306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: your_password # 예시용 비밀번호
    volumes:
      - mysql_data:/var/lib/mysql # Docker가 관리하는 mysql_data라는 외부 저장 공간을 mysql 컨테이너의 /var/lib/mysql 경로에 연결
    # 이 컨테이너는 로그를 10MB씩 3개까지 보관
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"


volumes:
  mysql_data: # Docker가 관리하는 named volume
```

#### 5-2. 컨테이너 실행

```shell
# 컨테이너 실행
docker-compose up -d

# 실행 상태 확인
docker-compose ps
docker-compose logs mysql
```

<br />

### 6. 백업 파일 전송

#### 6-1. 파일 전송

SFTP 프로그램을 이용하여 파일 전송

#### 6-2. 디렉터리 구조

```
# 디렉터리 구조

/home/ec2-user/
├── backup.sql
└── docker-compose.yml
```

<br />

### 7. 데이터베이스 복원

#### 7-1. 데이터베이스 생성

마스터 계정 접속

```shell
docker exec -it [컨테이너 이름] mysql -u root -p
```

데이터베이스 생성

```mysql
CREATE DATABASE [데이터베이스 이름];
SHOW DATABASES;
```

#### 7-2. 데이터 복원

데이터 복원

```shell
cat [백업 파일명].sql | docker exec -i [컨테이너 이름] mysql -u root -p [데이터베이스 이름]
```

<br />

### 8. 사용자 계정 생성 및 권한 설정

마스터 계정 접속

```shell
docker exec -it [컨테이너 이름] mysql -u root -p
```

계정 생성 및 권한 부여

```mysql
CREATE USER '[사용자 계정]'@'%' IDENTIFIED BY '[비밀번호]';
GRANT ALL PRIVILEGES ON [데이터베이스 이름].* TO '[사용자 계정]'@'%';
FLUSH PRIVILEGES;
```

<br />

### 9. 애플리케이션 설정 변경

#### 9-1. Spring Boot 설정

기존(RDS) :

```yaml
spring:
  datasource:
    url: jdbc:mysql://[RDS엔드포인트]:3306/[DB명]
    username: [ 사용자명 ]
    password: [ 패스워드 ]
```

변경(Docker MySQL) :

```yaml
spring:
  datasource:
    url: jdbc:mysql://[EC2-Private-IP]:13306/[DB명]
    username: [ 새로운 사용자명 ]
    password: [ 새로운 패스워드 ]
```

#### 9-2. 보안 그룹 설정

DB EC2 보안 그룹

- 인바운드: 13306 포트를 애플리케이션 EC2에서만 허용

<br />

### 10. DBeaver에서 DB서버 연결 (선택사항)

#### 10.1 기본 연결 정보

- Server Host: [DB EC2 Private IP]
- Port: 13306
- Database: [데이터베이스명]
- Username: [사용자명]
- Password: [패스워드]

#### 10.2 SSH 터널 설정

SSH 탭에서:

- Host/IP: [Bastion Host Public IP]
- Port: 22
- Authentication Method: Public Key
- Private Key: .pem 키 파일 경로


