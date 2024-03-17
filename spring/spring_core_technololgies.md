![스프링 로고](../images/스프링_로고.png)
> ### Spring Framework
> 자바 엔터프라이즈 애플리케이션 개발에 사용되는 오픈소스 경량급 애플리케이션 프레임워크   
<br>

### 스프링이 탄생하기 까지..

`EJB(Enterprise Java Bean)` ❄️   

대규모 분산 객체 시스템   
`분산 컴퓨팅` : 하나의 컴퓨터에 존재하는 애플리케이션이나 프로세스에서 혼자 수행하기 어려운 작업을 다중 프로세서나 컴퓨터에 분산 시키는 것   
`분산 객체` : 분산 컴퓨팅 기술이 객체 지향과 접목되어 하나의 프로세서나 컴퓨터에서 실행되는 객체가 다른 프로세서나 컴퓨터에서 객체와 통신이 가능하도록 하는 기술   
<br>

Spring Core Technologies
=

![스프링 코어](../images/스프링_코어.png)   


## Spring Core Container

#### IoC (Inversion of Control, 제어 역전)
> 프로그래머가 작성한 프로그램이 프레임워크의 제어를 받게 되는 소프트웨어 디자인 패턴

#### DI (Dependency Injection, 의존성 주입)
> 의존성을 외부에서(주로 IoC Container) 주입받는 것

#### IoC Container
> 객체의 생성, 의존성을 관리하는 컨테이너

#### Bean
> Spring IoC Container가 관리하는 자바 객체
> 스프링이 제어권을 가지고 직접 생성하며 관계를 부여하는 오브젝트

#### Bean Factory
> 빈을 생성하고 관계를 설정하는 등의 제어를 담당하는 IoC Object

#### Application Context
> 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC 엔진, 일종의 빈 팩토리
> ApplicationContext 인터페이스는 BeanFactory 인터페이스를 상속함

|              | 모듈/아티팩트                                | 사용                                                     |
|----------------|-----------------------------------|----------------------------------------------------|
| Core | spring-core | 다른 스프링 모듈이 사용하는 유틸리티 |
| Beans | spring-beans | 스프링 빈 지원, 의존성 주입을 제공한다. BeanFactory의 구현을 포함한다.|
| Context | spring-context | 빈 팩토리를 상속하는 애플리케이션 컨텍스트를 구현하고 리소스 로드 및 국제화 지원을 제공한다. |
| SpEL | spring-expresstion | EL(Expresstion Language, 표현 언어)을 확장하고 빈 속성 및 접근, 처리를 위한 언어를 제공한다. |

<br>   

***

## 횡단 관심 (Cross-cutting Concerns)
![스프링 관심사](../images/스프링_AOP(1).png)   
- 애플리케이션 전반에 걸쳐 공통적으로 사용되는 기능들에 대한 관심사 => `공통 관심 사항(Cross-cutting concern)`이라 부르며,   
- 애플리케이션의 주목적을 달성하기 위한 핵심 로직에 대한 관심사를 `핵심 관심 사항(Core concern)`이라 부른다.
#### AOP(Aspect Oriented Programming)   
> 관점 지향 프로그래밍. 어떤 로직을 기준으로 핵심적인 관점, 공통적인 관점(부가적인 관점)으로 나누어서 보고 그 관점을 기준으로 모듈화하겠다는 의미이다.

![스프링 AOP](../images/스프링_AOP(2).png)  

|              | 모듈/아티팩트                                | 사용                                                     |
|----------------|-----------------------------------|----------------------------------------------------|
| AOP | spring-aop | AOP(Aspect Oriented Programming, 관점지향 프로그래밍)에 대한 기본적인 기능을 제공한다. |
| Aspect | spring-aspects | 인기있는 AOP 프레임워크인 AspectJ와의 통합을 제공한다.|
| Instrument | spring-instrument | 기본적인 instrumentation을 제공한다. (Byte Code Instrumentation: 런타임이나 로딩 때 클래스의 바이트코드를 변경하는 것) |
| Test | spring-test | 단위 및 통합 테스트에 대한 기본적인 기능을 제공한다. |

<br>   

***

## WEB
> 스프링은 자체 MVC 프레임워크인`Spring MVC`를 제공한다.

|              | 모듈/아티팩트                                | 사용                                                     |
|----------------|-----------------------------------|----------------------------------------------------|
| Web | spring-web | 멀티파트 파일 업로드와 같은 기본 웹 기능을 제공한다. 다른 웹 프레임워크와의 통합을 제공한다. |
| Servlet | spring-webmvc | 자체 MVC 프레임워크를 제공한다. Spring MVC, REST 웹 서비스를 구현을 포함한다.|
| WebSocket | spring-websocket | 웹 소켓을 지원한다. |
| Portlet | spring-webmvc-portlet | 포틀릿 환경에서 사용할 MVC 구현을 제공한다. |

<br>   

***

## Business
> 비지니스 레이어는 애플리케이션의 `비즈니스 로직을 실행`하는 데 초점을 맞춘다.
> 스프링에서는 비즈니스 로직을 `POJO (Plain Old Java Object)`로 구현한다.

- POJO : 순수한 자바 객체.
- 	다시 말해, 어떤 클래스를 확장(extends)하거나, 인터페이스를 구현(implements)하거나, 어노테이션(Annotation)을 포함할 필요가 없는 객체이다.

|              | 모듈/아티팩트                                | 사용                                                     |
|----------------|-----------------------------------|----------------------------------------------------|
| Transaction | spring-tx | 선언적 트랜잭션 관리를 제공한다. |

<br>   

***

## Data
> 데이터 레이어는 일반적으로 데이터베이스 및 외부 인터페이스와 상호작용한다.

|              | 모듈/아티팩트                                | 사용                                                     |
|----------------|-----------------------------------|----------------------------------------------------|
| JDBC (Java Database Connectivity) | spring-jdbc | JDBC 추상화를 제공한다. |
| ORM (Object Relational Mapping) | spring-orm | JPA (Java Persistence API), JDO (Java Data Objects), Hibernate와 같은 ORM API를 위한 통합레이어를 제공한다. |
| OXM (Object XML Mapping) | spring-oxm | JAXB, Castor, XMLBeans, JiBX, XStream과 같은 Object/XML 매핑을 지원한다. |
| Messaging | spring-messaging | 메시지 기반 애플리케이션을 작성할 수 있는 기능을 제공한다. |
| JMS (Java Message Service) | spring-jms | 메시지 생산(Producing)과 소비(Consuming)를 위한 기능을 제공한다. Spring Framework 4.1 부터는 spring-messaging과의 통합을 제공한다. |

<br>   

* * *
#### Reference
[스프링의 정의와 특징](https://memodayoungee.tistory.com/102#%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98%20%ED%8A%B9%EC%A7%95(%ED%8A%B8%EB%9D%BC%EC%9D%B4%EC%95%B5%EA%B8%80)%C2%A0-1)   
[Spring Boot](https://velog.io/@yaaloo/Spring-Spring-Boot)  
[Spring Core Technologies 1. IoC Container](https://blog.leaphop.co.kr/blogs/44)   
[Cross-cutting concerns for an Enterprise Application](https://medium.com/techmonks/cross-cutting-concerns-for-an-enterprise-application-ef9884e6bed3)   
[핵심기능과 부가기능](https://codedragon.tistory.com/m/7912?category=81876)

