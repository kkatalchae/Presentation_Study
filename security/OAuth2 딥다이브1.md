# OAuth2 딥다이브
# 목차
1. OAuth2 소개
    - OAuth2의 기본 개념 및 작동 방식 
2. OAuth가 나오기 전엔 어떻게 인증을 했을까?
    - '사용자 인증 및 권한 부여' 프로토콜의 타임라인
    - 결론
    - SAML
    - OAuth 1.0a 탄생
    - OAuth 2.0의 탄생
3. OAuth2가 중요한 이유
4. 그외 - OAuth 2.0만으로 인증할 수 있지 않아?
5. 대표 보안 취약점 - CSRF, Covert Redirect

# 1. OAuth2 소개
### OAuth2의 기본 개념 및 작동 방식 
사용자가 웹 애플리케이션(노션)에 자신의 인증 정보(구글 ID, 비밀번호)를 제공하지 않고도 제3자 서비스(구글 캘린더, 클라우드)의 리소스에 접근할 수 있도록 하는 인증 프로토콜

![OAuth2-1](https://github.com/kkatalchae/Presentation_Study/assets/67645131/2042a3b1-5dc9-40c0-92a0-a56136dddff7)

간단히 말해, 사용자가 인증을 수행하고 제3자 서비스(노션)은 사용자의 허가를 통해 사용자 계정(구글 계정)에 제한적으로 접근하도록 허용하는 시스템

![OAuth2-2](https://github.com/kkatalchae/Presentation_Study/assets/67645131/8efa504f-fb93-4501-8004-aef0e8a01da2)

**OAuth2의 작동흐름**

![OAuth2-3](https://github.com/kkatalchae/Presentation_Study/assets/67645131/702aad5f-960c-4294-8c4a-abeb72aa4b9a)

**Authorization Code Grant**
1. 인증 코드 발급
    1. client_id & redirect_uri 일치여부
    2. 해당 클라이언트가 요청한 스코프의 범위 동의 여부
    3. 인증 코드 발급
2. 엑세스 토큰 발급
    1. 인증 코드의 값과 클라이언트 ID, `클라이언트 시크릿`, 리다이렉트 uri 일치여부 확인
    2. 인증 코드 삭제 후 토큰 발급
3. 유저 정보 제공
    1. 토큰에 해당하는 유저 정보 확인
    2. 유저 정보 제공

**code를 받고 토큰을 또 받는 이유** (예상)
- 보안?  
    - ~~인증 단계 없이 토큰을 받으면 매번 클라이언트 시크릿 키를 담아서 요청해야 함.~~
    - 인증코드와 시크릿 키를 백엔드로 전달하지 못하고 url로 담아서 보내야 함(보안이슈)
- 편의성? 
    - 토큰 발급 후 번거롭게 인증작업을 거칠 필요 없이 바로 유저 정보를 얻을 수 있도록?

[출처](https://blog.naver.com/mds_datasecurity/222182943542)
- 4가지 인증방식
1. Authorization Code Grant (권한 부여 승인 코드 방식)
    - 가장 많이 사용되는 방식
    - refresh token의 사용이 가능한 방식
    - 권한 부여 승인 요청 시 response_type을 code로 지정하여 요청
2. Implicit Grant (암묵적 승인 방식)
    - 자격증명을 안전하게 저장하기 힘든 클라이언트(javascript 등 스크립트 언어를 사용한 브라우저)에 최적화된 방식
    - 권한 부여 승인 코드 없이 바로 토큰이 발급됨. 
    - 토큰이 바로 전달되므로 만료기간을 짧게 설정하여 누출의 위험을 줄임
    - refresh token 사용이 불가능.
    - 이 방식에서 권한 서버는 client_secret를 사용해 클라이언트를 인증하지 않음.
    - 토큰이 url로 전달된다는 단점이 있음. 
3. Resource Owner Password Credentials Grant (자원 소유자 자격증명 승인 방식)
    - username, password로 Access Token을 받는 방식
    - 클라이언트가 타사의 외부 프로그램일 경우에는 적용하면 안됨
    - 자신의 서비스에서 제공하는 어플리케이션일 경우에만 사용되는 인증 방식 
    - 권한 서버, 리소스 서버, 클라이언트가 모두 같은 시스템에 속해 있을 때 사용되어야 하는 방식
    - Refresh Token의 사용도 가능
4. Client Credentials Grant (클라이언트 자격증명 승인 방식)
    - OAuth2의 권한 부여 방식 중 가장 간단한 방식
    - 클라이언트 자신이 관리하는 리소스 혹은 권한 서버에 해당 클라이언트를 위한 제한된 리소스 접근 권한이 설정되어 있는 경우 사용
    - Refresh Token은 사용할 수 없음



# 2. OAuth가 나오기 전엔 어떻게 인증을 했을까?

### '사용자 인증 및 권한 부여' 프로토콜의 타임라인

- SSO 구현하기 
- 웹 브라우저 통합 인증

![OAuth2-4](https://github.com/kkatalchae/Presentation_Study/assets/67645131/999bad41-45ba-4e4d-822f-94d30da39752)

### 결론

|기업|OAuth 2.0 | OIDC|
|---|---|---|
|구글|o|o|
|깃허브|o|x
|네이버|o|x
|카카북|o|o(optional)
|페이스북|o|o

<img width="869" alt="OAuth2-8" src="https://github.com/kkatalchae/Presentation_Study/assets/67645131/5d91ce45-4fe6-4048-9883-9b5c69ecf36b">

- 호텔 카드키  
  - 키 카드 소유자가 **어떤 객실에 출입할 권한**이 있는지 알 수 있지만, 카드키 **소유자에 대한 신원 정보**는 알 수 없음.
- 주민등록증 
  - 신원 확인(이름, 사진, 거주지 주소 등)을 확인할 수 있겠지만, 호텔 객실에는 출입할 수 없음

[출처1](https://www.itworld.co.kr/news/108736#csidx4eb8af0a32319858acfacca4286354e)
[출처2](https://saml.xml.org/google-ntt-and-us-gsa-deploy-saml-2-0-digital-identity-management)
- SAML은 SSO를 위해 **엔터프라이즈 네트워크** 내에서 배포된다. 
- 2008년 1월까지 SAML 2.0 배포는 전 세계 **정부, 고등 교육 기관 및 상업 기업**에서 일반화 됨
- 미주, 아시아, 호주 및 유럽의 정부 기관에서 SAML 2.0 기반 ID 애플리케이션을 구축 및 배포하면서 SAML 2.0은 글로벌 전자 정부 및 공공 부문에서 선택되는 표준이 됨
- 오쓰는 구글과 트위터에서 인터넷 규모로 설계됐다

### OpenID Connect

- SaaS 회사들, Microsoft, Google, Salesforce 및 기타 많은 회사들이 OIDC를 연합 신원 및 액세스 관리의 표준으로 지원
- 특히 웹 및 모바일 애플리케이션의 맥락에서 OIDC는 현대적인 인증 및 권한 부여의 사실상 표준으로 자리 잡음
- OAuth 2.0 프레임워크 위에 구축된 인증 프로토콜
- JSON Web Token (JWT)을 사용하여 신원 제공자와 신뢰하는 파티 간의 정보를 전송합니다. JWT는 디지털로 서명되고 암호화되어 데이터의 안전성과 위변조 방지를 보장
- SAML과 마찬가지로 OIDC는 단일 로그인(Single Sign-On, SSO) 인증을 가능하게 하지만, 일부 중요한 차이점과 장점이 있습니다.
- OIDC는 OAuth 2.0 프로토콜 위에 구축되며, 사용자 프로필 정보 및 네이티브 모바일 애플리케이션 지원과 같은 인증에 대한 추가 기능을 제공합니다. 또한 XML 기반의 SAML 토큰 대신 JSON 웹 토큰(JWT)을 사용하여 현대적인 웹 및 모바일 애플리케이션과의 작업을 용이하게 합니다.

<details>
<summary>샘플 SAML 응답</summary>
<div markdown="1">
<samlp:Response xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_8e8dc5f69a98cc4c1ff3427e5ce34606fd672f91e6" Version=" 2.0" IssueInstant="2014-07-17T01:01:48Z" Destination="https://sp.example.com/demo1/index.php?acs" InResponseTo="IDP_4fee3b046395c4e751011e97f8900b5273d56685">

 <saml:Issuer>https://idp.example.com/metadata.php</saml:Issuer>

 <샘플:상태>

<samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>

 </samlp:상태>

 <saml:Assertion xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xs="https://www.w3.org/2001/XMLSchema" ID="_d71a3a8e9fcc45c9e9d248ef7049393fc8f04e5f75" 버전 ="2.0" IssueInstant="2014-07-17T01:01:48Z">

<saml:Issuer>https://idp.example.com/metadata.php</saml:Issuer>

<saml:제목>

<saml:NameID SPNameQualifier="https://sp.example.com/demo1/metadata.php" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">_ce3d2948b4cf20146dee0a0b3dd6f69b6cf86f62d7</saml: 이름ID>

<saml:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">

<saml:SubjectConfirmationData NotOnOrAfter="2024-01-18T06:21:48Z" Recipient="https://sp.example.com/demo1/index.php?acs" InResponseTo="IDP_4fee3b046395c4e751011e97f8900b5273d56685"/>

</saml:SubjectConfirmation>

</saml:제목>

<saml:조건 NotBefore="2014-07-17T01:01:18Z" NotOnOrAfter="2024-01-18T06:21:48Z">

<saml:대상 제한>

<saml:Audience>https://sp.example.com/demo1/metadata.php</saml:Audience>

</saml:AudienceRestriction>

</saml:조건>

<saml:AuthnStatement AuthnInstant="2014-07-17T01:01:48Z" SessionNotOnOrAfter="2024-07-17T09:01:48Z " SessionIndex="_be9967abd904ddcae3c0eb4189adbe3f71e327cf93">

<saml:AuthnContext>

<saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes: 비밀번호</saml:AuthnContextClassRef>

</saml:AuthnContext>

</saml:AuthnStatement>

<saml:속성문>

<saml:속성 이름="uid" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">

<saml:AttributeValue xsi:type="xs:string">테스트</saml:AttributeValue>
</div>
</details>

<details>
<summary>샘플 OIDC 응답</summary>
<div markdown="1">

<img width="674" alt="OAuth2-10" src="https://github.com/kkatalchae/Presentation_Study/assets/67645131/54a07724-8d7b-4577-8899-302ff007316b">

</div>
</details>

### SAML

- 2006년까지 SAML 이외의 다른 인증 프로토콜은 OpenID 1.0 출시 전까지 크게 주목받지 못함.
- **SAML이 기술하는 가장 중요한 요구사항은 웹 브라우저 통합 인증(SSO).**
- 직원들이 회사 인트라넷, Microsoft Office 등 여러 시스템에 하나의 로그인으로 접근할 수 있도록 함. 
- **SAML 인증이 완료되면 기업 인트라넷, Microsoft Office, 브라우저 등 모든 툴에 액세스할 수 있다.**
- SAML을 통해 한 번의 디지털 서명으로 자격 증명을 다시 입력할 필요 없이 추가 서비스에 액세스 할 수 있음
- 국민연금 홈페이지에서 로그인 후 연금 정보를 확인하거나, 세무서 홈페이지에서 로그인 후 소득세 신고를 하는 경우에 사용

**예시 - Postman**

- 이 접근 방식을 사용하면 로그인 페이지에 `Login with SAML SSO` 버튼을 추가할 수 있음. 
- Postman은 이 접근 방식을 사용하여 기업 사용자를 위한 SAML SSO를 활성화.

![OAuth2-5](https://github.com/kkatalchae/Presentation_Study/assets/67645131/dc722231-d752-4f25-afc3-59417446fdd0)

- **SAML**은 전체 시간 동안 존재, 특히 교육 영역을 지배했지만,
- 업계는 점차 장황한 XML 형식에서 일반적인 형식인 JSON을 선호. 이로 인해 **OpenIDConnect** 1.0의 기반이 마련됨.

### openID
**OpenID**는 이미 일종의 죽은 프로토콜이고 거의 모든 업계 거대 기업이 2018년경에 이를 포기.

> OpenID란
> 
> - OpenID 스펙만 준수한다면 누구나 OpenID로 인증 가능
> - 최초 한번 OpenID 가입 후 인터넷을 이용하면서 OpenID를 지원하는 사이트를 만나는 경우 더 이상 회원가입하지 않고 기존에 만들어놓은 OpenID로 로그인
> - OpenID는 브라우저 기반의 제한 없이 분산형 신원 확인을 지원하였으며, 당시 여러 업계 거물들이 이 프로토콜에 기여하고 채택하면서 많은 관심을 얻음.
> - IDP에서 시작된 SAML 흐름을 SP에서 시작되도록 확장

**OAuth 2.0** 프로토콜의 인기로 인해 OpenID 프로토콜을 OAuth 정의 확장으로 확장하려는 시도가 두 번 있었지만 OAuth 2.0 프로토콜 위에 인증 프로토콜을 정의하는 다른 방법만 성공.

### OAuth 1.0a 탄생

2006년 11월 트위터는 [OpenID](https://ko.wikipedia.org/wiki/%EC%98%A4%ED%94%88%EC%95%84%EC%9D%B4%EB%94%94) 를 탑재하는 작업을 하고 있었고, 같은 시기에 한 소셜 북마크 사이트에서도 OpenID를 사용한 인증 방법을 필요로 하고 있었음.

당시에는 액세스 위임에 대한 공개 표준이 없었고, 각 회사의 4명의 웹 개발자가 만나 OpenID를 활용한 API로 인증 및 권한 부여를 동시에 제공하는 인증 프로토콜을 만든 것이 `OAuth 1.0`

구글과 트위터에서 공동으로 개발한 SAML보다 다소 새로운 표준

- 2007년 10월에 OAuth core 1.0 이 비공식 발표
- **세션 고정 공격** 보안 결함이 발견되어서 이 문제가 개선된 `OAuth 1.0a`가 2009년 6월에 발표
- 2010년 4월에 [“The OAuth 1.0 Protocol”이라는 이름으로 RFC 5849](https://tools.ietf.org/html/rfc5849) 문서 번호를 부여받음(RFC 표준 이름에는 1.0뒤에 *“a”*가 붙지 않지만 좀 더 명확히 하기 위해 `OAuth 1.0a`라고 불리고 있음)

**단점**

- 모바일에서 사용하기 어움
- Signature 개발하고 관리하는데 시간이 너무 오래 걸림,
- 기능은 단순화하고 규모는 확장하고 싶은데 너무 복잡
- 보안을 보장하기 위해 디지털 서명을 사용
    - 이러한 암호화 요구 사항으로 인해 개발자들 사이에서 인기가 떨어짐.

### OAuth 2.0의 탄생
2012년 10월에 이런 문제점을 보완한 OAuth 2.0 발표하였고, 크게 달라진 점은 아래와 같다. 

- 디지털 서명이 아닌 **HTTPS** 사용
    - OAuth 2.0은 **단기 액세스 토큰**을 사용하고 복잡한 암호화 개입을 요구하지 않고 **HTTPS에 의존**하여 이러한 장벽을 극복하도록 설계됨. OAuth 2.0은 OAuth 1.0과 호환되지 않지만 업계에서 많은 사랑을 받됨며 널리 사용되고 있음. 
    - `OAuth 1.0a`에서는 HTTPS가 필수가 아니었기 때문에 API를 호출할 때 Signature를 생성해서 호출해야했지만 `OAuth 2.0`는 암호화를 HTTPS에 맡김으로써 개발자의 수고를 덜어줌.
- Access Token의 만료 기간이 생김.
- 더 많은 인증 방법(Grant types)을 지원 → Implicit Grant 를 사용해 모바일에서도 쉽게 사용 가능.  
- 다양한 인증 방식을 통해 웹/모바일 등 다양한 시나리오에 대해 고려됨.

# 3. OAuth2가 중요한 이유
- **간편한 SSO서비스 구현 가능**
    - 개발자는 사용자 **인증 및 권한 부여 기능**을 직접 구현하지 않고도 OAuth2를 통해 간편하게 사용할 수 있습니다.
- **보안 관련 개발 편의성 향상**
    - 보안을 디지털 서명이 아닌 HTTPS로 보장하여 개발자가 직접 구현하지 않고도 간편하게 보안을 유지할 수 있음.
        - **OAuth2 인증 요청 및 응답:** OAuth2 인증 요청 및 응답은 HTTPS를 통해 전송되어 개인정보 유출을 방지합니다.
        - **액세스 토큰 발급 및 사용:** OAuth2 액세스 토큰은 HTTPS를 통해 발급되고 사용되어 위조를 방지합니다.
        - **사용자 프로필 정보 공유:** 사용자 프로필 정보는 HTTPS를 통해 공유되어 개인정보 유출을 방지합니다.
- **다양한 서비스 연동 가능**
    - OAuth2는 다양한 웹 서비스와 호환되기 때문에, 여러 서비스를 하나의 애플리케이션에서 통합하여 사용할 수 있습니다.



# 4. 그외 - OAuth 2.0만으로 인증할 수 있지 않아?
OAuth 2.0은 사용자의 리소스를 리소스 서버로부터 가져오기 위해 사용한다. 이 과정에서 인증 서버를 통해 사용자를 인증하는 과정을 거친다. 사용자의 신원 정보가 필요하면, OAuth 2.0 인가 프로세스를 거쳐 발급된 액세스 토큰을 가지고 사용자 정보 리소스에 접근하면 될 것 이다.

물론 OIDC없이 사용자 프로필 정보를 리소스 서버로부터 가져올 수 있다. 하지만, OIDC 없이 OAuth 2.0 단독으로 사용자 리소스를 가지고 오기 위해서는 OIDC를 사용할 때의 2배의 통신을 해야한다.

OAuth 2.0을 단독으로 사용하면, Access Token을 발급 받은 다음 이 토큰을 사용하여 사용자 리소스를 다시 요청하여 받아와야한다. 하지만 OIDC를 사용하면 Access Token과 함께 전달받은 ID Token을 복호화하여 사용자 정보를 가져오면 끝이다.

OAuth 2.0 단독으로 사용하여 사용자 정보를 조회할 때 발생한 통신이 1억회라면, OIDC를 사용하면 5천만회로 줄어드는 것이다.

[출처](https://meetup.nhncloud.com/posts/105)

# 5. 대표 보안 취약점 - CSRF, Covert Redirect
### CSRF 공격을 통한 계정 탈취

SNS 계정 연동할 수 있는 서비스는 [그림 2]와 같이 하나의 서비스에 여러 개의 SNS 계정을 연결할 수 있습니다.

계정을 연동하기 위해서는 OAuth 인증 과정을 통하는데, 이 때 CSRF 공격으로 피해자 계정에 공격자 계정을 연동할 수 있습니다.

**SNS 계정 연동 FLOW**

```
1. 기존 계정과 SNS 계정 연동 요청
2. 요청 SNS 로그인 페이지 출력 (Client ID 값이 포함된 로그인 페이지)
3. ID/PW 를 통해 SNS 계정에 로그인
4. 로그인 성공 시 인증 서버로부터 Authorization code를 발급 받음 (Authentication Server -> 사용자)
5. 발급 받은 code 값과 **state** 값을 Client 서버로 전송 (사용자 -> Client Server)
6. code 값과 **state** 값 검증 후 Client 서버에 로그인 되어있는 계정과 SNS 계정이 연동됨
```

여기서 `state` 값은 CSRF token 역할을 하는데, 만약 state 값에 대해 검증이 누락되어 있거나 미흡할 경우 사용자 계정을 탈취할 수 있습니다. 5번 과정에서 Client 서버로 전송하는 내용을 추출하여 만든 CSRF 공격 페이지에 사용자(피해자)가 접근하면, 사용자(피해자) 계정과 공격자 계정이 연동되며, 공격자의 SNS 계정을 통해 사용자(피해자) 계정으로 로그인을 할 수 있습니다.

### Covert Redirect

OAuth 2.0 인증 Flow 중 **`redirect_uri` 파라미터 값에 대해 검증이 누락되거나 미흡할 경우 발생하는 취약점**입니다.

정상적인 경로라면 사용자(Resource Owner)가 로그인 성공 후 발급받은 Authorization code를 Client로 전달해야합니다. 그러나 [그림 3]과 같은 방법으로 공격자는 사용자(피해자)에게 변조된 Redirect URI를 보내 로그인을 유도합니다. 4번 단계에서 사용자(피해자)가 Redirect URI 값이 변조된 URL로 로그인할 경우, Authorization code 값이 공격자 서버로 전달되어 공격자는 사용자(피해자)의 계정을 탈취할 수 있습니다.

![OAuth2-7](https://github.com/kkatalchae/Presentation_Study/assets/67645131/557e7ecb-c27e-4bad-9654-d2576a40b8df)

대응 방안은 Authorization Server 에서 Redirect URI 값에 대해 Full Path 검증을 진행해야합니다.

만일 도메인까지만 검증하거나 일부 경로까지만 검증하도록 조치할 경우, Redirect 취약점이 존재하는 Client는 여전히 Authorization code를 탈취할 수 있습니다.

### 예시)

```
http://client.co.kr/OAuth/Callback?code=XXXXXXXXXXXXXXXXXXXX 에서 도메인까지만 검증할 경우
http://client.co.kr/logout?returnURL=http%3a%2f%2fattacker.NulLBr4in.com%2fcode%2f 로 변조하게되면
도메인이 동일하여 검증을 우회하고 공격자 서버로 code를 전송합니다.

```

**위 예시처럼 도메인까지만 검증할 경우, code 값에 Query string이 포함되서 공격자 서버로 전달되거나 HTTP 헤더의 Referer를 참고하여, Authorization code 값을 탈취할 수 있습니다.**