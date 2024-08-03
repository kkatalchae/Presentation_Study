# 왜 가상환경? - 맥 + 리눅스 + 윈도우즈
맥 OS + 리눅스 뿐만 아니라 맥 OS + 윈도우즈 서버 환경도 같이 지원해야 하는 입장에선 가상환경에 머무는게 최선
맥을 사용하면서 어쩔 수 없이 윈도우즈 어플리케이션을 사용하기 위해 가상화 프로그램을 사용

## Virualbox vs VMware vs parallels
- 버추어 박스는 몇번 설치한 os가 부팅이 안될정도로 깨진 경우가 있어서 vm을 사용중입니다.
- 괜히 비싼 돈주고 쓰는게 아니죠. VM Ware도 써봤는데, 안정성이나 속도나 편의성이나 패럴에 못미칩니다
- 성능의 패럴렐즈, 범용성의 VMware, 무료의 버추얼박스

### 오라클의 버츄얼박스(VirtualBox)
무료로 사용

![스크린샷 2024-08-03 오전 9 26 22](https://github.com/user-attachments/assets/f7e653ee-e864-4f6b-9375-6c3ed235a0d0)

### VMware의 퓨전
원도우즈와 리눅스 다시 말해 맥을 제외한 거의 모든 컴퓨터 시스템에서 가장 큰 가상화 프로그램 개발사로 페러렐즈와 비교될 수 없는 수준

![스크린샷 2024-08-03 오전 9 26 56](https://github.com/user-attachments/assets/343b6a87-a407-4bbd-a559-719dde6284f8)

### 맥의 패러렐즈
원도우즈나 리눅스 기반의 가상 프로그램 개발을 포기하고, 맥에 집중

![스크린샷 2024-08-03 오전 9 27 40](https://github.com/user-attachments/assets/c49fe1d6-8014-418b-95a7-63c9018a26b6)


# 왜 도커? - 1. 프로젝트별 버전별 분리
각 프로젝트별로 확실히 분리됨 ⇒ 버전이 달라도 혼란x
- 가상환경

![스크린샷 2024-08-03 오전 9 12 51](https://github.com/user-attachments/assets/7f8799a2-a7a8-49ac-a88d-7e923e73a9d9)

- 도커

![스크린샷 2024-08-03 오전 9 13 05](https://github.com/user-attachments/assets/4d226614-025b-4c22-bbf8-82499bb6ac41)

# 왜 도커? - 2. 컴퓨터 자원
효율적으로 환경을 분리할 수 있음
컴퓨터 자원이 낭비되는 부분이 크지 않음

![스크린샷 2024-08-03 오전 9 17 03](https://github.com/user-attachments/assets/334640d6-b53e-46d0-9da3-6eb230fd12f9)

# 왜 도커? - 3. 서버환경과 개발환경 동일하게 세팅

![스크린샷 2024-08-03 오전 9 17 40](https://github.com/user-attachments/assets/bd99b581-d780-4490-a902-9e555be8291d)

# 도커 용어
<img width="1334" alt="스크린샷 2024-08-03 오전 9 49 15" src="https://github.com/user-attachments/assets/713bfd6b-ce64-443d-abe4-c69bd51e0892">

## 명령어 - 이미지
![스크린샷 2024-08-03 오전 9 51 45](https://github.com/user-attachments/assets/41b6d607-a640-4e95-9e84-b3597033c0e9)


![스크린샷 2024-08-03 오전 9 52 39](https://github.com/user-attachments/assets/ec257187-b252-4bf1-b940-d2d2352d9ff0)

## 명령어 - 컨테이너
### 컨테이너 생성
![스크린샷 2024-08-03 오전 9 53 28](https://github.com/user-attachments/assets/3abdc79e-dac9-4e0e-bee9-e08249b491e1)

``` bash
# 컨테이너 생성
docker run [이미지명]

# 동일한 이미성로 컨테이너 2개 생성하기
docker run httpd
docker run --name ws2 httpd
```

``` bash
# 컨테이너 생성하면서 포트포워딩하기
docker run --name ws3 -p 8081:80 httpd
```
![스크린샷 2024-08-03 오전 10 02 59](https://github.com/user-attachments/assets/00a851d2-7afe-4a67-9812-96761d3870fa)
``` bash
# 컨테이너 생성 + 컨테이너에 대해 상호작용 가능한 터미널 세션을 시작
docker run -it [이미지명]
```
![스크린샷 2024-08-03 오전 10 03 21](https://github.com/user-attachments/assets/8f9c122c-ac11-4f92-baf9-3c29ba52bc90)
``` bash
# 컨테이너를 생성 
# + 호스트의 8888 포트를 컨테이너의 80 포트로 매핑
# + 호스트의 htdocs 디렉토리를 컨테이너의 htdocs 디렉토리로 매핑
docker run -p 8888:80 -v ~/Desktop/htdocs:/usr/local/apache2/htdocs/ httpd
```
![스크린샷 2024-08-03 오전 10 03 59](https://github.com/user-attachments/assets/6af3091f-8e30-4e9e-b949-d21bcf101ec4)

### 컨테이너 접속
``` bash
# 실행 중인 특정 컨테이너에 접속
# + 상호작용 가능한 Bash 셸을 시작
# 컨테이너 내부에서 직접 명령어를 실행하거나 디버깅 작업을 수행할 때 
docker exec -it [컨테이너명] /bin/bash
```
![스크린샷 2024-08-03 오전 10 13 04](https://github.com/user-attachments/assets/e499e5b9-fbde-4566-87de-a4ee3edfe162)

## 그 외
``` bash
docker ps : 실행중인 컨테이너 확인  

docker stop ws2 : 컨테이너 중지

docker ps -a : 

docker start ws2 : 재시작

docker logs ws2 : 로그 출력

docker logs -f ws2 : 로그의 변화를 실시간 watching 가능

docker stop ws2 : 컨테이너 중지

docker rm ws2 : 컨테이너 삭제

docker images : 이미지 확인

docker rmi httpd: 이미지 삭제
```

# Dockerfile - 이미지 생성

도커 이미지를 생성하기 위한 **스크립트** 파일

여러 키워드를 사용하여 dockerfile을 작성하여 **빌드**를 보다 쉽게 수행할 수 있음

node 이미지를 다운받고 dockerfile로 내가 원하는 설정을 이미지로 만들어 컨테이너로 쉽게 만들 수 있도록 한다

![스크린샷 2024-08-03 오후 1 41 19](https://github.com/user-attachments/assets/f14fb399-56cf-4050-aec7-b0064782dc50)
![스크린샷 2024-08-03 오후 1 41 55](https://github.com/user-attachments/assets/9cee68fa-2c65-4e49-91e5-de7d21d629a9)

``` bash
# 공식 이미지를 개조
FROM openjdk:17-jdk-slim  

ARG JAR_FILE=build/libs/app.jar

COPY ${JAR_FILE} ./app.jar

ENV TZ=Asia/Seoul

ENTRYPOINT ["java", "-jar", "./app.jar"]
```
``` bash
# 공식 이미지를 개조
FROM node: 12.18.4   

# '이미지 생성 과정'에서 실행할 명령어 : npm명령어로 http-server를 설치한다
RUN npm install -g http-server  

# 이미지 내에서 명령어를 실행할(현 위치로 잡을) 디렉토리 설정
WORKDIR / home/node/app

# 컨테이너 실행시 실행할 명령어
CMD ["http-server", "-p", "8080", "•/public" ]
```
![스크린샷 2024-08-03 오후 1 42 04](https://github.com/user-attachments/assets/3848da89-6bea-4d67-bd1f-2003762e2982)

# 이미지 생성
``` bash
# docker build : Docker 이미지를 생성
# -t [이미지명] : 이미지를 web-server-build라는 이름으로 태그
# . : Docker 빌드 컨텍스트의 위치를 현재 디렉토리로 지정
#     빌드 컨텍스트는 Dockerfile과 이미지 생성에 필요한 모든 파일이 위치한 디렉토리
# 이 명령어가 실행되려면 현재 디렉토리에 Dockerfile이 있어야 함.
docker build -t [이미지명] .
```

### RUN & CMD

RUN : 이미지를 빌드할 때 실행할 명령어

![스크린샷 2024-08-03 오후 1 42 17](https://github.com/user-attachments/assets/ccfe62b4-9cbd-4449-b31f-474522b331ae)

CMD : 컨테이너가 가동될 때 실행할 명령어

![스크린샷 2024-08-03 오후 1 42 30](https://github.com/user-attachments/assets/c98ce7cc-63a4-4f8f-8bcb-c69aef7a164d)


**CMD 예시**
```bash
# 공식 이미지를 개조
FROM node: 12.18.4   

# '이미지 생성 과정'에서 실행할 명령어 : npm명령어로 http-server를 설치한다
RUN npm install -g http-server  

# 이미지 내에서 명령어를 실행할(현 위치로 잡을) 디렉토리 설정
WORKDIR / home/node/app

# 컨테이너 실행시 실행할 명령어
# 컨테이너는 실행 시 이 사이트를 8080으로 송출할 것
CMD ["http-server", "-p", "8080", "•/public" ]
```
![스크린샷 2024-08-03 오후 1 43 50](https://github.com/user-attachments/assets/39fa40c4-4963-4d9d-a9df-cafafeb3fdc7)
### COPY & VOLUME

- 볼륨 : 연결

![스크린샷 2024-08-03 오후 1 45 05](https://github.com/user-attachments/assets/cd621415-a9e9-4464-a43c-b385293ded6e)

```bash
# path 디렉토리를 볼륨으로 설정
VOLUME ["/container/path"]
```
- 컨테이너를 실행할 때 호스트의 디렉토리와 매핑하려면 docker run 명령어에서 -v 옵션을 사용
```bash
# 호스트의 /host/path 디렉토리를 컨테이너의 /container/path 디렉토리에 마운트
# 호스트 시스템의 파일이 컨테이너에서 사용될 수 있음
# 컨테이너 내부에서 수정된 파일은 호스트 시스템에 그대로 반영
docker run -v /host/path:/container/path my-image
```
- COPY : 복사

![스크린샷 2024-08-03 오후 1 44 56](https://github.com/user-attachments/assets/cbe70e05-82a1-475d-879b-31bb21ca7cc9)

- 컨테이너 초기화 과정에 필요해서 이미지 안에 미리 넣어두어야 할 때
- **이미지 빌드 시 정적 파일 추가**
    - 애플리케이션 코드, 설정 파일, 바이너리 등 정적 파일을 이미지에 포함시키기 위해 사용
    - 이 파일들은 컨테이너가 시작될 때 이미 이미지 내부에 존재하게 됨
- **파일 시스템 변경**
    - Dockerfile에서 이미지를 빌드하는 동안 파일 시스템을 수정하는 데 사용
```bash
# 호스트 시스템의 ./scripts를 이미지 내부의 /docker-entrypoint-initdb.d/로 복사
# 이 파일은 이미지 빌드 시점에 컨테이너 파일 시스템에 추가됨

FROM ubuntu:20.04
COPY ./scripts /docker-entrypoint-initdb.d/
```