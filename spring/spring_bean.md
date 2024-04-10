## 핵심 개념
#### IoC
- Inversion of Control (제어의 역전)
- **프로그램에서 모듈간의 의존성을 관리하기 위한 설계 원칙**
- 개발자는 프레임워크에 필요한 부품만 개발하며 코드의 호출은 개발자가 제어하는 것이 아닌 프레임워크의 내부에서 결정된 대로 이뤄진다. 즉, 객체의 생명 주기를 프레임워크에서 관리한다.

#### DI
- Dependency Injection (의존성 주입)
- **IoC 패턴의 구현 방법 중 하나**
- 스프링에서만 사용되는 것은 아니다.
- 의존성이 있는 객체를 개발자가 직접 생성하거나 참조하지 않고 컨테이너가 제공하는 DI 기능을 사용해 필요한 객체를 주입받을 수 있다.
- 스프링에서 빈으로 정의된 객체를 @Autowired로 선언하면 자동으로 이를 주입해줘서 별다른 작업없이 바로 사용할 수 있는 것이 DI를 이용하는 예시이다.
- DI 컨테이너에는 BeanFactory, ApplicationContext, Guice, Dagger 등이 있다.

#### Spring Framework의 IoC
- BeanFactory와 ApplicationContext 인터페이스를 이용해 IoC를 구현한다. 이들은 DI 컨테이너라고 불린다.
- BeanFactory는 가장 기본적인 IoC 컨테이너이며, ApplicationContext는 BeanFactory를 상속받아 추가적인 기능들을 제공하는 IoC 컨테이너이다.
- BeanFactory가 프레임워크의 구성과 기본 기능을 제공한다고 하면, ApplicationContext는 용도에 따른 추가적인 기능을 제공한다.
  - ApplicationContext가 제공하는 것들
    - Spring의 AOP 기능과의 통합
    - 메시지 리소스 처리 (for use in internationalization)
    - 이벤트 발행
    - 웹 애플리케이션을 위한 WebApplicationContext와 같은 응용 프로그램 레이어별 Context에서 사용
   
#### Bean
- **Spring IoC 컨테이너에 의해 인스턴스화, 조립, 관리되는 객체**
- Bean으로 선언되지 않은 객체는 Spring IoC 컨테이너가 관리하지 않는다.
  - Bean이 아닌 Dog Class가 존재한다고 할 때, `Dog myDog = new Dog();` 으로 선언된 myDog의 LifyCycle은 개발자가 작성한 코드에 의해 결정된다.
- Bean들 간의 의존성을 컨테이너에서 사용되는 configuration metadata에 반영된다.

## Container
Spring Core 컨테이너는 스프링 프레임워크의 핵심이며, Bean / Core / Context 의 모듈로 나누어져 있고 이는 스프링 프레임워크에서 IoC Container를 구성하는데 사용하는 모듈이다.

#### ApplicationContext
애플리케이션의 설정 정보를 제공하는 인터페이스로써 아래와 같이 정의되어 있다.
```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
    @Nullable
    String getId();

    String getApplicationName();

    String getDisplayName();

    long getStartupDate();

    @Nullable
    ApplicationContext getParent();

    AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException;
}
```
ApplicationContext는 몇가지 interface를 상속함으로써 여러 기능을 제공한다.
- `ListableBean` : 애플리케이션 컴포넌트에 액세싱하는 빈 팩토리를 제공하는 메서드
- `ResourceLoader` : 파일 리소스 로드 기능 (`ResourcePatternResolver`가 `ResourceLoader`를 상속받는다.)
- `ApplicationEventPublish` : 등록된 리스너에 이벤트 발행 기능
- `MessageSource` : 다국어 지원

상위(부모) 컨텍스트를 상속하거나 자식 컨텍스트에서 정의된 내용이 항상 우선시된다. 즉, 어떤 상위 컨텍스트가 전체 웹 애플리케이션에서 사용되어도 각 서블릿마다 독립적인 자식 컨텍스트를 가질 수 있다.

