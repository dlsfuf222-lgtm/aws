## apache kafka 를 실행 시키기 위한 순서

1. AWS 에 들어가서 root 계정으로 로그인 한 후 EC2 인바운드 설정을 다음과 같이 해야한다.
<img width="1634" height="411" alt="인바운드 설정" src="https://github.com/user-attachments/assets/d053ee6a-5399-4d32-adc5-72e697d5e852" />


## 그 후 Git Bash를 사용하여 AWS EC2(아파치 브로커 서버를 위한 인스턴스)SSH 접속.   

myHelloWorldEC2Pem.pem 펌파일과 퍼플릭 주소 13.125.224.213 라고 하면   
다음과 같이 접속한다.

```
$ ssh -i ./myHelloWorldEC2Pem.pem ubuntu@13.125.224.213
```
