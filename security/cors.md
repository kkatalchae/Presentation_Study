# CORS
## CORS란?
> CORS(Cross-Origin Resource Sharing) 교차 출처 리소스 공유

CORS를 영문 그대로 번역하면 "출처 리소스를 공유한다" 라는 뜻이다
* 출처 = Origin
  * 도메인, 프로토콜, 포트 => 서버를 접근하기 위해 필수로 알아야 하는 정보들. 서버의 정체성!

![image](https://github.com/user-attachments/assets/ee37739c-8c09-46fc-8753-75383eff8cc8)
출처: [toss 개발 블로그 - CORS](https://docs.tosspayments.com/resources/glossary/cors)

* 출처가 교차한다(Cross-Origin) = 출처가 다르다 = !프로토콜or!도메인or!포트

출처가 동일한 리소스 예시
| isSame | Origin 1 | Origin 2 |
|:--:|:--:|:--:|
|O|`https://www.domain.com:3000/`|`https://www.domain.com:3000/`|
|O|`https://www.domain.com:3000/user?query=name&page1`|`https://www.domain.com:3000/`|
|x->프로토콜 다름|`http://www.domain.com:3000/`|`https://www.domain.com:3000/`|
|x->포트 다름|`http://www.domain.com/`|`http://www.domain.com:3000/`|
|O->도메인 같음|`http://domain.com/`|`http://www.domain.com/`|
|O->도메인 같음|`http://www.do.com/`|`http://www.domain.com/`|

* SOP(Same-Origin Policy) : 동일 출처 정책
  * 동일한 Origin(출처) 사이에서만 리소스를 공유할 수 있다. 모든 처리가 같은 도메인 내에서만 일어나야 한다.
  * 다른 출처로 요청을 보내는 것 자체를 악성행위로 간주하고(CSRF, XSS) 아예 다른 출처랑 통신 못하도록 막아버리는 것
  * 문제는 웹 기술이 발전하면서 다른 출처로 요청하고 응답받아오는 수요가 늘어남ㅠ
* 그래서 CORS가 뭔데?
  * SOP 정책 때문에 호출하고자 하는 API를 백엔드서버를 한 번 거쳐서 호출하거나 JSONP같은 꼼수가 등장했는데 꼼수는 꼼수일뿐... 근본적인 해결 방법이 필요해짐
  * CORS의 등장 -> 리소스 호출이 허용된 출처를 서버가 명시해두면, 출처가 달라도 요청과 응답을 주고받을 수 있도록 만들어둔 정책

## CORS 접근 제어 시나리오 - CORS에러는 어떻게 발생하는가?
### Simple Requests
![image](https://github.com/user-attachments/assets/38cd8ae8-6c85-4867-8792-db3b653d1a05)
1. 브라우저가 다른 출처로 요청을 보낼 때 헤더에 Origin을 추가해서 전송
```
Origin: https://www.site.com
```
2. 서버는 응답 해더에 Access-Controle-Allow-Origin 을 실어 보냄(서버에서 허용한 출처 정보-즉 서버에서 설정한 CORS설정)
```
Access-Control-Allow-Origin: * //전체 허용
```
3. 브라우저가 Access-Controle-Allow-Origin에 request를 전송하고자하는 origin이 포함되어 있으면 해당 origin으로 request 전송, **아니면 CORS 에러 발생**


SimpleRequest의 조건
* method : GET, POST, HEAD
* header : Accept, Accept-Language, Content-Language, Content-Type
  * Content-Type의 경우 application/x-www-form-urlencoded, multipart/form-data, text/plain 만 허용
* Cookie나 Autorization등 추가 헤더를 사용하면 Simple Request의 조건을 맞추기 힘들다

### Preflight Requests
우리가 웹 어플리케이션을 개발할 때 가장 자주 마주치는 시나리오. 요청을 한번에 보내지 않고 예비요청/본요청으로 나눠 서버에 전송
* 본요청 보내기전의 예비요청을 Preflight라 부르고, OPTION 메서드가 사용됨
* 본요청 전에 "이런 메서드와 헤더로 요청을 보낼건데 너네 서버 CORS에서 허용된 Origin이니?"하고 물어보는 과정
![image](https://github.com/user-attachments/assets/10ad8a68-53a9-4040-b4b3-029e8ba1069f)
![image](https://github.com/user-attachments/assets/af1e2d50-8f55-4a27-b557-34906741482c)

1. 브라우저가 서버에 Access-Controle-Allow-Origin 헤더와, 요청하는 메서드, 사용하는 헤더 리스트를 전송
```
[OPTIONS https://www.api.com]
Origin: https://www.site.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type
```
2. 서버가 허용하는 오리진과 메서드, 헤더 목록 + preflight 캐시 기간인 Access-Controle-Max-Age를 내려줌
```
Access-Control-Allow-Origin: https://www.site.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
```
3. 브라우저가 위 응답을 바탕으로 CORS 정책 위반을 판단하면 에러 리턴

### Credentialed Requests
쿠키, 토근과 같이 식별 정보가 담긴 요청에서는 좀 더 엄격하게 CORS 요청을 처리함. 클라이언트가 요청 전송시 **credentials 옵션**을 설정해줘야 함


credential 옵션 종류 `credentials: <option>`
* same-origin : 같은 출처간 요청에만 인증정보 담을 수 있음
* include : 모든 요청에 인증 정보 담을 수 있음
* omit : 모든 요청에 인증 정보 담지 않음


주의사항
* 서버는 응답 시 `Access-Control-Allow-Credentials` 를 true로 설정해야 함
* `Access-Control-Allow-Origin` 는 와일드카드가 될 수 없음

## CORS에러 대응 방법
### Access-Control-Allow-Origin 세팅(서버에서 해결)
정석대로 서버에서 Access-Control-Allow-Origin 세팅해주는 방식.

```java
    @Override
    public void addCorsMappings(CorsRegistry corsRegistry) {

        corsRegistry.addMapping("/**")
                .allowedOrigins("http://localhost:3000","http://localhost:80",
                        "https://farmigo-client.vercel.app",
                        "http://api.farmigoapp.com:80","http://api.farmigoapp.com",
                        "https://api.farmigoapp.com:443","https://api.farmigoapp.com")
                .allowedMethods("*")
                .allowedHeaders("*")
                .maxAge(3600L)
                .exposedHeaders("*")
                .allowCredentials(true);
    }
```
* 왜인지는 모르겠는데... 포트까지 명시해줬을때는 CORS 에러가 나다가, 포트를 지운 버전을 추가해주니까 CORS에러가 해결되었던 경험;; -> 프론트에서 접근하는 리소스 형식 그대로 지켜줘야 하나봄.. 실제 열린 포트랑 상관 없이

### Webpack Dev Server로 리버스 프록싱(프론트에서 해결)
대부분의 CORS에러는 로컬 테스트 중 발생하는 에러 -> 프론트엔드 개발자가 `webpack-dev-server` 로 프록시 기능을 사용해서 CORS 정책을 우회하는 방법
```
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'https://api.domain.com',
        changeOrigin: true,
        pathRewrite: { '^/api': '' },
      },
    }
  }
}
```
* 이렇게 설정해두면 로컬 환경에서 `/api` 로 설정하는 URL 요청에 대해 `localhost:8000/api` 가 아니라 `https://api.domain.com` 으로 프록싱한다고 함 -> CORS 정책 지킨것처럼 낚기
* 실제 프로덕션 환경에서의 서버와 같은 경우에서만 사용하는걸 권장 -> 테스트 할 때는 CORS 우회하니까 괜찮았는데 막상 프로덕션에서는 CORS 설정 안되어있어서 에러날 수 있으니까

## 그 외 잡지식들
* CSRF(Cross Site Request Forgery attack)
  * [출처](https://nordvpn.com/ko/blog/csrf/)
  * 사용자가 웹 애플리케이션에 특정 요청을 보내도록 유도하는 행위
  * 이메일, 웹사이트에 request가 삽입된 하이퍼링크를 심어놓고, 사용자가 해당 하이퍼링크를 클릭하면 요청이 전송되도록 함
  * 공격자가 웹 브라우저를 속여서 사용자가 로그인한 애플리케이션에서 작업을 진행하는 방식으로 진행.
    * 핵심은 인증된 세션을 악용하는 공격방식.. 로그인을 함으로서 얻을 수 있는 정보를 탈취해가는 것
* XSS(Cross Site Scripting) : 메일/웹 페이지등에 스크립트 코드를 삽입해서 개발자가 고려하는 기능이 작동하도록 하는 방식
  * [출처](https://4rgos.tistory.com/1)
  * 나는 SQL Injection과 유사하다고 느꼈음...
  * Relfected XSS : 공격자가 XSS에 취약한 웹사이트에 XSS공격을 위한 스크립트를 포함한 URL을 사용자에게 노출시킴(GET 파라미터 조작 메이비?) => 해당 URL을 클릭하는 것 만으로 Requst를 해커 서버에 전송하게 됨
  * Stored XSS : 웹 사이트 게시판에 스크립트를 삽입. 공격자가 스크립트를 삽입해두고 공격대상자가 해당 게시글 클릭하도록 유도시킴
## 참고
* [toss 개발 블로그 - CORS](https://docs.tosspayments.com/resources/glossary/cors)
  * [결제창에서 CORS 대응하기](https://docs.tosspayments.com/blog/payment-window-cors-error)
* [AWS - CORS란](https://aws.amazon.com/ko/what-is/cross-origin-resource-sharing/)
* [CORS가 무엇일까 feat. SOP](https://hudi.blog/sop-and-cors/)
* [CORS는 왜 이렇게 우리를 힘들게 하는걸까?](https://evan-moon.github.io/2020/05/21/about-cors/)