사실상 ApplicationContext에서 가장 주목해야할 핵심 기능은 BeanFactory를 통한 Bean의 인스턴스화이다.
BeanFactory는 스프링 프레임워크의 IoC/DI 기능을 구현하는 인터페이스로, 빈 객체를 생성하고 관리하는 기능을 제공한다. ApplicationContext는 이러한 BeanFactory 인터페이스를 상속하여 구현되었기 때문에, 빈 객체의 인스턴스화와 관리를 위해 BeanFactory의 모든 기능을 사용할 수 있다.

그렇다면 Bean을 인스턴스화한다. 라는 것이 무슨 의미일까?
이는 스프링 컨테이너가 설정 파일에 등록된 빈 정보를 바탕으로 빈을 생성하고 런타임 시점에서 빈 객체의 참조를 관리할 수 있도록 스프링 내부에서 관리하는 것을 의미한다.

빈 정보는 XML, Java 어노테이션, Java 코드로 작성되며 애플리케이션을 구성하는 객체와 객체들 간의 상호 종속성을 포함한다. 작성한 애플리케이션 내의 클래스들은 이러한 설정 메타데이터를 기반으로 Spring Container에 의해 인스턴스화 되는 것이다.

#### Configuration Metadata
객체의 생명 주기와 런타임 내 주입 시기에 대해서는 스프링에 작업을 위임하나 객체를 구성하는 데에 있어서는 개발자가 지시를 내려줘야한다. 간단하게 XML, Java Annotation, Java 기반의 Configuration 작성 방법을 알아보자.

###### 1) XML-based configuration
```xml
<bean id="myComponent" class="com.example.MyComponent">
  <property name="myService" ref="myService"/>
</bean>
<bena id="myService" class="com.example/MyService"/>
```
- 가장 오래전부터 사용해온 방식이다.
- 어노테이션 기반 설정이 각 클래스 파일 내부에 있어 관리가 어려운 것에 비해, 루트 디렉터리에 분류에 따라 여러 파일로 구성이 가능하여 프로젝트 관리가 쉬운 이점이 있다.
- 반면에 구성 정보를 검증하기 어렵고 휴먼 에러가 발생할 가능성이 높다. XML 구성 파일을 읽고 파싱하는데 오버헤드가 발생할 수 있다.

###### 2) Annotation-based configuration
```java
@Component
public class MyComponent {
  //..
}
```
- 스프링이 제공하는 `@Component`, `@Service`, `@Repository` 등과 같은 어노테이션을 이용해 설정을 정의한다.
- 코드의 양을 줄이고 직관적인 코드로 작성할 수 있다.

###### 3) Java-based configuration
```java
public class MyComponent {
  //..
}

@Configuration
public class MyConfig {
  @Bean
  public MyComponent myComponent() {
    return new MyComponent();
  }
}
```
- `@Configuration` 어노테이션을 사용하여 Configuration으로 사용할 클래스를 지정하고 내부에서 `@Bean` 어노테이션을 사용해 빈으로 등록할 클래스를 지정한다.

