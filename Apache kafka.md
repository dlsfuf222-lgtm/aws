## apache kafka 를 실행 시키기 위한 순서

1. AWS 에 들어가서 root 계정으로 로그인 한 후 EC2 인바운드 설정을 다음과 같이 해야한다.
<img width="1634" height="411" alt="인바운드 설정" src="https://github.com/user-attachments/assets/d053ee6a-5399-4d32-adc5-72e697d5e852" />


## 그 후 Git Bash를 사용하여 AWS EC2(아파치 브로커 서버를 위한 인스턴스)SSH 접속.   

myHelloWorldEC2Pem.pem 펌파일과 퍼플릭 주소 13.125.224.213 라고 하면   
다음과 같이 접속한다.

```
$ ssh -i ./myHelloWorldEC2Pem.pem ubuntu@13.125.224.213
```

## OpenJDK 설치하기

1. 인스턴스 업데이트 : 우선, EC2 인스턴스의 패키지 목록을 최신 상태로 업데이트합니다.
```
sudo apt update
sudo apt upgrade
```

2. Java 설치 가능한 버전 확인: 사용 가능한 OpenJDK 버전을 검색합니다. 이를 통해 원하는 Java 버전을 선택할 수 있습니다.
```
apt search openjdk
```

3. Java 설치: 원하는 버전의 OpenJDK를 설치합니다. 예를 들어, OpenJDK 17를 설치하고 싶다면, 다음과 같이 입력합니다.
```
sudo apt install -y openjdk-17-jdk
```

**-y** 옵션은 설치 중 나타나는 모든 질문에 자동으로 yes라고 답하는 옵션입니다.

4. Java 버전 확인: Java 설치가 성공적으로 완료되었는지 확인하기 위해 설치된 Java의 버전을 확인합니다.
```
java -version
```

이 커맨드는 설치된 Java의 버전 정보를 출력합니다.

## 환경변수 설정   
Java를 설치한 후, 특정 애플리케이션에서 Java 환경변수를 요구하는 경우가 있습니다. 예를 들어, **JAVA_HOME** 환경변수를 설정해야 하는 경우, **.bashrc** 또는 **.profile** 파일에 다음과 같이 추가할 수 있습니다.

1. 홈 디렉토리의 **./bashrc** 파일을 엽니다.
```
vi ~/.bashrc
```

2. 파일의 맨 아래에 JAVA_HOME 환경변수를 추가합니다. 예를 들어, OpenJDK 17를 설치한 경우 다음과 같이 추가합니다.
```
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
```
<img width="1262" height="303" alt="자바 환경변수" src="https://github.com/user-attachments/assets/61756a2c-2215-4bc3-bb72-f978e11a9b67" />

3. 변경 사항을 저장하고 .bashrc 파일을 닫은 다음, 변경 사항을 적용합니다.
```
source ~/.bashrc
```

이렇게 하면 AWS의 Ubuntu EC2 인스턴스에 Java를 설치하고 설정하는 방법을 완료할 수 있습니다.

<img width="655" height="441" alt="kafka 다운로드" src="https://github.com/user-attachments/assets/4e208360-c58a-4880-b255-bef41cec7796" />
<img width="677" height="871" alt="다운로드" src="https://github.com/user-attachments/assets/00c216c0-7559-42a6-a1de-537e603dee36" />

wget 커맨드로 아파치 카프카를 다운로드 받습니다.
```
wget https://downloads.apache.org/kafka/3.7.2/kafka_2.12-3.7.2.tgz
```

압축 파일을 해제합니다.
```
tar xvf ./kafka_2.12-3.7.2tgz
```

## server.properties 수정

kafka_2.13-3.7.2/config/server.properties 내용중, 아래 빨간색 하이라이트 부분을 수정합니다.

```
vi ./server.properties
```
<img width="1221" height="395" alt="properties 수정" src="https://github.com/user-attachments/assets/569b4b59-ce62-491e-9853-1a9312eea5c2" />

Kafka의 heap memory는 디폴트로 Kafka = 1GByte, Zookeeper=512MByte입니다. 이는 현재 선택한 AWS EC2 프리티어 메모리가 1GByte 이기 때문에 에러가 발생합니다. 그래스 Kafka와 Zookeeper의 힙 메모리를 .bashrc 파일에서 재설정해야 합니다.
```
export KAFKA_HEAP_OPTS="-Xms300m -Xmx300m"
```

<img width="965" height="383" alt="Kafka Zookeeper 메모리 재설정" src="https://github.com/user-attachments/assets/861f32a8-4257-4494-b7a7-294a2a545842" />

※위 이미지 설정 대신에 300Mbyte로 수정

KAFKA_HEAP_OPTS환경 변수를 시스템에 등록합니다.
```
$ source ./.bashrc
```

