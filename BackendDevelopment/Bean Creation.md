# Spring Beans in Spring Boot

A Spring bean is a Java object that is created and managed by the Spring IoC container instead of being instantiated manually with `new`. It is the core building block in Spring applications: services, repositories, controllers, and configuration objects are all beans managed by the container. [web:3]

## What is a Bean?

A **bean** is any object that:

- Is constructed by the Spring container (not by calling `new` yourself). 
- Has its dependencies injected by Spring (constructor, setter, or field injection). 
- Has its lifecycle (creation, initialization, destruction) managed by Spring. 

In practice, beans are injected into other beans via `@Autowired` or constructor injection, instead of manually wiring dependencies.

Conceptually:

> Bean = Spring-managed object that lives in the application context.

---

## Ways to Create a Bean in Spring Boot

Below are the main, idiomatic ways to define beans in a Spring Boot application.

### 1. Using Stereotype Annotations (`@Component` and Friends)

This is the most common way to define beans.

You place a stereotype annotation on a class:

- `@Component` – generic Spring bean.
- `@Service` – service-layer classes (specialization of `@Component`).
- `@Repository` – data access classes.
- `@Controller` / `@RestController` – web controllers.

Make sure the class is in a package scanned by Spring Boot (typically under your main application package).

```java
import org.springframework.stereotype.Service;

@Service
public class GreetingService {

    public String greet(String name) {
        return "Hello " + name;
    }
}
```
Spring Boot’s component scan detects GreetingService and registers it as a bean. You can then inject it into other beans:

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private final GreetingService greetingService;

    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/hello")
    public String hello(@RequestParam String name) {
        return greetingService.greet(name);
    }
}
```

### 2. Using @Bean Inside a @Configuration Class
Use this when you need to create a bean from a class you cannot annotate (for example, a third-party class), or when you want fine-grained control over how it is constructed.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MyCustomClient myCustomClient() {
        // Custom construction logic
        return new MyCustomClient("https://api.example.com");
    }
}
```
Key points:

The method name (myCustomClient) becomes the default bean name.

The return type (MyCustomClient) is the bean’s type.

You inject this bean like any other:

```java
import org.springframework.stereotype.Service;

@Service
public class ClientService {

    private final MyCustomClient myCustomClient;

    public ClientService(MyCustomClient myCustomClient) {
        this.myCustomClient = myCustomClient;
    }
}
```
### 3. Using XML Configuration (Legacy / Less Common in Boot)
Spring Boot prefers Java-based configuration, but XML configuration is still supported for legacy scenarios.

Example beans.xml:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.example.Student"/>
</beans>
```
When this XML is loaded into the application context, the student bean of type com.example.Student becomes available for injection.

### 4. Using @ConfigurationProperties Beans (Configuration Mapping)
This pattern is useful for binding external configuration from application.yml or application.properties into a strongly typed bean.

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app.client")
public class ClientProperties {

    private String baseUrl;
    private int timeout;

    // getters and setters
}
```
Example application.yml:

```text
app:
  client:
    base-url: https://api.example.com
    timeout: 5000
```
ClientProperties is a bean whose fields are populated from external configuration.

### 5. Auto-Configured Beans (Spring Boot Starters)
Spring Boot auto-configures many beans based on what is on the classpath and on your configuration properties.

### Common auto-configured beans include:

DataSource

EntityManagerFactory

RestTemplateBuilder

WebClient.Builder

You do not define these beans directly; you add the appropriate starter dependencies and inject them where needed.

```java
import org.springframework.stereotype.Service;
import org.springframework.web.reactive.function.client.WebClient;

@Service
public class HttpService {

    private final WebClient webClient;

    public HttpService(WebClient.Builder builder) {
        this.webClient = builder.baseUrl("https://api.example.com").build();
    }
}
```
Here, WebClient.Builder is an auto-configured bean provided by the Spring Boot WebFlux starter.
