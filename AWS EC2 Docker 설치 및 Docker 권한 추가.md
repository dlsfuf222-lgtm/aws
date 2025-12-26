# AWS EC2 Docker 설치

1. 패키지 업데이트
```
$ sudo apt update
$ sudo apt upgrade
```
2. https관련 패키지 설치
```
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
명령어 구성
* **apt-transport-https**: APt가 HTTPS 프로토콜을 통해 패키지를 다운로드할 수 있도록 지원하는 모듈. HTTPS는 패키지 다운로드 시 데이터 암호화를 제공하므로 더 안전한 방법.
* **ca-certificates**: HTTPS 연결을 위해 필요한 인증서를 제공하는 패키지. 시스템에서 신뢰 할 수 있는 인증서를 관리하여 안전한 네트워크 통신을 기원.
* **curl**: 커맨드 라인 기반 파일 전송 도구로, HTTP, HTTPS, FTP 등의 프로토콜을 지원.
* **software-properties-common**: PPA(Personal Package Archive)를 쉽게 관리할 수 잇는 유틸맅. 외부 소프트웨어 저장소를 추가하거나 관리할 때 필요한 명령(add-apt-repository)을 제공.

**3. docker repository 접은을 위한 gpg 키 설정**   
Docker의 GPG 키란?   
Docker의 GPG 키는 Docker 저장소에서 제공하는 소프트웨어 패키지의 출저와 무결성을 보장하기 위해 사용되는 디지털 서명 키입니다. GPG 키는 GNU Privacy   
Guard(GPG)를 기반으로 한 암호화 기술로, 패키지 관리자가 패키지의 신뢰성을 확인하는 데 사용합니다.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

명령어 구성
* **-f**: 실패 시 오류 메시지를 반환.
* **-s**: 작업을 조용히 수행하며, 진행 상태나 오류를 출력하지 않음.
* **-S**: 오류, 발생 시 세부 메시지를 출력(보통 -s와 함께 사용).
* **-L**: 요청한 URL이 리다이렉션될 경우, 최종 URL로 자동 이동.
* https:download.docker.com/linux/ubuntu.gpg: Docker의 GPG 키를 제공하는 URL
* **|(pipe)**: curl 명령의 출력을 다음 명령으로 전달.
* **sudo apt-key add**: apt-key 커맨드는 APT 패키지 관리자가 사용하는 키를 관리.
* **-**: 표준 입력(STDIN)을 의미. curl에서 전달된 데이터를 키로 추가

**4. docker repository 등록**
```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
**5. 다시 업데이트**
```
$ sudo apt update
```
**6. 도커 설치**
```
$ sudo apt install docker-ce
```
**7. 설치 확인**
```
$ docker --version
```

# Docker 권한 추가
**1. user id 확인**

다음 커맨드에서 **ubuntu(@앞에 위치)** 는 현재 AWS EC2 인스턴스에 접속하려는 user id 임.
```
$ ssh -i ./development/Tools/aws_ec2_keys/kafkaserverkey.pem ubuntu@3.38.108.0
```
**2. 1의 커맨드로 해당 AWS EC2 인스턴스에 접속한 다음,group을 확인**
```
$ sudo groups $USER
```
## 명령어 구성
1. **sudo** : * 관리자 권한으로 명령어를 실행합니다. groups 명령을 실행하는 데 관리자 권한이 필요한 것은 아니지만, 습관적으로 sudo를 사용한 것일 수도 있습니다.
2. **groups** : * 현재 사용자가 속한 **모든 그룹** 을 확인하는 명령어 입니다.
3. **$USER** : * 현재 로그인한 사용자의 사용자 이름을 참조하는 환경 변수입니다. 여기서는 로그인한 사용자 계정이 ubuntu이므로 $USER는 ubuntu를 의미합니다.

결과적으로, sudo groups $USER는 **현재 로그인한 사용자가 속한 그룹을 관리자 권한으로 확인** 하는 명령어입니다.
# 출력 설명
```
ubuntu : ubuntu adm cdrom sudo dip lxd
```

이 결과는 ubuntu 사용자 계정이 다음 그룹들에 속해 있음을 보여줍니다.   

**1. ubuntu (디폴트 그룹)**
* 사용자의 디폴트 그룹으로, 파일 생성 시 디폴트로 소속되는 그룹입니다.
* ubuntu 사용자의 홈 디렉토리 /home/ubuntu와 같은 파일의 소유 그룹도 보통 이 그룹입니다.

**2.adm (관리 그룹)**
* **로그 파일을 읽을 수 있는 권한** 을 가진 그룹입니다.
* 예: /var/log/syslog 또는 /var/log/auth.log와 같은 시스템 로그 파일은 기본적으로 adm 그룹만 읽을 수 있습니다.

**3. cdrom (CD-ROM 액세스 그룹)**
* **CD-ROM 드라이브를 사용할 수 있는 권한** 을 제공합니다.
* 현재는 거의 사용되지 않는 그룹이지만, 시스템에서 미디어를 마운트하는 데 필요할 수 있습니다.

**4. sudo (관리자 권한 그룹)**
* **sudo 명령** 을 사용하여 루트 권한을 얻을 수 있는 그룹입니다.
* 이 그룹에 속해 있으면 sudo를 사용해 시스템 관리자 작업을 수행할 수 있습니다.

**5. dip (네트워크 설정 그룹)**
* **Dial-up IP 연결 권한** 을 가진 그룹입니다.
* 예전에는 모뎀 네트워크 연결을 설정할 때 사용되었지만, 오늘날에는 드물게 사용됩니다.

**6. lxd (LXD 컨테이너 관리 그룹)**
* **LXD 컨테이너 관리 권한** 을 가진 그룹입니다.
* 이 그룹에 속하면 LXD(Linux Containers Daemon)를 사용해 컨테이너를 생성하고 관리할 수 있습니다.

## 명령 실행 후 출력 해석
* 결과적으로 이 명령은 **현재 사용자가 속한 모든 그룹** 을 출력하며, 각 그룹은 특정 권한이나 역할과 관련되어 있습니다.
* ubuntu 사용자는 시스템 관리 작업(sudo), 로그 파일 접근(adm), 그리고 컨테이너 관리(lxd)와 같은 여러 중요한 작업을 수행할 수 있는 권한을 가지고 있음을 알 수 있습니다.

## 추가 설명
* 이 명령은 특정 사용자가 시스템에서 어떤 작업을 수행할 수 있는지를 파악하기에 유용합니다.
* 그룹 관리를 통해 시스템 보안을 강화하거나 특정 작업을 제한할 수도 있습니다.

**3. docker 그룹 생성**
```
$ sudo groupadd docker
```
다음과 같이 docker 그룹 생성 확인
```
$ cat /etc/group
```

**4. docker 그룹에 user 추가**
```
$ sudo usermod -a -G docker $USER
```
다음 커맨드로 위 커맨드의 실행 성공 여부를 확인
```
$ getent group docker
```

**5. AWS EC2 인스턴스 재부팅 혹은 ssh 접속 종료 후 재접속**
다음 커맨드는 인스턴스 재부팅
```
$ sudo reboot
```
다음 커맨드는 ssh 접속 종료
```
$ exit
```



