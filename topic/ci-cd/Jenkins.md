
## Jenkins 서버용 인스턴스 구축

패키지 최신 버전 업데이트
```shell
sudo yum update -y
```

자바 17 설치
```shell
sudo yum install -y java-17-amazon-corretto
```

Jenkins 저장소 추가
```shell
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

Jenkins 설치
```shell
sudo yum install -y jenkins
```

[선택] 설치 중 GPG check FAILED 에러 발생 시
- gpgcheck=1 -> gpgcheck=0
```shell
sudo vi /etc/yum.repos.d/jenkins.repo
```
```text
[jenkins]
name=Jenkins-stable
baseurl=https://pkg.jenkins.io/redhat-stable/
gpgcheck=0
```

Jenkins 시작 및 부팅 시 자동 실행 설정
```shell
sudo systemctl enable jenkins
sudo systemctl start jenkins
# sudo systemctl status jenkins
```

Jenkins 초기 비밀번호 확인
```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

<br />

## Jenkins 웹 인터페이스 설정
Jenkins 웹 인터페이스 접속
- http://ec2-ip:8080
  - 초기 비밀번호 입력 -> Suggested plugins
  - 관리자 계정 생성

필수 플러그인 설치
- Jenkins 관리 -> Plugins -> Available plugins -> SSH Agent

SSH 키 설정
- Jekkins 서버에서 EC2 인스턴스에 접근할 수 있도록 SSH 키 설정
- Jenkins 관리 -> Credentials -> (global) -> Add Credentials
  - Kind : SSH Username with private key
  - ID : 이 자격 증명을 식별하기 위한 이름 (예: ec2-ssh-key)
  - Username : ec2-user (Amazon Linux의 기본 사용자)
  - Private Key -> Enter directly -> 키 파일(pem) 값 입력
