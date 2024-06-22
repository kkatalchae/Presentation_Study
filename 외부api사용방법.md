# 외부 API 테스트 - REST API 테스트 도구

curl 또는 postman으로 api를 직접 호출해보고 결과를 확인

- CURL과 Postman
    
    API 엔드포인트를 호출하고 싶은 경우 사용하는 개발자 도구 
    
- CURL
    - curl은 1998년부터 존재하기 때문에 널리 사용됨. Linux, Mac, Windows에서 사용할 수 있으며, 대부분의 컴퓨터에도 기본적으로 설치됨
    - CURL이 설치되었는지 확인하려면 명령 줄에 curl --help 명령어를 입력하여 확인
- 포스트맨
    - postman은 curl과 달리 그래픽 사용자 인터페이스를 가지고 있음. 따라서, 명령 줄이 익숙하지 않은 경우에는 Postman을 사용
    - Postman은 JSON 결과를 읽기 쉬운 방식으로 포맷팅

<img width="673" alt="스크린샷 2024-06-22 오전 11 32 40" src="https://github.com/kkatalchae/Presentation_Study/assets/67645131/4f831d6c-8e5b-489f-89f3-40685f495f51">

> 💡 REST API란?
> 결론 : REST는 화면(HTML)이 아닌 데이터 자체를 리턴함
> 
> 1. REST
>    - Representational State Transfer의 줄임말
>    - 컴퓨터 과학자 Roy Fielding이 만듦
>    - 웹(HTTP)의 장점을 활용한 **아키텍쳐**
>        1. 웹에서 사용되는 표준화된 HTTP 메서드와 URI
>            
>            이를 통해 클라이언트와 서버 간의 통신이 일관되고 표준화된 방식으로 이루어짐
>            
>        2. 무상태성
>            
>            REST는 이 무상태성을 그대로 활용하여 서버의 부하를 줄임
>            
>        3. 그 외 캐시 가능 등 총 6개정도의 특징이 있음
>    - REST는 프로토콜이나 표준이 아닌 아키텍처 제약 조건. API 개발자는 REST를 **다양한 방식으로 구현**할 수 있다
>    - REST 요소
>        - 자원 -  URI. 모든 것을 명사로 표현하고 세부 리소스에는 id를 붙임
>        - HTTP 메서드 - 자원에 대한 행위 (POST, GET, PUT, DELETE)
>        - 메시지 - 메시지 포맷 (json, xml..)
>    
>    ```java
>    HTTP POST, http://member
>    {
>    	"users" : {
>    		"name" : "terry"
>    	}
>    }
>    ```
>    
>2. API
>    - 클라이언트의 요청을 서버에 **전달**하고, 서버의 결과물을 클라이언트에게 **돌려주는** **인터페이스**
>    - API를 통해 서버에서 제공하는 데이터를 요청해서 사용할 수 있음

### REST API 탄생

REST는 Representational State Transfer라는 용어의 약자로 2000년도에 로이 필딩 (Roy Fielding)의 박사학위 논문에서 최초로 소개. 

로이 필딩은 HTTP의 주요 저자 중 한 사람으로 그 당시 웹(HTTP) 설계의 우수성에 비해 제대로 사용되어지지 않는 것이 안타까워 웹의 장점을 최대한 활용할 수 있는 아키텍처로써 REST를 발표.

### REST를 다양한 방식으로 구현

REST는 프로토콜이나 표준이 아닌 아키텍처 제약 조건. 

API 개발자는 REST를 다양한 방식으로 구현할 수 있다.

(REST는 시스템 설계 시 따를 수 있는 가이드라인과 제약 조건을 제공하지만, 이를 어떻게 구현할지는 개발자의 선택에 따라 달라질 수 있다)

- 데이터 형식
    
    REST API는 JSON, XML, HTML, 텍스트 파일 등 다양한 형식으로 데이터를 주고받음. 어떤 형식을 사용할지는 API의 목적과 클라이언트의 요구에 따라 결정
    
- 리소스 설계
    - `GET /users` vs `GET /users/list`
    - `POST /users/{id}/orders` vs `POST /orders` 
    (where the user ID is in the payload)

> 참고
> - [REST API 모범사례](https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design) - 마소
> - [REST API 모범사례](https://appmaster.io/ko/blog/rest-api-mobeom-sarye) - 앱마스터?
> - [REST API](https://meetup.nhncloud.com/posts/92) - NHN

# java에서 외부 API 사용하는 방법

1. api 환경변수 설정
    - application.yml파일에 rest api키 설정
    - 인텔리J 환경변수로 설정
    <img width="467" alt="스크린샷 2024-06-22 오전 11 37 01" src="https://github.com/kkatalchae/Presentation_Study/assets/67645131/f273e44c-1f75-4139-9169-03d0ae651aec">

2. api에서 응답한 값을 받을 dto
    - KakaoApiResponseDto(MetaDto, DocumentDto)
3. 외부 api 호출하고 응답값을 dto에 리턴
    1. RestTemplate(http client 모듈)
        
        ```java
        @Slf4j
        @Service
        @RequiredArgsConstructor
        public class KakaoAddressSearchService {
        
          private final RestTemplate restTemplate;
          private final KakaoUriBuilderService kakaoUriBuilderService;
        
          @Value("${kakao.rest.api.key}")
          private String kakaoRestApiKey;
        
          public KakaoApiResponseDto requestAddressSearch(String address) {
        
        			//validation 체크
              if(ObjectUtils.isEmpty(address)) return null;
        
        			//uri 받아오기
              URI uri = kakaoUriBuilderService.buildUriByAddressSearch(address);
        
        			//header에 담기
              HttpHeaders headers = new HttpHeaders();
              headers.set(HttpHeaders.AUTHORIZATION, "KakaoAK " +kakaoRestApiKey);
              HttpEntity httpEntity = new HttpEntity<>(headers);
        
              // kakao api 호출
              return restTemplate.exchange(uri, HttpMethod.GET, httpEntity, KakaoApiResponseDto.class).getBody();
          }
        ```
        
    2. UriComponentBuider
        
        `https://dapi.kakao.com/v2/local/search/address.json?query=전북 삼성동 100`
        
        ```java
        KakaoUriBuilderService>>
        
        public URI buildUriByAddressSearch(String address) {
        
            UriComponentsBuilder uriBuilder = UriComponentsBuilder.fromHttpUrl(KAKAO_LOCAL_SEARCH_ADDRESS_URL);
            uriBuilder.queryParam("query", address);
        
            URI uri = uriBuilder.build().encode().toUri();
        
            return uri;
        }
        ```