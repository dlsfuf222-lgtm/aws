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

[kafka 다운로드](https://downloads.apache.org/kafka/)
<img width="655" height="441" alt="kafka 다운로드" src="https://github.com/user-attachments/assets/4e208360-c58a-4880-b255-bef41cec7796" />
<img width="677" height="871" alt="다운로드" src="https://github.com/user-attachments/assets/00c216c0-7559-42a6-a1de-537e603dee36" />
wget 커맨들 아파치 카프카를 다운로드 받습니다.
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


