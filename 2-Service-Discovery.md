# 서비스 디스커버리
- 서비스 디스커버리 패턴을 사용하여 서비스의 물리적 위치를 추상화한다.
- 유레카 같은 서비스 디스커버리 엔진은 서비스 클라이언트에 영향을 주지 않고 해당 환경에서 서비스 인스턴스를 원활하게 추가하고 삭제할 수 있다.

## 유레카 서버 구축
### 의존성
- Spring Boot DevTools
- Spring Configuration Processor
- Spring Boot Actuator
- **Eureka Server**

### 부트스트랩 클래스 설정
```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}

}

```

### application.yml 설정
```yml
server:
  port: 8761
  
spring:
  application:
    name: eureka-server

eureka:
  client:
    fetch-registry: false
    register-with-eureka: false
    service-url:
      defaultZone: http://localhost:8761/eureka/
 
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

## 스프링 유레카에 서비스 등록
### 의존성
- Spring Boot DevTools
- Spring Configuration Processor
- Spring Boot Actuator
- Spring Data JPA
- MySQL Driver
- Config Client
- **Eureka Discovery Client**
- Spring Web

### 부트스트랩 클래스 설정
```java
@SpringBootApplication
@EnableDiscoveryClient
public class DeptServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(DeptServiceApplication.class, args);
	}

}

```

### application.yml 설정
```yml
server:
  port: 8080
  
spring:
  application:
    name: dept-service
  profiles:
    active: dev
  config:
    import: optional:configserver:http://localhost:8888

  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect
    show-sql: true

### 스프링 유레카에 서비스 등록
eureka:
  client:
    fetch-registry: true
    register-with-eureka: true
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

## 넷플리스 Feigh 클라이언트로 서비스 호출

### 의존성
- OpenFeign

### 부트스트랩 클래스 설정
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class EmpServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(EmpServiceApplication.class, args);
	}

}

```

### application.yml
```yml

server:
  port: 8081

spring:
  application:
    name: emp-service
  profiles:
    active: dev
  config:
    import: optional:configserver:http://localhost:8888

  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect
    show-sql: true

### 스프링 유레카에 서비스 등록
eureka:
  client:
    fetch-registry: true
    register-with-eureka: true
    service-url:
      defaultZone: http://localhost:8761/eureka/

```

### Feign 인터페이스 정의
```java
// 데이터를 제공하는 스프링 애플리케이션의 이름을 설정한다.
@FeignClient("dept-service")
public interface DeptFeignClient {
	// 데이터를 제공하는 스프링 애플리케이션의 요청핸들러 메소드를 호출하는 Feign 클라이언트의 인터페이스를 정의한다.
	@RequestMapping(method = RequestMethod.GET, path = "/api/dept/{deptId}", consumes = "application/json")
	Dept getDept(@PathVariable("deptId") long deptId);
}

```

### Feign 인터페이스 활용
```java
@Service
@RequiredArgsConstructor
public class EmployeeServiceImpl implements EmployeeService {

	// Feign 인터페이스 구현체를 주입받는다.
	private final DeptFeignClient deptFeignClient;
	
	@Override
	public Dept getDept(long deptId) {
		return deptFeignClient.getDept(deptId);
	}
}

```


