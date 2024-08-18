# Spring Rest Docs And Swagger

> api 문서 도입하기

0. 목차
1. api 문서
   1. API와 API문서
   2. API 문서의 구성
2. Swagger
   1. Swagger?
   2. 장점 및 단점
   3. 사용 방법
3. SpringRestDocs
   1. SpringRestDocs?
   2. 장점 및 단점
   3. 사용 방법
4. 비교
    1. 우리 서비스에 맞는 선택 
    2. 결론
5. 참고


--------------------------

## 1. API 문서

### 1. API와 API문서
   - 서버와 클라이언트가 데이터를 주고 받을 수 있도록 도움을 주는 매개체
   - API를 사용하기 위해선, 서버-클라이언트는 주고받는 데이터의 규칙을 약속해야함.
   - 규칙은 데이터의 형식, validation, 전달 방식, 요청에 대한 결과 등 ...
   - 이 규칙을 확인할 수 있는 문서가 API문서
   - 문서를 작성하는 방법에는 엑셀, 사내 위키, 자동 API 문서화 도구 ...
   - 자동 API 문서화 도구에는 Swagger, Redoc, RapiDoc, SpringRestDocs... 

### 2. API 문서의 구성
   1. API에 대한 간략한 소개 
      - 개발 배경, 목적...등  
   2. 공통 요청(Request)/응답(Response) 형식
      - 대개 한 프로젝트(서비스)의 API는 통일된 형식.
      - 예를 들어 요청 데이터의 형식이 'application/json'
      - 응답 형식 또한, 상태코드를 status로 표현하기도 하고 message를 통해 설명하기도 함
      - 공통사용 되는 에러코드를 정리하기도 함
   3. 에러형식
      - 에러가 발생했을 경우, 어떠한 상황에서 어떠한 에러를 발생되는지 작성
   4. Request
      - Request Syntax
        - API형태, 구조에 대한 정의
        - 어떠한 메서드를 사용하고 요청 URL의 형태
        - 예시 코드
      - Request Header
        - 메시지의 총 길이(Content-Length)
        - 형식(Content-Type)
        - 인증
        - 요청을 보내는 클라이언트에 대한 정보(User-Agent)...
      - Request Element
        - 해당 요청의 실제 메세지, 내용
        - API를 요청하기 위한 파라미터와 파라미터의 유형, 필수 여부, 제약사항...
    5. Response
        - Response Element
          - API요청에 대한 결과값
          - Body에 본문 내용을 싣기도 하고, status 코드로 나타나기도 함.
          - 필드, 필드 유형, 필수 여부, 설명 등


----
## 2. Swagger
### 1. Swagger?
- API를 설계하고 문서화하는데 도움이 되도록 구축된 오픈소스
- 의존성 설치 후  http://localhost:8080/swagger-ui.html (도메인+swagger-ui.html) 접속 시 api 문서를 볼 수 있음
### 2. 장점 및 단점
- 장점
  - 적용하기 쉬움
  - 배우기 쉬움
  - 작성하기 쉬움
  - 테스트할 수 있는 UI를 제공해서 문서화면에서 API를 바로 테스트 가능
- 단점
  - 운영 코드에 침투적
### 3. 사용 방법
1. 의존성 추가
```groovy
compile 'io.springfox:springfox-swagger2:2.9.2'
compile 'io.springfox:springfox-swagger-ui:2.9.2'
```
2. config 파일추가
```java
@Configuration
@EnableSwagger2 //swagger2 버전을 활성화 하겠다는 의미
public class SwaggerConfiguration {

    /**
     * Docket : Swagger 설정을 할 수 있게 도와주는 클래스
     * API 대한 정보 자체는 컨트롤러에서 작성
     */
    @Bean
    public Docket apiV1() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("groupName1")
                .select()
                .apis(RequestHandlerSelectors.
                        basePackage("com.app.edit"))
                .paths(PathSelectors.ant("/v1/api/**"))
                .build()
                .apiInfo(apiInfo());
    }

    @Bean
    public Docket apiV2() {
        return new Docket(DocumentationType.SWAGGER_2)
                .useDefaultResponseMessages(false)  //false로 설정하면 Swagger에서 제공하는 기본 status 응답코드에 대한 기본 메세지 제거
                .groupName("groupName2") //Docket Bean이 둘 이상일 경우 충돌방지를 위해 설정
                .select() //ApiSelectorBuilder를 생성해 apis()와 paths()를 사용
                .apis(RequestHandlerSelectors.  //api스펙이 작성되어 있는 패키지 저장. 컨트롤러가 존재하는 패키지를 basePackage로 지정해 해당 패키지에 존재하는 API를 문서화
                        basePackage("com.test.swagger"))
                .paths(PathSelectors.ant("/v2/api/**")) //apis()로 선택된 API 중, 특정 path 조건에 맞는 API들을 다시 필터링
                .build()
                .apiInfo(apiInfo());//제목, 설명 등 문서에 대한 정보들 설정하기 위해 호출
    }

    private ApiInfo apiInfo() {
        return new ApiInfo(
                "Title",
                "Description",
                "version 1.0",
                "https://naver.com", 
                new Contact("Contact Me", "https://daum.net", "colt@colt.com"),
                "Edit Licenses",
                "https://naver.com",
                new ArrayList<>()
        );
    }
}
```

3. Controller
```java
@Api(value = "hello", tags = {"swagger", "v1", "api"})
@RequestMapping("/v1/api")
@RestController
public class SwaggerController {

    @ApiOperation(value = "this is test!", notes = "note!!!!!")
    @PostMapping("/test")
    public String test(@ApiParam(name = "first param", value = "first value", required = true) String input,
                       @ApiParam(name = "second param", value = "second value", required = false) String input2) {
        return "test";
    }
}
```