###### XML-based configuration vs. Annotation-based configuration vs. Java-based configuration
| | 장점 | 단점 |
|:--:|:--:|:--:|
| XML-based | 설정 정보를 파일로 관리하기 때문에, 수정과 관리가 쉽다. | XML 파일을 작성하는 것이 번거롭고, 오타 등의 실수가 발생할 수 있다. |
| | 스프링 컨테이너가 XML 파일을 읽어들이는 시간이 빠르다. | XML 파일의 크기가 커지면 가독성이 떨어지고 유지보수가 어렵다. |
| | 설정 정보를 한 눈에 파악할 수 있다. | |
| Annotation-based | 어노테이션을 사용하기 때문에 XML 파일보다 설정 정보가 간결하고 가독성이 좋다. | 설정 정보가 흩어져있어 XML 파일보다 관리가 어렵다. |
| | IDE 등의 도구를 이용하여 코드 작성이 용이하다. | 어노테이션을 이용하여 설정 정보를 작성하면 코드가 복잡해질 수 있다. |
| | 컴파일 과정에서 오류를 확인할 수 있어 안정적이다. | |
| Java-based | 설정 정보를 자바 파일로 관리하기 때문에, 가독성이 좋고 수정 및 관리가 용이하다. | Annotation 기반 설정보다 코드가 길어지기 때문에, 보다 복잡한 설정을 다루기 어렵다. | 
| | XML 파일에 비해 설정 정보의 양이 적어서 가벼운 애플리케이션에서 빠른 초기화 속도를 보장한다. | XML 파일과 Annotation 방식보다 학습 곡선이 높아 사용하기 어렵다. |
| | 자바 코드에 대한 컴파일 타임 체크가 가능하여, 런타임에서 발생하는 예외를 사전에 방지할 수 있다. | |

빈 객체를 정의하기 위한 메타데이터를 제공했으니 ApplicationContext는 이를 바탕으로 빈을 만들어 낼 것이다.

이 때 ApplicationContext가 빈을 생성할 때 위의 medatada를 직독직해하는 것이 아니고 BeanDefinition을 사용한다.

#### BeanDefinition
`BeanDefinition`은 스프링 컨테이너가 빈 객체를 생성하고, 관리하기 위해 필요한 정보를 담고 있는 객체이다. 빈 객체의 클래스 정보, 생성자 인자, 프로퍼티 값, 스코프 등의 정보가 포함되며 BeanDefinition은 XML 파일이나 자바 코드에서 설정 정보를 작성하여 생성된다. 즉 우리가 작성한 Bean Configuration metadata 내 정보에 기반하여 객체인 BeanDefinitation을 새로 생성하는 것이다.

Configuration metadata는 BeanDefinitation을 만드는 데 사용되는 일종의 템플릿 역할을 한다고 말할 수 있다. 결론적으로 ApplicationCotnext는 BeanDefinition을 중간 매개체로 사용하여 Configuration metadata에 정의된 대로 빈을 생성하고 관리한다.

###### BeanDenfinition이 포함하는 정보
- 빈으로 정의한 클래스 이름
- 컨테이너 내에서의 빈의 동작 방식 (scope, lifecycle callback)
- 다른 빈에 대한 종속성
- 새로 생성된 객체에 사용될 다른 configuration 설정들

## Bean 인스턴스화
> Bean 인스턴스화란 스프링 컨테이너가 설정 파일에 등록된 빈 정보를 바탕으로 빈을 생성하고 런타임 시점에서 빈 객체의 참조를 관리할 수 있도록 스프링 내부에서 관리하는 것을 의미한다.
스프링에서 빈을 인스턴스화 하는 방법을 알아보자.

1. 생성자(Constructor)를 이용한 방법
   - 스프링 컨테이너가 빈을 생성할 때, 해당 빈 객체의 생성자를 호출하여 인스턴스를 생성한다.
   - 대부분의 빈 객체는 생성자를 이용하여 인스턴스화된다.
2. 정적 팩토리 메서드를 이용한 방법
   - 스프링 컨테이너가 빈을 생성할 때, 해당 빈 객체의 정적 팩토리 메서드를 호출하여 인스턴스를 생성한다.
   - 일반적으로 외부 라이브러리나 다른 프레임워크에서 제공하는 객체를 스프링 빈으로 등록할 때 많이 사용된다.
3.  인스턴스 팩토리 메서드를 이용한 방법
   - 스프링 컨테이너가 빈을 생성할 때, 해당 빈 객체의 인스턴스 팩토리 메서드를 호출하여 인스턴스를 생성한다.
   - 인스턴스 팩토리 메서드는 빈을 생성할 때 더 복잡한 로직이 필요할 때 사용된다.

