<img width="567" alt="스크린샷 2024-07-21 오전 1 21 56" src="https://github.com/user-attachments/assets/33597add-d538-419c-8582-43fae7080a84"># 스프링 부트에서 API 중복 Request 처리하기

<br><br>

## 개요
   
API를 운영하다 보면 흔하게 발생하는 이슈 중 하나. 짧은 찰나에 동일한 API 요청이 거의 동시에 들어오는 것이다. 일명 *"따닥이슈"*   
일반적인 원인은 대개 유저가 브라우저 상에서 특정 버튼을 아주 빠르게 연속으로 클릭하는 것이고,   
크리티컬하게는 특정 엔티티의 상태 변화를 유발하는 아주 미세한 차이의 Race Condition이 발생하는 경우도 있다.   
API는 이런 상황에 대비하여 동일 요청에 대해 중복 실행을 방지하는 로직으로 대응할 필요가 있다.   
이번 발표는 Spring Boot 프로젝트에서 중복 실행 방지 로직을 구현하고 사용하는 예를 설명하고자 한다.

<br><br>

## 상황 설정

사용자가 화면에서 주문 버튼을 누른다고 가정해보자.   
서버는 여러대로 다중화 되어있고, 화면의 요청은 LoadBalancer를 통해서 여러 대의 서버에 골고루 요청한다.   
주문은 총 2회에 걸쳐서 각각 다른 서버로 요청된다.   
서버 측의 처리에 따라서 각기 다른 2개의 주문 요청이 생성될 수 있고, 내용이 중복될 수도 있으며, 각양각색의 피곤한 상황이 발생할 수 있다.

<br><br>

## 발생하는 문제
1. 한 번 수행할 로직을 두 번 이상 수행하여 리소스가 낭비된다.
2. 중복 데이터 저장 가능성이 있다.
-> 배치가 주기적으로 삭제 처리

<br><br>
*****

## 해결 방안

### 클라이언트 접근 방안
1. 버튼을 누른 후 일정 시간 동안 해당 버튼을 비활성화한다.
2. ajax 요청 시 `flag`를 걸어서 요청이 완료될 때 까지 재요청을 보낼 수 없게 한다.
3. `alert("진짜 주문하시겠습니까?");` 로 시간차를 두고 요청을 보내도록 노력한다.

<br><br>

### 애플리케이션 접근 방안

**서버 멱등성 혹은 동시성의 문제**   
클라이언트에서의 검증 보다는 멱등성 있는 시스템을 만들어야 한다.   

> #### 멱등성이란?
> 동일한 작업을 여러 번 수행하더라도 항상 같은 결과를 반환하는 성질을 의미한다.   
> 클라이언트 측에서 버튼을 여러 번 눌러도, 서버는 그 요청을 처음 한 번만 인식하고 처리하는 것.   
>이렇게 서버에서 한 번의 요청으로만 인식하여 처리함으로써 중복 등록과 같은 문제를 효과적으로 방지할 수 있다.


> #### 동시성 문제?
> 이는 동시성(concurrency) 문제의 한 예로도 볼 수 있다.   
> 동시성 문제란 여러 프로세스나 스레드각 동시에 같은 데이터에 접근하려 할 때 발생하는 문제를 의미한다.   
> 본 경우에는 여러 클라이언트 요청이 서버에 동시에 도달하여 중복된 작업을 수행하게 되는 상황이다.   
> 서버가 동시에 여러 요청을 받았을 때, 각각의 요청을 독립적으로 처리하지 못하고 중복된 작업을 실행하게 되면 이러한 동시성 문제가 발생한다.   

<br>

### 레디스를 이용해보자
Redis에는 get이라는 아주 사용하기 쉬운 명렁이 있다. get 명령은 **atomic**하게 동작하여 각 요청은 순차적으로 한 번에 하나씩만 atomic(원자적으로)하게 작동한단 뜻이다.

#### Key를 잘 설정하자
Redis에 key-value 형태로 데이터를 set 할 것이므로, key를 잘 결정해야 중복을 막을 수 있다. 다른 사용자에게 영향을 미치지 않으면서도 중복을 막을 수 있는 키를 설정한다.   
ex) 로그인한 사용자의 user_id + uuid 등   

#### 실제 호출 순서
1. client : 서버로 api 요청
2. server
   - 요청에 대해 key 값을 조립
   - redis에 key를 생성한다.
   - set의 result 값을 체크한다.
   - 만약 새로운 값으로 등록되었다면 정상적으로 진행
   - 만약 기존 값이 존재한다면 현재 요청 실패 처리
