# @Configuration and Java-Based Configuration in Spring

## Introduction

In the Spring Framework, configuration defines **how beans are created, wired, and managed** by the Spring IoC container.

Earlier versions of Spring used **XML-based configuration**, but modern Spring applications use **Java-based configuration** with annotations such as:

- `@Configuration`
- `@Bean`
- `@ComponentScan`

The `@Configuration` annotation is used to **define a configuration class that contains bean definitions**.

---

# What is @Configuration?

`@Configuration` is a Spring annotation that indicates that a class contains **bean definitions**.

It tells the Spring container:

> "This class contains methods that define Spring Beans."

These beans are registered in the **Spring IoC container**.

---

# Syntax

```java
@Configuration
public class AppConfig {
}
```

A class annotated with `@Configuration` acts as a **replacement for XML configuration files**.

---

# Example Without @Configuration (Old XML)

Example of defining a bean in XML:

```xml
<bean id="userService" class="com.example.UserService"/>
```

---

# Java-Based Configuration Using @Configuration

We replace XML with Java code.

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService(){
        return new UserService();
    }

}
```

Spring registers the returned object as a **bean**.

---

# What is @Bean?

`@Bean` is used inside a `@Configuration` class to define a **bean manually**.

Example:

```java
@Bean
public UserService userService(){
    return new UserService();
}
```

Spring stores the returned object in the **IoC container**.

---

# Example of Java-Based Configuration

## Step 1: Create a Service Class

```java
public class UserService {

    public void display(){
        System.out.println("User Service Running");
    }

}
```

---

## Step 2: Create Configuration Class

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public UserService userService(){
        return new UserService();
    }

}
```

---

## Step 3: Load Configuration

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp {

    public static void main(String[] args){

        ApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);

        UserService service = context.getBean(UserService.class);

        service.display();

    }

}
```

Output:

```
User Service Running
```

---

# How @Configuration Works Internally

Process:

```
Application Start
       ↓
Spring reads @Configuration class
       ↓
Finds @Bean methods
       ↓
Creates Bean Objects
       ↓
Stores Beans in IoC Container
```

---

# Java-Based Configuration

Java-based configuration means **defining beans using Java code instead of XML**.

Example:

```
@Configuration
      ↓
@Bean methods
      ↓
Spring creates beans
```

This approach is widely used in **Spring Boot applications**.

---

# Advantages of Java-Based Configuration

1. Eliminates XML configuration
2. Type-safe configuration
3. Easy to debug
4. Better IDE support
5. Improves readability

---

# @Configuration vs @Component

| Feature | @Configuration | @Component |
|------|------|------|
| Purpose | Defines configuration class | Defines generic Spring bean |
| Contains | @Bean methods | Business logic |
| Role | Application setup | Application functionality |

Example:

```
@Configuration → Defines beans
@Component → Uses beans
```

---

# Example Combining @Configuration and @ComponentScan

```java
@Configuration
@ComponentScan("com.example")
public class AppConfig {
}
```

This tells Spring to:

1. Read configuration
2. Scan packages for components
3. Register beans automatically

---

# Types of Configuration in Spring

| Configuration Type | Description |
|------|------|
| XML Configuration | Beans defined in XML files |
| Annotation-Based Configuration | Using annotations like @Component |
| Java-Based Configuration | Using @Configuration and @Bean |

Modern Spring Boot mainly uses:

```
Java-Based + Annotation-Based Configuration
```

---

# Real Example Structure

Typical Spring Boot project:

```
com.example.project

controller
    UserController

service
    UserService

repository
    UserRepository

config
    AppConfig
```

Example configuration class:

```java
@Configuration
public class AppConfig {

    @Bean
    public ModelMapper modelMapper(){
        return new ModelMapper();
    }

}
```

---

# Summary

`@Configuration` is used to define **Spring configuration classes** that contain **bean definitions**.

Key points:

- Replaces XML configuration
- Works with `@Bean` to define beans
- Enables Java-based configuration
- Used widely in **Spring Boot applications**

Java-based configuration provides **clean, maintainable, and type-safe application configuration**.