#### 1. 생성자를 사용한 인스턴스화
모든 클래스에 대해 사용 가능한 방식이다. 즉, 대상이 되는 클래스는 어떤 인터페이스의 구현체일 필요도 없으며 정해진 방식으로 코딩할 필요도 없다. Bean 객체로 지정되는 것만으로도 충분하다.

일반적으로 많이 하는 착각 중 하나가 빈 객체로 선언된 클래스는 반드시 기본 생성자가 있어야한다는 것이다. 개발자가 클래스에 파라미터가 있는 생성자를 직접 작성한 경우, 해당 클래스에는 기본 생성자가 없을 수 있다. 이럴 경우에도 스프링 IoC 컨테이너에는 `@Autowired` 어노테이션을 이용하여 생성자 주입 방식으로 의존성을 주입할 수 있기에 빈 객체로 선언된 클래스에는 기본 생성자가 있어야 한다는 생각은 오류이다.

###### Java-based configuration examples
기본 생성자가 없는 클래스
```java
public class MyComponent {
  private final MyDependency dependency;

  public MyComponent(MyDependency dependency) {
    this.dependency = dependency;
  }
}
```

###### - 생성자를 이용한 빈 정의
```java
@Configuration
public class MyConfig {
  @Bean
  public MyComponent myComponent(MyDependency myDependency) {
    return new MyComponent(myDependency);
  }

  @Bean
  public MyDependency myDependency() {
    return new MyDependency();
  }
}
```

###### - Setter를 이용한 빈 정의
```java
@Configuration
public class MyConfig {
  @Bean
  public MyComponent myComponent() {
    MyComponent component = new MyComponent();
    component.setDependency(myDependency());
    return component;
  }

  @Bean
  public MyDependency myDependency() {
    return new MyDependency();
  }
}
```

###### Annotation-based configuration examples
```java
@Component
public class MyComponent {
  //@Autowired private MyDependency dependency;
  private final MyDependency dependency;

  //@Autowired
  public MyComponent(MyDependency dependency) {
    this.dependency = dependency;
  }
}
```
Field Injection을 사용하는 경우 자동으로 의존성이 주입된다. 
생성자 주입시 생성자가 하나 있을 경우 @Autowired를 생략해도 주입이 가능하도록 편의성을 제공한다.

#### 2. 정적 팩토리 메서드를 사용한 인스턴스화
생성자 대신에 정적 메서드를 이용하여 객체를 생성하는 방법. 이 방식은 객체 생성을 처리하는 코드를 별도의 클래스 또는 메서드 분리하여 캡슐화하고, 이를 통해 객체 생성 방식을 유연하게 변경할 수 있는 장점이 있다.

###### Annotation-based configuration examples
```java
@Configuration
public class MyConfig {
  @Bean
  public MyComponent myComponent() {
    return MyComponent.createInstance();
  }
}
@Component
public class MyComponent {
  private MyComponent() {
    // private constructor
  }
  public static MyComponent createInstance() {
    return new MyComponent();
  }
}
```
`@Component` 어노테이션에 의해 스프링이 `MyComponent`를 스캔하여 인스턴스화 하는 것을 막기 위해 기본 생성자를 명시적으로 private 선언하였다. 대신 `@Configuration`이 설정된 클래스에서 다시 빈으로 선언하고 정적 팩토리 메서드를 사용해 인스턴스화하도록 유도한다.

사실 어노테이션 기반으로 빈을 정의할 때는 `@Component`를 이용한 편리함이 큰 이점이라고 생각되는데, 팩토리 메서드를 사용해서 인스턴스화하는 경우에는 그 이점이 사라지고, 단지 "이 클래스는 빈이다"를 알리는 수준으로 어노테이션이 쓰이고 있다. 따라서 이 코드에는 `@Component`가 직접적으로 수행하고 있는 것은 없기 때문에 어노테이션을 제거하고 Java-based configuration 으로 작성해도 무관하다.