3. client : 응답을 받는다.
   - 성공이라면 중복 없이 성공한 것
   - 실패하였는데 중복 요청 때문이라면 적절하게 처리
4. 일정 시간 이후 : redis에서 사용했던 key는 expire time이 지나면 자동으로 제거되어 리소스에 신경 쓰지 않아도 됨.

### 예제
##### redis 정보 설정

``` yaml
spring.redis.host=localhost
spring.redis.port=6379
```

##### dependency

``` gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-redis'
	implementation 'org.springframework.boot:spring-boot-starter-web'

	compileOnly 'org.projectlombok:lombok:1.18.34'
	annotationProcessor 'org.projectlombok:lombok:1.18.34'
}
```
##### configuration

``` java
@Configuration
public class RedisConfiguration {

    @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        return new StringRedisTemplate(redisConnectionFactory);
    }
}
```

##### controller

``` java
@RestController
@RequestMapping("/example")
@RequiredArgsConstructor
public class ExampleController {

    private final ExampleService service;

    @GetMapping("/process")
    public String process(@RequestParam Long uuid) {
        return service.process(uuid);
    }
}
```

##### service

``` java
@Service
@Slf4j
@RequiredArgsConstructor
public class ExampleService {

    private final RedisTemplate redisTemplate;
    public String process(Long uuid) {// redis 로 lock 을 잡을 key 를 생성.
        String key = getKey(uuid);
        Boolean isSuccess = redisTemplate.opsForValue().setIfAbsent(key, "message", Duration.ofMinutes(5));
        log.info("## isSuccess=${}", isSuccess);
        if (isSuccess == false || isSuccess == null) {
            // 이미 키가 선점되어있으므로, 진행할 수 없습니다.
            return "이미 락이 걸려있습니다.";
        }

        try {
            // 여기서 하고싶은 처리를 합니다.
            // 테스트를 위한 코드이므로, 적절히 수정해야 합니다.
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "처리했습니다.";
        } finally {
            // 처리가 일찍 끝나면, lock 으로 잡았던 key 는 해제해줍니다.
            redisTemplate.unlink(key);
        }
    }

    private String getKey(Long id) {
        // 보통 redis 는 depth 를 구분할때 : 를 사용한다.
        return "processkey:" + id;
    }
}
```

##### test

- 터미널에 탭을 2개 띄우고 번갈아가면서 요청한다.
  - 락을 획득하면, 5초 후에 응답이 온다.
  - 락을 획득하지 못하면, 바로 응답한다.
    

<br><br>
*****

### 마무리

API 중복 요청이 들어올 경우 문제점과 해결 방안에 대해서 알아보았다.   
애플리케이션 접근법으로 (또 다른 방법인) AOP로 처리하거나 'lock' 메커니즘을 고려할 수 있겠다.   
중복 등록 방지를 위해 특정 데이터 또는 자원에 대한 접근을 잠그고, 한 번에 하나의 요청만 처리할 수 있게 하여 데이터의 일관성과 무결성을 유지하는 데 효과적일 수 있다.   
문제는 단일 서버 인스턴스에서의 동시성 문제에 대응할 수 있으나, 현재 운영 환경에서 서버 인스턴스가 다중화 되어 있는 상태를 고려할 때,   
각각의 서버 인스턴스는 독립적으로 작동하므로, 하나의 인스턴스에서 락을 걸어도 다른 인스턴스들은 그 영향을 받지 않는다는 것이다.   
<br>
각 요청에 대해 유일성을 보장하기 위해 분산 캐시(Redis)를 사용할 수도 있다.   
클라이언트로부터 받은 요청의 고유 식별자를 정의하고, 이를 생성하여 요청별로 분산 캐시에 저장한다. 동일한 ID 요청이 들어오는 경우 중복인 것으로 판단한다.   
하지만 실제 환경에서 고유 식별자를 완벽하게 생성하는 것이 실질적으로 어려울 수 있다. 클라이언트가 생성한 고유 식별자가 사실상 고유하지 않아 중복이 아닌 요청을 중복으로 잘못 판단하는 경우가 발생할 수 있다.   
그 외에도 네트워크 지연, 캐시 미스, 복잡성 문제 등으로 애플리케이션 접근 방안 또한 완벽한 해결점이 아닐 수 있음에 유의하자.   

*다른 해결 방안*   
[Redis 분산락을 이용한 중복 방지](https://winterroom.tistory.com/168)
[AOP로 중복 요청 처리(단일 서버일 경우)](https://soobysu.tistory.com/125)   
[데이터베이스 차원에서의 중복 방지](https://upcurvewave.tistory.com/646)
