# 자바 빈 vs 스프링 빈

## Java Bean

Java Bean 은 데이터를 표현하는 것을 목적으로 하는 자바 클래스. Java Bean 규약 에 맞춰서 만든 클래스

**Java Bean 규약**

1. 기본생성자가 존재해야한다.

2. 모든 멤버변수의 접근제어자는 private이다.

3. 멤버변수마다 getter/setter가 존재해야한다. (속성이 boolean일 경우 is를 붙힘)

4. 외부에서 멤버변수에 접근하기 위해서는 메소드로만 접근할 수 있다.

5. Serializable(직렬화)가 가능해야한다.

- 직렬화
  - 시스템 내부에서 사용하는 객체 혹은 데이터를 외부의 시스템에서도 사용할 수 있도록 변환시키는 것
  - 자바에서는 JVM의 Heap 영역에 상주한 객체를 byte형태로 변환시키거나(직렬화), byte 형태를 다시 자바 객체로 변환하는 것(역직렬화)을 말한다.
  - CSV, JSON format으로 자바 객체를 변경시키는 것도 직렬화하는 것이라고 볼 수 있다.
  - Serializable 인터페이스를 implements한 클래스는 직렬화 할 수 있다.

- 스프링에서 자바 빈의 사용
  - Spring은 뷰 영역(JSP, 혹은 다른 템플릿 엔진)에 데이터를 출력하고 싶을 때 *Java Bean 규약*에 맞춰 만들어진 객체를 사용하고, 객체들을 외부 저장소에 저장하고 전송할 때 사용한다.

```java
import java.io.Serializable;

// 직렬화가 가능하도록 Serializable 인터페이스를 구현
public class Person implements Serializable {
    // 모든 멤버변수의 접근자는 private
    private String name;
    private int age;
    private String address;

    // 기본생성자가 있어야한다.
    public Person() {
    }

    // 기본생성자가 있다면 매개변수가 있는 생성자가 있어도 무방함
    public Person(String name, int age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    // 각 멤버변수에 접근할 수 있는 getter/setter가 있어야한다.
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
```

## Spring Bean

Spring Framework의 Container에 의해 등록, 생성, 조회, 관계설정이 되는 객체이다

일반 **Java Object**와 동일하지만 **IoC** 방식으로 관리되는 오브젝트를 뜻한다.

'Spring Bean'은 'Java Bean'과는 달리 별다른 생성 규칙은 없다.

# IoC와 DI

