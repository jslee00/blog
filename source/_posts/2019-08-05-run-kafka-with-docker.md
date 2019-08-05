---
title: Docker를 이용하여 Zookeeper와 Kafka 실행하기
date: 2019-08-05 20:31:52
updated:
categories:
  - 시스템 구축
tags:
  - Docker
  - Zookeeper
  - Kafka
---

## 개요

{% asset_img "kafka-logo.png" 200 %}

Docker를 이용하여 Kafka와 Zookeeper 서비스를 구동하는 방법을 정리한다.

<!-- more -->

### 버전

2019년 07월 29일 시점에서 최신 버전인 Kafka 2.12-2.3.0, Zookeeper 3.5.5 버전을 기준으로 설명한다.

## Docker Network 설정

다음 명령어를 이용하여 Kafka와 Zookeeper가 통신하기 위해 사용할 Docker Network를 생성한다.

```bash
sudo docker network create kafka
```

## Zookeeper 서비스 구동

[https://hub.docker.com/\_/zookeeper](https://hub.docker.com/_/zookeeper)

Zookeeper는 공식 Docker 이미지를 제공하고 있다.

다음 명령어를 통해 Docker를 이용하여 Zookeeper 서비스를 실행한다.

```bash
docker run -d --name=zookeeper --net=kafka -p 2181:2181 zookeeper
```

## Kafka 서비스 구동

[https://hub.docker.com/r/wurstmeister/kafka](https://hub.docker.com/r/wurstmeister/kafka)

Kafka는 wurstmeister라는 사람이 만든 이미지를 이용하여 실행시킬 수 있다.

다음 명령어를 통해 Docker를 이용하여 Kafka 서비스를 실행한다.

```bash
docker run -d --name kafka --net=kafka -p 9092:9092 -e KAFKA_ADVERTISED_LISTENERS="PLAINTEXT://localhost:9092" -e KAFKA_LISTENERS="PLAINTEXT://0.0.0.0:9092" -e KAFKA_ADVERTISED_HOST_NAME="127.0.0.1" -e KAFKA_ADVERTISED_PORT="9092" -e KAFKA_ZOOKEEPER_CONNECT="zookeeper:2181" -e KAFKA_CREATE_TOPICS="javainuse-topic:1:1" wurstmeister/kafka
```

## Kafka 서비스 테스트

## 확인 사항

### Java 설치 확인

Java가 설치되어 있지 않으면 [CentOS에 JDK 설치](https://www.notion.so/93c3d7fb-8c5b-472f-af4f-8a7c67d51de1) 문서를 참고하여 Java를 설치한다.

### 바이너리 다운로드

[https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)

위 사이트에서 Kafka Docker 이미지의 버전과 동일한 버전의 바이너리 파일 주소를 복사한다.

본 문서를 작성하는 시점에서는 다음과 같았다.

[http://apache.mirror.cdnetworks.com/kafka/2.3.0/kafka_2.12-2.3.0.tgz](http://apache.mirror.cdnetworks.com/kafka/2.3.0/kafka_2.12-2.3.0.tgz)

다음 명령어를 통해 Kafka 바이너리 파일을 다운로드한다.

```bash
wget http://apache.mirror.cdnetworks.com/kafka/2.3.0/kafka_2.12-2.3.0.tgz
```

다음 명령어를 통해 압축을 해제한다.

```bash
tar -xzvf kafka_2.12-2.3.0.tgz
```

다음 명령어를 통해 안으로 들어간다.

```bash
cd kafka_2.12-2.3.0
```

### Topic 생성 테스트

다음 명령어를 통해 Kafka Topic을 생성할 수 있다.

```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic test --partitions 1 --replication-factor 1
```

### Topic 확인 테스트

다음 명령어를 통해 Kafka Topic을 확인할 수 있다.

```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe
```

### Consumer 테스트

다음 명령어를 통해 Kafka Console Consumer를 테스트할 수 있다.

```bash
bin/kafka-console-consumer.sh --from-beginning --bootstrap-server localhost:9092 --topic test
```

### Producer 테스트

다음 명령어를 통해 Kafka Console Consumer를 테스트할 수 있다.

```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

### Topic 삭제 테스트

다음 명령어를 통해 Kafka Topic을 삭제할 수 있다.

```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic test
```
