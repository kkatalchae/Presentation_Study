# rabbitmq
![logo](./images/logo.png)
### 목차

1. amqp 프로토콜과 rabbitmq
2. 왜 사용하는가?
3. 설치 및 구동
4. 다른 메시지 큐 시스템 : 아파치 카프카, activemq 무엇이 다른가?


<br><br><br>
### 1. amqp 프로토콜과 rabbitmq

RabitMq 는 amqp프로토콜로 구현한 오픈소스 메시지 브로커로, 메시지를 많은 사용자에게 전달하거나, <br>
요청에 대한 처리 시간이 길때, 해당 요청을 다른 api 에게 위임하고 빠른 응답을 할 때 많이 사용한다.

​
### amqp 란?
<br>
advanced message queue protocol 의 줄임말로 메시지 지향 미들웨어를 위한 개방형 표준 응용 계층 프로토콜이다. publisher가 발행한 메시지는 message broker 를 통해 consumer 에게로 전송된다.<br> message broker 는 publisher 에게서 받은 메시지를 exchange 를 통해 어디로 전송할 지 결정하고 queue 에 적재한 메시지를 consumer 로 전송한다.<br> 

![protocol](./images/protocol.png)<br><br>
`producing : 메시지를 보내는 시스템`<br>
`queue : rabbitmq의 우편함. 메시지 버퍼이다. 메시지가 rabbitmq와 application 에서 전송될때 queue 안에서만 저장될 수 있다.`<br>
`comsuming : 메시지를 받는 시스템`<br>
`Exchange : Publisher(Producer)로부터 수신한 메시지를 큐에 분배하는 라우터의 역할`<br>
`Binding : exchange 에게 메시지를 라우팅 할 규칙을 지정하는 행위`<br>

​

### Exchange 의 네가지 타입
1) Direct
2) Topic
3) Headers
4) Fanout
<br><br><br>
### 2. 왜 사용하는가?

​

rabbitmq 는 서비스간 연결 분리가 필요할 경우 용이하게 사용된다(Decoupling). <br>예를 들어, 푸시 알람과 이메일, 두가지 방식으로 사용자에게 알람을 보내야 할 경우 각각의 서비스를 담당하는 Queue 에 메시지를 publishing 한다.<br> 메시지 브로커를 사용할 경우 전체 서비스의 간섭 없이 알람 서비스 유지가 가능하다.<br>
![application](./images/application.png)<br>
<br><br><br>
### 3.설치 및 구동 

​

공식문서의 설치 방법을 따른다.<br>

https://www.rabbitmq.com/docs/download<br>

윈도우의 경우 chocolatey 를 이용해 설치하는게 신상에 이롭다(...) rabbitmq 구동에 필요한 Erlang 버전과 호환이 잘 맞아야 하기 때문이다.<br><br>
```js

## 1. chocolatey 설치
   chocolatey 란? 윈도우에서 사용할 수 있는 커맨드 라인 패키지 매니저
## 2. Window Powershell 관리자 권한으로 실행후 하기 명령어 입력
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
## 3. 설치 확인
choco
## 4. rabbitmq 다운로드
choco install rabbitmq

```
<br><br>

![myfolder](./images/myfolder.png) <br><br>
![powershell](./images/powershell.png) <br><br>
![gui](./images/gui.png) <br> <br>
<br>
```js

## 사용자 리스트 보기
.\rabbitmqctl.bat list_users
## 사용자 추가 (아이디/비밀번호)
.\rabbitmqctl.bat add_user myid mypasswordd
## 관리자 계정 지정
.\rabbitmqctl.bat set_user_tags adteck administrator


```

<br><br><br>
### 4. 다른 메시지 큐 시스템 : 아파치 카프카, activemq 무엇이 다른가?

activemq 는 java 에서 채택한 메시지 지향 미들웨어이다. 

카프카는 이벤트 브로커이다. 기본 메시징 시스템(rabbitMQ, ActiveMQ)에서는 브로커(Broker)가 컨슈머(consumer)에게 메시지를 push해 주는 방식인데, 카프카는 컨슈머(Consumer)가 브로커(Broker)로부터 메시지를 직접 가져가는 PULL 방식으로 동작하기 때문에 최적의 성능을 낼 수 있어 대용량 처리에 특화되어있다.
