b### 0. 이전 작업

- [jenkins-docker-cicd-setup](jenkins-docker-cicd-setup.md)

<br />

### 1. GitHub Webhook 등록

- Settings > Webhooks > Add webhook
    - Content type: application/json
    - Payload URL: http://<EC2_IP>:<PORT>/github-webhook/
    - Just the push event 체크

<br />

### 2. Jenkins에 GitHub Plugin 설치

- Dashboard > Jenkins 관리 > Plugins > Available plugins
    - GitHub
    - GitHub Integration Plugin [추가 설치]
    - GitHub Branch Source
    - GitHub plugin
    - Pipeline: GitHub Groovy Libraries

<br />

### 3. Jenkins Job 설정

- Dashboard > {job-name} > Configuration > Triggers
    - GitHub hook trigger for GITScm polling 체크

<br />

### 4. AWS 보안 그룹 추가

EC2 인바운드 규칙 추가

- 커스텀 TCP TCP 8080 0.0.0.0/0

<br />

### 5. 테스트

- GitHub에서 코드 푸시
- Jenkins Job 자동 실행 확인

<br />

### 관련 코드

- https://github.com/khghouse/cicd

<br />

#### 참고 자료

- ChatGPT 대화 내용