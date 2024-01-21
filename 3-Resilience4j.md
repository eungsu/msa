# 스프링 클라우드와 Resilience4j를 사용한 회복성 패턴
- 마이크로서비스처럼 고도로 분산된 애플리케이션을 설계할 때는 클라이언트 회복성을 고려해야 한다.
- 서비스의 전면 장애는 쉽게 탐지하고 처리할 수 있다.
- 성능이 낮은 서비스 하나가 자원을 소진하는 연쇄 효과를 유발할 수 있다. 서비스가 작업을 완료할 때까지 기다리는 동안 호출하는 클라이언트의 스레드가 블로킹되기 때문이다.
- 세가지 핵심 플라이언트 회복성 패턴은 회로 차단기 패턴, 폴백 패턴, 벌크헤드 패턴이다.
- 회로 차단기 패턴은 느리게 수행되고 저하된 시스템 호출을 제거해서 이러한 호출은 빨리 실패하고, 자원 소진을 막는다.
- 폴백 패턴은 원격 서비스 호출이 실패하거나 회로 차단기가 실패할 경우에 대체 코드 경로를 정의할 수 있다.
- 벌크헤드 패턴은 원격 자원에 대한 호출을 자체 스레드 풀로 격리해서 원격 자원 호출을 서로 분리한다. 한 종류의 서비스 호출이 실패하면 이 실패 때문에 애플리케이션 컨테이너의 모든 자원이 소진되지 않도록 해야 한다.
- 속도 제한기 패턴은 주어진 시간 동안 총 호출 수를 제한한다.
- Resilience4j를 사용하면 여러 패턴을 동시에 사용할 수 있다.
- 재시도 패턴은 서비스가 일시적으로 실패했을 때 시도하는 역할을 한다.
- 벌크헤드 패턴과 속도 제한기 패턴의 주요 차이점은 벌크헤드는 한 번에 동시 호출 수를 제한하는 역할을 하고, 속도 제한기는 주어진 시간 동안 총 호출 수를 제한하는 역할을 한다.

## 스프링 클라우드와 Resilience4j를 사용하는 서비스 설정
### 의존성
- Resilience4J

### 속도 제한기 패턴 구현
#### 속도 제한기 생성하기
```java
@GetMapping(path ="/message")
@RateLimiter(name = "getMessage", fallbackMethod = "getMessageError")
public ResponseEntity<String> getMessage() {
  return ResponseEntity.ok().body("message");
}

public ResponseEntity<String> getMessageError(Throwable ex) {
  return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS).body("Too many requests");
}
```

#### application.yml에 속도 제한기 패턴 구성하기
```yml
resilience4j:
  ratelimiter:
    instances:
      getMessage:
        limit-for-period: 2        # 갱신 제한 기간 동안 가용한 허용 수를 정의한다. 기본값은 50이다.
        limit-refresh-period: 5s   # 갱신 제한 기간을 정의한다. 기본값은 500ns다.
        timeout-duration: 0        # 스레드가 허용을 기다리는 시간을 정의한다. 기본값을 5초다.
```