**Zookeeper 실행**
```
$ ./bin/zookeeper-server-start.sh -daemon /home/ubuntu/kafka_2.12-3.7.2/config/zookeeper.properties
```

**Zookeeper 실행 확인**
```
$ jps -m
```

JPS = Java Virtual Machine Process Status Tool
실행 중인 Java 프로세스(JVM) 들을 확인하기 위한 유틸리티입니다.

<img width="1103" height="76" alt="Zookeeper 실행 확인" src="https://github.com/user-attachments/assets/d68dc4e4-5976-4bab-93bf-fb0320cc6fd9" />

4954 프로세스는 Zookeeper 서버를 실행하는 Java 프로세스입니다. QuorumPeerMain 클래스는 Zookeeper 서버를 시작하는 메인 클래스입니다.

**Kafka 실행**
```
$ ./bin/kafka-server-start.sh -daemon /home/ubuntu/kafka_2.12-3.7.2/config/server.properties
```
**Kafka 실행 확인**
```
$ jps -m
```

**Topic 생성**
```
$ bin/kafaka-topics.sh \
> --create \
> --bootstrap-server 54.180.100.188:9092 \
> --topic tacocloudorders
```

Topic 목록 확인하기
```
$ bin/kafka-topics.sh --bootstrap-server 54.180.100.188:9092 --list
```

Topic 정보 확인
```
$ bin/kafka-topics.sh --bootstrap-server 54.180.100.188:9092 --topic tacocloudorders --describe
```

**메세지 프로듀서를 위한 인스턴스 설정**

메시지 프로듀서를 위한 EC2 인스턴스의 인바운드 규칙은 디폴트 규칙만 사용하면 됩니다.
카프카 브로커 서버를 위한 인스턴스처럼 패키지 업데이트, OpenJDK설치, 환경변수 설정 그리고 아파치 카프카 다운로드 및 압축해제를 합니다.
이 인스턴스는 메시지 프로듀서 역할만 하기 때문에 Zookeeper와 Kafka를 실행시킬 필요가 없습니다.   
단지 다운로드받은 아파치 카프카에서 kafka-console-producer.sh를 실행시켜 타겟 토픽에 메시지를 publish하면 됩니다.
```
kafka-console-consumer.sh --bootstrap-server <broker-address>:<port> --topic <topic-name>
```
* <broker-address>:<port>:Kafka 브로커의 주소와 포트번호입니다. 예를 들어, **54.180.96.36:9092.**
* <topic-nama>:메시지를 확인하고 싶은 토픽의 이름입니다. 예를 들어, **tacocloud_orders.**

```
$ ./kafka_2.12-3.7.2/bin/kafka-console-producer.sh \
> --bootstrap-server 54.180.32.76:9092 \
> --topic tacocloudorders
```
<img width="1280" height="178" alt="메시지 전달" src="https://github.com/user-attachments/assets/253bb4d0-7b59-426e-bb7c-459772816512" />

**메시지 컨슈머를 위한 인스턴스 설정**

메시지 컨슈머를 위한 EC2 인스턴스의 인바운드 규칙은 디폴트 규칙만 사용하면 됩니다.   
카프카 브로커 서버를 위한 인스턴스처럼 패키지 업데이트, OpenJDK 설치, 환경변수 설정 그리고 아파치 카프카 다운로드 및 압축해제를 합니다.   
이 인스턴스는 메시지 컨슈머 역할만 하기 때문에 Zookeeper와 Kafka를 실행시킬 필요가 없습니다.   
단지 다운로드받은 아파치 카프카에서 kafka-console-consumer.sh를 실행시켜 타겟 토픽을 구독하여 메시지를 전송받으면 됩니다.
```
 ./kafka-console-consumer.sh 
 --bootstrap-server <broker-address>:<port> 
 --topic <topic-name> 
 --from-beginning
 --group <group-name>
```
* <broker-address>:<port>: Kafka 브로커의 주소와 포트번호입니다. 예를 들어, **54.180.96.36:9092.**
* <topic-name>: 메시지를 확인하고 싶은 토픽의 이름입니다. 예를 들어, **tacocloud_orders.**
* --from-beginning: 이 옵션을 사용하면 토픽의 시작부터 현재까지의 모든 메시지를 확인 할 수 있습니다. 이 옵션 없이 명령어를 실행하면, 명령어를 실행한 시점 이후에 도착하는 메시지만 확인할 수 있습니다.
* <group-name>: 이 옵션을 사용하면 타겟 토픽을 subscribe할 수 있습니다.
```
$ ./kafka_2.12-3.7.2/bin/kafka-console-consumer.sh \
> --bootstrap-server 54.180.32.76:9092 \
> --topic tacocloudorders \
> --from-beginning \
> --group order-consumer-group
```
<img width="1251" height="341" alt="메시지 받기" src="https://github.com/user-attachments/assets/7bcbf682-e80b-48de-8191-3a85c94f900d" />

