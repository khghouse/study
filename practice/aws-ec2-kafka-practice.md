### 실습 목표

AWS EC2 환경에서 Docker Compose로 Kafka 클러스터를 구성하고, 토픽 생성과 메시지 송수신 실습을 통해 Kafka 기본 통신 흐름을 익혔습니다.

<br />

### AWS EC2 인스턴스 설정

```shell
# Docker 설치
sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
docker --version

# Docker Compose 설치
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

# 현재 사용자(ec2-user)를 docker 그룹에 추가
sudo usermod -aG docker ec2-user

# 변경된 그룹 권한을 현재 셀에 반영
newgrp docker
```

<br />

### Docker Compose로 Kafka 클러스터 구성

```yaml
version: '3.8'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    container_name: zookeeper
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SYNC_LIMIT: 10
      ZOOKEEPER_INIT_LIMIT: 10
      ZOOKEEPER_4LW_COMMANDS_WHITELIST: "*"
    networks:
      - kafka-network
    volumes:
      - zookeeper-data:/var/lib/zookeeper/data
      - zookeeper-log:/var/lib/zookeeper/log

  kafka:
    image: confluentinc/cp-kafka:7.4.0
    container_name: kafka
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: 'true'
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_ZOOKEEPER_CONNECTION_TIMEOUT_MS: 60000
      KAFKA_ZOOKEEPER_SESSION_TIMEOUT_MS: 60000
      KAFKA_HEAP_OPTS: "-Xmx512M -Xms512M"
    networks:
      - kafka-network
    volumes:
      - kafka-data:/var/lib/kafka/data
    restart: on-failure

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    container_name: kafka-ui
    depends_on:
      - kafka
    ports:
      - "8080:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:29092
      KAFKA_CLUSTERS_0_ZOOKEEPER: zookeeper:2181
    networks:
      - kafka-network

networks:
  kafka-network:
    driver: bridge

volumes:
  zookeeper-data:
  zookeeper-log:
  kafka-data:
```

<br />

### Kafka 클러스터 실행 및 확인

```shell
# Docker Compose 실행
docker-compose up -d

# 컨테이너 상태 확인
docker-compose ps

# 로그 확인
docker-compose logs -f
```

<br />

### 토픽 생성 및 확인

```shell
# 토픽 생성
docker exec -it kafka kafka-topics --create --topic test --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1

# 생성된 토픽 목록 확인
docker exec -it kafka kafka-topics --list --bootstrap-server localhost:9092

# 토픽 상세 정보 확인
docker exec -it kafka kafka-topics --describe --topic test --bootstrap-server localhost:9092
```

<br />

### Producer/Consumer 테스트

```shell
# 터미널 1: Consumer 실행 (먼저 실행)
docker exec -it kafka kafka-console-consumer \
  --topic test \
  --bootstrap-server localhost:9092 \
  --from-beginning
  
# 터미널 2: Producer 실행
docker exec -it kafka kafka-console-producer \
  --topic test \
  --bootstrap-server localhost:9092
```

```shell
# 터미널 2 : Producer
docker exec -it kafka kafka-console-producer \
>   --topic test \
>   --bootstrap-server localhost:9092

# 메시지 입력
>Hello Kafka!
>안녕 카프카!

# 터미널 1 : Consumer
docker exec -it kafka kafka-console-consumer \
>   --topic test \
>   --bootstrap-server localhost:9092 \
>   --from-beginning

# 메시지 수신
Hello Kafka!
안녕 카프카!
```

<br />

#### 참고 자료

- Claude.ai 대화 내용
- ChatGPT 대화 내용