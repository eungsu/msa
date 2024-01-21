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