**Kafka 브로커 서버의 토픽에 저장된 메시지 확인**

Kafka 서버의 토픽에 저장된 메시지를 확인하는 가장 일반적인 방법은 Kafka의 커맨드 라인 툴인 **kafka-console-consumer**를 사용하는 것입니다. 이 툴을 사용하면 실시간으로 메시지를 소비하거나, 특정 시간에서부터의 메시지를 조회하는 등 다양한 방식으로 메시지를 확인할 수 있습니다.

**kafka-console-sonsumer 사용법**
1. 실시간으로 메시지 소비하기: Kafka 설치 디렉토리의 **bin** 폴더 안에서 다음 커맨드를 실행하여, 특정 토픽의 메시지를 실시간으로 확인할 수 있습니다. 이 커맨드는 새로운 메시지가 토픽에 도착할 때마다 해당 메시지를 출력합니다.
```
./kafka-console-consumer.sh --bootstrap-server <broker-address>:<port> --topic <topic-name> --from-beginning
```
```
./kafka-console-consumer.sh --bootstrap-server 54.180.96.36:9092 --topic tacocloudorders --from-beginning
```
* <broker-address>:<port>: Kafka 브로커의 주소와 포트번호입니다. 예를 들어, **54.180.96.36:9092.**
* <topic-name>: 메시지를 확인하고 싶은 토픽의 이름입니다. 예를 들어, **tacocloud_orders.**
* --from-beginning: 이 옵션을 사용하면 토픽의 시작부터 현재까지의 모든 메시지를 확인 할 수 있습니다. 이 옵션 없이 명령어를 실행하면, 명령어를 실행한 시점 이후에 도착하는 메시지만 확인할 수 있습니다.

2. 특정 시간부터의 메시지 소비하기: Kafka 2.1.0 이상에서는 --offset 대신 --from-beginning 또는 --partition과 함께 --offset 옵션을 사용하여 특정 시점부터 메시지를 소비할 수 있습니다. 또한 kafka-console-consumer는 --timestamo 옵션을 지원하지 않으므로, 특정 시간으로부터 메시지를 조회하고자 할 때는 다른 도구나 API를 사용해야 합니다.

## 토픽 삭제

Kafka 토픽을 삭제하는 커맨드는 Kafak가 설치된 디렉토리 내의 bin 폴더에서 실행할 수 있는 kafka-topics.sh 스크립트를 사용하여 수행할 수 있습니다. 기본적인 커맨드 형식은 다음과 같습니다.
```
./kafka-topics.sh --delete --topic <토픽 이름> --bootstrap-server <브로커 목록>
```

여기서 <토픽 이름>에는 삭제하고자 하는 Kafka 토픽의 이름을, <브로커 목록>에는 Kafka 클러스터의 브로커들의 주소 목록을 콤마로 구분하여 입력합니다. 예를 들어, 브로커가 localhost:9092에 실행 중이고, 삭제하고자 하는 토픽 이름이 my-topic이라면, 다음과 같이 명령을 실행할 수 있습니다.
```
./kafka-topics.sh --delete --topic my-topic --bootstrap-server localhost:9092
```

이 커맨드를 실행하기 전에 Kafka 설정에서 토픽 삭제가 활성화되어 있는지 확인해야 합니다.   
Kafka의 server.properties파일에서 **delete.topic.enable=ture** 설정을 확인하거나 추가해야 할 수 있습니다. 이 옵션이 활성화되어 있지 않으면, 토픽을 삭제해도 실제로는 삭제되지 않고, 삭제 마킹만 되어 있게 됩니다.   

또한, 토픽 삭제 후에는 해당 토픽에 관련된 데이터가 Kafka 클러스터의 브로커에서 실제로 삭제될 때까지의 약간의 시간이 소요될 수 있습니다. 삭제 프로세스는 비동기적으로 수행됩니다.

**Kafka 종료**

**./bin/kafka-server-stop.sh** 을 실행시키면 종료됩니다.

**주의사항**
* kafka-console-consumer.sh스크립트는 Kafka의 설치 경로 내 **bin** 디렉토리에 위치합니다. 따라서 해당 스크립트를 실행하기 전에 Kafka 설치 디렉토리로 이동하거나, 전체 경로를 명시해야 합니다.
* 실제 운영 환경에서는 토픽에 저장된 대량의 메시지를 모두 소비하는 것이 시간이 많이 걸릴 수 있으므로, 필요한 정보를 효율적으로 검색하기 위해 적절한 옵션을 선택하여 사용하는 것이 중요합니다.