###### Java-based configuration examples
```java
public class MyComponentFactory {
    public MyComponent createMyComponent(MyDependency dependency) {
        return new MyComponent(dependency);
    }
}

public class MyComponent {
    private MyDependency dependency;
    
    public MyComponent(MyDependency dependency) {
        this.dependency = dependency;
    }
}

@Configuration
public class MyConfig {
    @Bean
    public MyComponentFactory myComponentFactory() {
        return new MyComponentFactory();
    }
    
    @Bean
    public MyComponent myComponent(MyComponentFactory myComponentFactory, MyDependency myDependency) {
        return myComponentFactory.createMyComponent(myDependency);
    }
}
```

실제로 사용하고자 하는 클래스는 `MyComponent`이나 이를 생성하기 위한 `MyComponentFactory` 빈을 추가로 정의한다. `MyComponentFactory`에 인스턴스 메서드로 `MyComponent`를 생성, 반환하는 메서드를 작성해주었다.

> ##### 정적 팩토리 메서드를 이용한 인스턴스화 vs. 인스턴스 팩토리 메서드를 이용한 인스턴스화
> 인스턴스 팩토리 메서드는 객체 생성을 담당하는 메서드가 별도의 클래스에 정의되기 때문에 해당 클래스를 먼저 인스턴스화한 후에 해당 메서드가 호출된다. 반면에 정적 팩토리 메서드는 객체 생성을 담당하는 메서드가 해당 클래스 내에 정의되므로 클래스 이름을 이용하여 해당 메서드를 호출한다.
>
> 따라서, 인스턴스 팩토리 메서드는 객체 생성 과정에서 다양한 방식으로 객체를 생성할 수 있도록 유연성을 제공하며, 객체 생성 과정에서 복잡한 로직을 처리할 때 유용하다. 반면에 정적 팩토리 메서드는 객체 생성 과정에서 유연성은 제한되지만, 객체 생성에 필요한 정보를 정적 메서드 인자로 전달할 수 있어서 코드 가독성이 높아지는 장점이 있다.

#### 4. XML-based 팩토리 메서드를 이용한 인스턴스화
위의 예제들을 보면, 팩토리 메서드를 사용해서 인스턴스화하는 경우에 Annotation 기반 혹은 Java 기반으로 코드를 작성할 경우 오히려 코드가 복잡해짐을 느낄 수 있다. 이때는 오히려 XML을 사용할 때 더 깔끔한데, factory-method라는 property key에 사용할 팩토리 메서드의 이름을 추가하기만 하면 된다.

###### 정적 팩토리 메서드를 사용하는 경우
```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```
```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

###### 인스턴스 팩토리 메서드를 사용하는 경우
```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```
```java
public class DefaultServiceLocator {
    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

## 맺음말..
Spring Core 기술 중 Ioc Container와 Bean에 대해 알아보는 시간을 가졌다.

스프링에서는 IoC 원칙을 구현하기 위해 BeanFactory를 상속받는 ApplicationContext를 IoC 컨테이너로 정의한다.
사용자는 XML, Annotation, Java 기반 Configuration으로 원하는 빈 객체를 선언하고, 스프링은 해당 정보를 가지는 BeanDefinition을 생성한다. 마지막으로 ApplicationContext는 이를 이용해 빈을 생성, 관리한다.


Ref   
[Spring Framework Documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html)

더 알아보면 좋을 글   
[의존이 복잡하게 얽힌 Bean들은 어떻게 생성될까?](https://creampuffy.tistory.com/177)   
[spring Boot Bean 동작 원리 & 클래스 final 작업](https://velog.io/@gale4739/Spring-Boot-Bean-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%ED%81%B4%EB%9E%98%EC%8A%A4-final-%EC%9E%91%EC%97%85Feat.-Component-%EC%83%9D%EC%84%B1%EC%9E%90-Value-)

