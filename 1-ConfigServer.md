# Spring Cloud Config Server
스프링 클라우드 컨피그 서버는 스프링 부트로 만든 REST 기반의 애플리케이션이다.

## 스프링 클라우드 컨피그 서버 구축
### 의존성
- Spring Boot DevTools
- Spring Configuration Processor
- Spring Boot Actuator
- Config Server

### 부트스트랩 클래스 설정
```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigServerApplication.class, args);
	}

}
```

### 스프링 클라우드 컨피그 서버에 파일 시스템 사용
#### application.yml 설정
```yml
server:
  port: 8888
  
spring:
  application:
    name: config-server
  profiles:
    active: native
  cloud:
    config:
      server:
        native:
          search-locations: classpath:/config    # 구성파일이 저장된 검색 위치를 설정한다.
```

### 스프링 클라우드 컨피그 서버에 github 사용
#### application.yml 설정
```yml
server:
  port: 8888
  
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/eungsu/Config-Files.git
```

### 구성파일 설정
1. src/main/resources 폴더에 config 폴더를 생성한다.
2. 구성파일을 생성한다.
   - 구성파일은 스프링애플리케이션이름-프로파일.properties로 설정한다.
  
#### 구성파일 예시
```
### dept-service-dev.properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/dept-db?useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=1234
```

```
### dept-service-prod.properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/dept-db?useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=1234
```

## 스프링 클라우드 컨피그와 스프링 부트 클라이언트 통합
### 의존성
- Spring Boot DevTools
- Spring Configuration Processor
- Spring Boot Actuator
- Spring Data JPA
- MySQL Driver
- Config Client
- Spring Web

### 부트스트랩 클래스 설정
```java
@SpringBootApplication
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
    name: dept-service	# 스프링 애플리케이션 이름이다. 구성파일의 dept-service-dev.properties 과 매칭된다.
  profiles:
    active: dev		# 스프링 애플리케이션 프로파일이다. 구성파일의 dept-service-dev.properties 과 매칭된다.
  config:
    import: optional:configserver:http://localhost:8888		# 스프링 컨피그 서버의 위치를 지정한다.

  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect
    show-sql: true
```
  