- **스프링 삼각형**
    
    스프링의 기반이 되는 설계 개념

    ![스크린샷 2024-07-20 오후 6 14 46](https://github.com/user-attachments/assets/3c8976df-db78-4404-be8b-2780c0c3d947) 
- '의존'의 의미
    - 의존성은 new다
    - 전체가 부분에 의존한다
> 참고
>
> - 집합관계 : 부분이 전체와 다른 생명 주기를 가질 수 있다. 
>   - 예:  집 vs 냉장고
> - 구성관계 : 부분은 전체와 같은 생명 주기를 갖는다. 
>   - 예: 사람 vs 심장

- **IoC :**
    - **#인터페이스 #인자로 전달, #런타임 시점**
    - **#결합도를 줄어듬, #객체를 편하게 관리**, **#확장성이 좋아짐**
    
    제어의 역전이라는 의미로, **객체의 생명주기 및 타 객체와의 의존관계 설정**을 객체 자신이 아닌 어플리케이션 컨텍스트(혹은 IoC **컨테이너**, 빈 팩토리 등으로 표현)가 **대신 해줌**.
    
    스프링 프레임워크 뿐만 아니라 타 프레임워크 및 디자인 패턴에서도 사용되는 개념.
    
    객체의 사용여부가 타 객체에 의해 수동적으로 결정되어 사용되는 방식도 IoC의 개념이 적용된 것.
    
    설계 시점에서 알 수 없는 **런타임 시점**에 객체간의 의존관계를 제 3자(컨테이너)가 결정하고, 컨테이너가 객체 레퍼런스를 제공해주는 것. 
    
- **DI란**
    - **#의존성을 낮춘다, #의존관계 주입에 집중**
    - DI는 스프링에서 지원하는 IoC의 한 형태
    - 각 계층/Class 사이에 필요로 하는 의존 관계가 있다면  **Bean** **설정 정보**를 바탕으로 **컨테이너가 자동적으로 연결**시켜 주는 것



- **IoC와 DI의 차이점**
    - IoC
      - 객체의 흐름, 생명주기 관리 등 독립적인 제 3자에게 역할과 책임을 위임하는 방식의 **디자인 패턴**
      - IoC라는 개념은 스프링에만 국한되는 패턴은 아님 : 다른 컨테이너를 가진 프레임워크들에서도 찾아볼 수 있기 때문
    - DI
      - IoC보다 **의존 관계 주입**에 초점을 좀 더 맞춘 개념

- **인터페이스와 런타임**
    - 인터페이스를 통한 느슨한 의존 관계에서는 클래스 다이어그램만으로는 실제 A 클래스가 런타임 시점에 C인터페이스를 구현한 어떤 구현 클래스를 사용할지 알 수 없다.
    - 사전에 설정을 통해 어떤 클래스의 객체를 쓸지 정해둘 수는 있으나, 그 내용이 설계나 코드 상에는 나타나지 않는다.
    - 결국 컨테이너 설정에 따라 구체적인 사용 클래스를 지정해두고, 런타임 시점에 객체간의 의존 관계를 연결해주는 작업이 의존관계 주입이다.
    ![스크린샷 2024-07-20 오후 6 15 23](https://github.com/user-attachments/assets/3cc8a0b8-0a0d-4bc9-8f2b-feb1ccfe0a2e)

# IoC 구현 방법
![스크린샷 2024-07-20 오후 6 16 03](https://github.com/user-attachments/assets/920bc4ec-a603-45ac-ab0f-0d5397351739)

### 1. DL(Dependency Lookup): 의존성 검색

의존관계가 필요한 객체에서 직접 검색

### 2. DI(Dependency Injection): 의존성 주입

컨테이너에서 제공하는 API를 이용해 사용하고자 하는 빈(Bean)을 저장소에서 Lookup 하는 것을 말한다.

new 연산자를 사용하여 객체를 생성하지 않고, 각 클래스의 의존관계를 **Bean 정보**를 바탕으로 컨테이너가 자동 생성, 연결한다.

# 일반적인 의존관계 vs IoC
![스크린샷 2024-07-20 오후 6 16 34](https://github.com/user-attachments/assets/bf12abe2-a985-4e9d-8a6c-b43a8de0ec23)
### 일반적인 의존 관계
![스크린샷 2024-07-20 오후 6 16 56](https://github.com/user-attachments/assets/3b0a0499-fe81-4438-ac25-2dfbe340b352)
- 바리스타
    - 사용할 원두를 생성하여 사용.
    - 원두가 변경되면 바리스타는 변경된 원두를 새로 생성해서 사용한다

```java
interface CoffeeBean {
	void roastBeans();
}

class ArabicaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("아라비카 원두를 볶는 나!");
    }
}

class RobustaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("로부스타 원두를 볶는 나!");
    }
}
```
```java
class Barista {
	private CoffeeBean coffeebean;
    
    public CoffeeBean() {
    	this.coffeebean = new ArabicaBean();
    }
    
    public void roasting() {
	 	this.coffeebean.roastBeans();
    }
}
```
```java
public class Cafe {
	public static void main(String[] args) {
		Barista barista = new Barista();
		barista.roasting();
	}
}
```

만약 원두를 바꾼다면..
```java
class Barista {
	private CoffeeBean coffeebean;
    
    public Barista() {
	    //this.coffeebean = new ArabicaBean();
    	this.coffeebean = new RobustaBean();
    }
    
    public void roasting() {
	 		this.arabicaBean.roastBeans();
    }
}
```
### 의존관계 역전 (IoC) - 생성자를 통해 의존성 주입
![스크린샷 2024-07-20 오후 6 17 58](https://github.com/user-attachments/assets/e9fb18a6-a932-4864-a6c6-c30645fe21b2)
- 원두
    - CoffeeBean을 **인터페이스**로 만들고, CoffeeBean을 implements하는 각각의 원두입클래스를 만듦.
- 바리스타
    - 원두를 자신이 만들어서 사용하는 것이 아닌, 외부에서 만들어진 원두를 **인자**로 받아서 사용 
    - 원두 타입을 **인터페이스**로 바꿨기에 어떤 원두든 코드 변경없이 (새로 추가하여) 사용 가듬
    - 구체 객체를 정확히 알지 못해도, 인터페잇를 구현한 어떤 객체가 들어와도 정상적으로 작동함 **(확장성)**
- 코드를 실행하는 부분(카페)
    - 원두의 종류를 선택하여 **생성**하고 바리스타에게 **인자**로 전달함

⇒ 바리스타가 원두에 의존하고 있던 관계가 바뀜. 이러한 현상을 DIP(의존 관계 역전 원칙)라고 함
- 장점
    - 유연성이 높아짐
> **참고 - DIP(Dependency Inversion Principle)**
> 
> 객체는 저수준 모듈보다 고수준 모듈에 의존해야 한다
> 
> - 고수준 : 인터페이스 같은 추상적 개념
> - 저수준 : 구현된 객체

```java
interface CoffeeBean {
	void roastBeans();
}

class ArabicaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("아라비카 원두를 볶는 나!");
    }
}

class RobustaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("로부스타 원두를 볶는 나!");
    }
}
```
```java
class Barista {

	private CoffeeBean coffeeBean;
    
    public Barista(CoffeeBean coffeeBean) {
		this.coffeeBean = coffeeBean;
	}
    
    public void roasting() {
        this.coffeeBean.roastBeans();
    }
}
```
```java
public class Cafe {
	public static void main(String[] args) {
        
        // ArabicaBean
        CoffeeBean arabicaBean = new ArabicaBean();
        Barista barista = new Barista(arabicaBean);
        barista.roasting();
        
        // RobustaBean
        CoffeeBean robustaBean = new RobustaBean();
        Barista barista = new Barista(robustaBean);
        barista.roasting();
    }
}
```
### 의존관계 역전 (IoC) - 속성 통해 의존성 주입
```java
interface CoffeeBean {
	void roastBeans();
}

class ArabicaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("아라비카 원두를 볶는 나!");
    }
}

class RobustaBean implements CoffeeBean {
	public void roastBeans() {
    	System.out.println("로부스타 원두를 볶는 나!");
    }
}
```
```java
class Barista {

	private CoffeeBean coffeeBean;
    
    //coffeeBean 속성에 대한 설정자 메서드
    public void setCoffeeBean(CoffeeBean coffeeBean) {
		this.coffeeBean = coffeeBean;
	}
    
    public void roasting() {
        this.coffeeBean.roastBeans();
    }
}
```
```java
public class Cafe {
	public static void main(String[] args) {
    	Barista barista = new Barista();
        
        // ArabicaBean
        CoffeeBean arabicaBean = new ArabicaBean();
        barista.setCoffeeBean(arabicaBean);
        barista.roasting();
        
        // RobustaBean
        CoffeeBean robustaBean = new RobustaBean();
        barista.setCoffeeBean(robustaBean);
        barista.roasting();
    }
}
```

# IoC vs 스프링 IoC/DI
### 스프링 IoC/DI - 필드 주입

- 원두와 바리스타를 빈으로 등록한다
- 생성 코드가 따로 없음
- @Component 어노테이션이 붙은 변수의 타입(타입이 같은 빈이 여러개 있으면 이름을 본다)을 가진 객체를 해당 변수에 의존성 주입시킴

```java
interface CoffeeBean {
	String roastBeans();
}

@Component("arabicaBean") // arabicaBean이란 이름을 가진 Bean으로 등록
public class ArabicaBean implements CoffeeBean {
	public String roastBeans() {
    	return "아라비카 원두를 볶는 나!";
    }
}

@Component("robustaBean") // robustaBean이란 이름을 가진 Bean으로 등록
public class RobustaBean implements CoffeeBean {
	public String roastBeans() {
    	return "로부스타 원두를 볶는 나!";
	}
}
```
```java
@Component	// 의존성을 주입받는 객체도 Bean으로 등록되어있어야 합니다.
public class Barista {
	
    @Autowired	// 의존성 주입
    @Qualifier("arabicaBean")	// 사용할 의존 객체를 선택
	private CoffeeBean coffeeBean;
    
    public String roasting() {
    	return this.coffeeBean.roastBeans();
    }
}
```
```java
@RestController
public class RoastController {
	
	@Autowired	// Barista라는 타입을 가진 Bean을 찾아서 주입시킴
    private Barista barista;
    
    @RequestMapping("/roast")
    public String roastDriver() {
    	return barista.roasting();
    }
}
```

- Quaifier 어노테이션
  - 동일한 타입을 가진 bean 객체가 2개 이상일 경우 사용
  - @Qualifier에 지정한 한정자 값을 갖는 bean 객체가 존재하지 않으면 Exception이 발생
- @Autowired 어노테이션 적용시 의존 객체를 찾는 순서
   1. 타입이 같은 bean 객체를 검색한다. => 1개이면 해당 bean 객체를 사용한다.
      - @Qualifier가 명시되어 있는 경우 같은 값을 갖는 bean 객체여야 한다.
   2. 타입이 같은 bean 객체가 두개 이상이고, @Qualifier가 없는 경우 이름이 같은 빈 객체를 찾는다.
      - 찾은경우 그 객체를 사용
   3. 타입이 같은 bean 객체가 두개 이상이면, @Qualifier로 지정한 bean 객체를 찾는다.
      - 찾은경우 그 객체를 사용
   4. 위 경우 모두 해당되지 않으면 컨테이너가 Exception을 발생시킨다.
    

# 생성자 주입 vs setter 주입 vs 필드 주입
1. 필드 주입: @Autowired를 필드에 직접 붙여서 주입.
    - 필드 주입은 간편하지만, 테스트나 유지보수에서의 불편함
2. 생성자 주입: 생성자를 통해 주입. 
   - 생성자에 @Autowired를 붙이거나, 스프링 4.3 이상에서는 생성자가 하나일 경우 @Autowired를 생략해도 됩니다.
   - 런타임에 누락된 의존성을 빨리 감지할 수 있음. 
   - 생성자 호출 시 검증 로직을 추가할 수 있어 추후 재구성이나 재주입이 가능. 
   - 빈의 의존성이 변경될 가능성이 높은 상황에서 유연성을 제공한다.
3. 세터 주입: 세터 메서드에 @Autowired를 붙여서 주입.
   - Setter 기반 DI는 필수적이지 않은 선택적인 의존성을 주입할 때 유용하다.
   - Setter 기반 DI를 사용하면 해당 값이 존재하는지에 대한 확신이 없기 때문에 사용할 때마다 null 체크를 통해 혹시 모를 오류에 대비해야 하므로 스프링 팀에서는 일반적으로 생성자 기반 DI를 권장한다.
### 세터 주입
- 빈을 찾아서 의존성 주입
```java
@Component	
public class Barista {
	
    @Autowired // 의존성 주입
    @Qualifier("arabicaBean") // 사용할 의존 객체를 선택
	private CoffeeBean coffeeBean;
    
    public String roasting() {
    	return this.coffeeBean.roastBeans();
    }
}
```
```java
@RestController
public class RoastController {
	
	@Autowired	// Barista라는 타입을 가진 Bean을 찾아서 주입시킴
    private Barista barista;
    
    @RequestMapping("/roast")
    public String roastDriver() {
    	return barista.roasting();
    }
}
```
### setter 주입
- 빈 객체의 기본 쟁성자를 먼저 호출 한 뒤, 세터 메서드를 통해 의존성을 주입
```java
@Component
public class Barista {

    private CoffeeBean coffeeBean;

    
    @Autowired //기본 생성자를 호출 > set메소드를 통해 수정후 주입
    @Qualifier("arabicaBean") //사용할 의존 객체 선택
    public void setCoffeeBean(CoffeeBean coffeeBean) {
        this.coffeeBean = coffeeBean;
    }

    public String roasting() {
        return this.coffeeBean.roastBeans();
    }
}
```
```java
@RestController
public class RoastController {

    private Barista barista;

    @Autowired // Barista라는 타입을 가진 Bean을 찾아서 수정 후 주입
    public void setBarista(Barista barista) {
        this.barista = barista;
    }

    @RequestMapping("/roast")
    public String roastDriver() {
        return barista.roasting();
    }
}
```
### 생성자 주입
- 의존성이 불변임을 보장하고, 더 쉽게 테스트할 수 있다
```java
@Component
public class Barista {

    private final CoffeeBean coffeeBean;

    @Autowired //사용할 의존 객체를 생성 후 주입
    public Barista(@Qualifier("arabicaBean") CoffeeBean coffeeBean) {
        this.coffeeBean = coffeeBean;
    }

    public String roasting() {
        return this.coffeeBean.roastBeans();
    }
}
```
```java
@RestController
public class RoastController {

    private final Barista barista;

    @Autowired // Barista라는 타입을 가진 Bean을 생성 후 주입
    public RoastController(Barista barista) {
        this.barista = barista;
    }

    @RequestMapping("/roast")
    public String roastDriver() {
        return barista.roasting();
    }
}
```