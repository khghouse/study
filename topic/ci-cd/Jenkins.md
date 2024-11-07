
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