- @Api
   - 해당 클래스가 Swagger 리소스라는 것을 명시
- @ApiOperation
  - 한개의 Operation 선언
  - API에 대한 요약 및 자세한 설명
- @ApiParam
  - 파라미터에 대한 정보를 명시
  - name, value, required ... 

## 3. Spring Restdocs
### 1. Spring Restdocs?
- RESTful서비스를 문서화하는데 도움이 되는 오픈소스
- Asciidoctor에 Spring MVC Test로 자동 생성된 스니펫이 결합되어 만들어짐    
### 2. 장점 및 단점
- 장점 
  - 테스트를 강제함
  - 운영소스에 비 침투적임
- 단점
  - 테스트를 강제함
  - 적용, 사용 어렵고 러닝커브 높음
### 3. 사용 방법
1. 의존성 추가
- Asciidoctor 플러그인
- 스니펫이 생성될 위치
- 테스트 문서가 작성되기 전, 테스트가 실행되도록 작업을 종속적이게 만듦
- mockMvc를 사용 (WebFlux의 WebTestClient 또는 REST Assured 테스트를 통해 스니펫을 만들기 위해선 별도의 의존성 추가 필요)
- 생성된 문서를 프로젝트의 jar파일에 패키징 하도록 해서, 'static/docs' 경로에 복사
```groovy
plugins {
    id "org.asciidoctor.jvm.convert" version "3.3.2" 
}

configurations {
    asciidoctorExt
}

dependencies {
    asciidoctorExt 'org.springframework.restdocs:spring-restdocs-asciidoctor:{project-version}'
    testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc:{project-version}'
}

ext {
    snippetsDir = file('build/generated-snippets')
}

test {
    outputs.dir snippetsDir
}

asciidoctor {
    inputs.dir snippetsDir
    configurations 'asciidoctorExt'
    dependsOn test
}

bootJar {
    dependsOn asciidoctor
    from ("${asciidoctor.outputDir}/html5") {
        into 'static/docs'
    }
}
```

2. Config/BeforeEach
- Junit 5 기준
```java
public interface ApiDocumentUtils {

    static OperationRequestPreprocessor getDocumentRequest() {
        return preprocessRequest(
                modifyUris() // 문서상 uri 변경
                        .scheme("https")
                        .host("docs.api.com")
                        .removePort(),
                prettyPrint()); // 문서의 request 예쁘게 출력
    }

    static OperationResponsePreprocessor getDocumentResponse() {
        return preprocessResponse(prettyPrint()); // 문서의 response 예쁘게 출력
    }
}

```
3. Restful 서비스 호출
```java
@ExtendWith(RestDocumentationExtension.class)
@AutoConfigureRestDocs(uriScheme = "https", uriHost = "docs.api.com")
@WebMvcTest(PersonController.class)
public class JUnit5ExampleTests {

    private MockMvc mockMvc;

    @BeforeEach
    void setUp(WebApplicationContext webApplicationContext, RestDocumentationContextProvider restDocumentation) {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
                .apply(documentationConfiguration(restDocumentation))
                .build();
    
    void test() {
    ...
        /**
         * 서비스의 루트("/") 경로를 호출하고, application/json 응답이 필요함을 나타냄
         * 서비스가 예상한 응답을 생성했는지 확인함
         * 서비스 호출을 문서화하여, 이름이 지정된 디렉터리에 스니펫을 생성함.
         * 만들어 지는 종류는
         *  curl-request,http-request, http-response, httpie-request, request-body, response-body 
         */
        this.mockMvc.perform(get("/").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andDo(document("index"));
    }
}
```


-- 해당 응답 형식일 경우,아래와 같이 표현 가능 
```json
{
	"contact": {
		"name": "Jane Doe",
		"email": "jane.doe@example.com"
	}
}
```

```java

@Autowired
private MockMvc mockMvc;

@Autowired
private ObjectMapper objectMapper;

@MockBean
private PersonService personService;

void test2(){

    /**
     * personService..를 활용한 테스트 코드 작성
     */

    ResultAction resultAction = this.mockMvc.perform(get("/user/", 1L).accept(MediaType.APPLICATION_JSON));

    resultAction.andExpect(status().isOk())
            .andDo(document("index",
                    getDocumentRequest(),
                    getDocumentResponse(),
                    pathParameters(
                            parameterWithName("id").description("아이디")
                    ),
                    responseFields(
                            fieldWithPath("contact.email").description("The user's email address"), (2)
                            fieldWithPath("contact.name").description("The user's name")
                    )
            ));
                    
}

```
4. 만들어진 스니펫을 Asciidoc 파일에 포함
직접 만든 Asciidoc 파일에 아래의 코드를 포함해서 스니펫을 추가함
```
include::{snippets}/index/curl-request.adoc[]
```

## 4. 비교
### 1. 우리 서비스에 맞는 선택
1. 빠르고 쉬운 API문서 작성 VS 강제적인 테스트코드
2. 알록달록 예쁜 UI VS 비교적 딱딱한 UI 
### 2. 결론
> Spring REST Docs를 점진적으로 도입하기로 결정 
1. 테스트 코드 작성이 부담되나 회귀테스트의 필요성을 팀이 공감하고 있음
2. 운영 코드에 문서화를 위한 코드를 추가로 붙이는 것이 부담스러움
3. 가장 고민되는 것은 UI -> 딱딱한 html문서    
    => SpringRESTDocs에 Swagger를 넣을 수 있음(스니펫과 비슷한 yml코드가 만들어짐. 이것을 swagger-ui에서 열어보도록 함)

## 5. 참고
- https://tech.kakaoenterprise.com/127
- https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle/#introduction
- https://techblog.woowahan.com/2597/
- https://helloworld.kurly.com/blog/spring-rest-docs-guide/