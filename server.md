# 간단한 HTTP 서버 구현
### 목적

- 서버의 기본 구조와 동작원리 이해
- `소켓`을 사용하여 클라이언트 연결 수락
- `HTTP 요청` 파싱
- 간단한 정적 파일 서비스 구현

### 한계

- 많은 클라이언트 요청을 처리하는 데 한계
    - 기본적인 기능만을 제공하며, 성능 최적화가 거의 이루어지지 않음
    - 동시 접속 처리, 비동기 처리, 효율적인 I/O 처리 등은 고려되지 않음
- 보안 기능이 거의 없으며, 많은 보안 취약점(예: SQL 인젝션, XSS, CSRF 등)에 노출
- 기본적인 HTTP 요청 처리와 정적 파일 서빙 기능만 제공
    - 고급 기능(로드 밸런싱, 리버스 프록시, SSL/TLS, 캐싱 등)은 구현되어 있지 않음

### 간단한 HTTP 서버 구현

1. 자바 새 프로젝트 생성
2. 서버 클래스와 public 폴더 생성
    1. 서버 클래스
        - ![스크린샷 2024-08-17 오후 9 38 52](https://github.com/user-attachments/assets/858c89c7-d300-4f34-a36b-3e7e8e99b1fa)
        - 패키지 이름을 `com.example.simplehttpserver`로 지정
        - 클래스 이름을 `SimpleHttpServer`로 지정
        - 자바 파일 작성
    2. public 폴더
        - `public` 폴더를 생성하고, 여기에 `index.html` 등의 정적 파일을 추가.

        ```html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Simple HTTP Server</title>
        </head>
        <body>
            <h1>Hello, World!</h1>
        </body>
        </html>
        ```
3. IDE(통합개발환경)이 자바 애플리케이션을 실행하기 위한 구성 설정
    <img width="1032" alt="스크린샷 2024-08-17 오후 9 44 46" src="https://github.com/user-attachments/assets/bc2306e0-70f5-4a78-bfd7-eec0c3bddbf1">
    - **Run > Edit Configurations > Add New Configuration**을 클릭하고 **Application**을 선택
        - **Application** 구성은 Java 애플리케이션을 실행하는 데 사용되는 가장 기본적인 실행 구성
        - 이 설정을 통해 IDE는 어떤 Java 클래스가 프로그램의 진입점(Main class)인지, 그리고 해당 프로그램을 어떤 방식으로 실행할지를 이해
    - **Name**에 `SimpleHttpServer`를 입력
        - 실행 구성을 구별하기 위한 이름을 지정. 아무렇게나 지어도 됨
    - **Main class**에 `com.example.simplehttpserver.SimpleHttpServer`를 설정
        - 프로그램의 진입점을 지정. 여기서는 SimpleHttpServer클래스의 main()메서드가 애플리케이션의 진입점이 됨.
        - **Module** 드롭다운 메뉴에서 해당 프로젝트 모듈을 선택
            - Module not specified 오류
                
                "Module not specified" 오류는 IntelliJ IDEA에서 실행 구성을 설정할 때 모듈이 지정되지 않았기 때문에 발생
                
                **Module** 드롭다운 메뉴에서 해당 프로젝트 모듈을 선택
                
    - Apply를 클릭하고 OK를 클릭
4. 애플리케이션 실행
    - 상단의 Run 버튼을 클릭하여 프로젝트를 실행

## 서버 클래스 분석
### 서버 시작
```java
ServerSocket serverSocket = new ServerSocket(8080);
```
- 서버 시작(클라이언트 연결 요청을 받을 준비)
- 포트 바인딩
- 네트워크 리스닝(8080 포트에서 들어오는 연결 요청을 대기)

### 문자 처리 
```java
try (Socket clientSocket = serverSocket.accept();
     BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
     OutputStream out = clientSocket.getOutputStream()) {
```
- 버퍼링으로 I/O 작업의 효율성 높임
- 문자 처리 
    - 소켓에서 응답을 받으면
    - clientSocket으로부터 입력 스트림을 얻고, `raw 바이트 데이터` → 읽을 수 있는 InputStream객체로 반환
    - `InputStream(바이트 스트림)`을 `Reader(문자 스트림)`로 변환
    - 문자 입력 스트림에 버퍼링 기능을 추가

### HTTP 요청 파싱
```java
String requestLine = in.readLine();
if (requestLine == null) continue;

String[] requestParts = requestLine.split(" ");
if (requestParts.length != 3) continue;

String method = requestParts[0];
String path = requestParts[1];
```
- 입력 스트림에서 한 줄씩 텍스트를 읽음
- requestLine이 null(입력 스트림의 끝)이라면, continue 문을 실행
- requestLine(HTTP 요청의 첫 줄)을 공백(" ")을 기준으로 분할하여 문자열 배열 requestParts에 저장
    - "GET /index.html HTTP/1.1"
- 분할된 요청 부분이 정확히 3개가 아니면 현재 반복을 중단하고 다음 반복으로 넘어감
- requestParts 배열의 첫 번째 요소를 method 변수에 저장 
- requestParts 배열의 두 번째 요소를 path 변수에 저장

```java
package com.example.simplehttpserver;

import java.io.*;
import java.net.*;
import java.nio.file.*;

public class SimpleHttpServer {
    public static void main(String[] args) throws IOException {
    
        ServerSocket serverSocket = new ServerSocket(8080);

        while (true) {
		        // 소켓에서 응답답 받으면 바이트 -> 문자로 변경하고, 버퍼링 기능을 추가
            try (Socket clientSocket = serverSocket.accept();
                 BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                 OutputStream out = clientSocket.getOutputStream()) {

                // HTTP 요청 파싱
                String requestLine = in.readLine();
                if (requestLine == null) continue;

                String[] requestParts = requestLine.split(" ");
                if (requestParts.length != 3) continue;

                String method = requestParts[0];
                String path = requestParts[1];

                // 간단한 라우팅
                if ("/".equals(path)) {
                    path = "/index.html";
                }

                // 정적 파일 서비스
                try {
                    byte[] fileContent = Files.readAllBytes(Paths.get("public" + path));
                    String contentType = getContentType(path);

                    // HTTP 응답 헤더
                    String headers = "HTTP/1.1 200 OK\r\n" +
                            "Content-Type: " + contentType + "\r\n" +
                            "Content-Length: " + fileContent.length + "\r\n" +
                            "\r\n";

                    out.write(headers.getBytes());
                    out.write(fileContent);
                } catch (IOException e) {
                    // 파일을 찾을 수 없을 경우 404 응답
                    String response = "HTTP/1.1 404 Not Found\r\n\r\n";
                    out.write(response.getBytes());
                }
            } catch (IOException e) {
                System.out.println("Error handling client request: " + e.getMessage());
            }
        }
    }

    private static String getContentType(String path) {
        if (path.endsWith(".html")) return "text/html";
        if (path.endsWith(".css")) return "text/css";
        if (path.endsWith(".js")) return "application/javascript";
        return "text/plain";
    }
}
```

# 도커로 nginx 서버 환경 설정
1. docker-compose.yml 파일 
    ```yml
    version: "3.8"
    services:
    study-database:
        # ... (데이터베이스 설정)

    app:
        build:
        context: ./app
        dockerfile: Dockerfile
        container_name: study-app
        ports:
        - "8080:8080"
        depends_on:
        - study-database
        environment:
        - SPRING_DATASOURCE_URL=jdbc:mysql://study-database:3306/study-version
        - SPRING_DATASOURCE_USERNAME=root
        - SPRING_DATASOURCE_PASSWORD=${SPRING_DATASOURCE_PASSWORD}

    nginx:
        image: nginx:alpine
        container_name: study-nginx
        ports:
        - "80:80"
        - "443:443"
        volumes:
        - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
        - ./nginx/conf.d:/etc/nginx/conf.d:ro
        - ./certbot/conf:/etc/letsencrypt
        - ./certbot/www:/var/www/certbot
        depends_on:
        - app
    ```
2. Nginx 설정 파일 생성 
    - Nginx의 메인 설정 파일로, 서버 블록과 로깅, 이벤트 설정 등을 포함
        - Nginx의 메인 설정 파일의 `include` 지시어를 통해 `conf.d` 디렉터리 내의 설정 파일을 불러옴
    ```php
    user  nginx;
    worker_processes  auto;

    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
    }

    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';

        access_log  /var/log/nginx/access.log  main;

        sendfile        on;
        keepalive_timeout  65;

        include /etc/nginx/conf.d/*.conf;
    }
    ```
3. Nginx 서버 블록 설정
    - listen : 서버(Nginx)가 HTTP 요청을 듣는 포트 지정.
    - server_name : 해당 서버 블록이 처리하는 도메인 이름을 지정
    - root : 웹 페이지 파일이 위치하는 디렉토리 경로를 지정
    - location : 해당 요청경로로 시작하는 경우의 처리를 정의

    ```yml
    # 하나의 가상 서버를 정의하는 블록. 
    # 여러 가상 서버를 설정할 수 있으며 각 서버는 서로 다른 도메인이나 포트에서 요청을 처리할 수 있음. 
    server {

        listen 80;

        server_name localhost;
        
        location / {
            proxy_pass http://study-app:8080; 
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Let's Encrypt 인증을 위한 설정
        location /.well-known/acme-challenge/ {
                # Let's Encrypt가 인증 파일을 찾을 수 있도록 /var/www/certbot 디렉터리를 루트로 설정
                # 이 디렉터리에는 인증을 위한 파일이 위치
            root /var/www/certbot;
        }
    }
    ```
4. 빌드
5. 컨테이너 실행
    - docker-compose up -d 