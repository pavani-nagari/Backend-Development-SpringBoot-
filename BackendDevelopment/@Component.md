# @Component Annotation in Spring

## Introduction

`@Component` is one of the **core annotations in the Spring Framework**.  
It is used to **mark a Java class as a Spring Bean**, allowing Spring to automatically detect and manage it during **component scanning**.

In simple terms:

`@Component` tells Spring:

> "This class should be managed by the Spring IoC container."

Once marked with `@Component`, Spring automatically **creates, initializes, and manages the lifecycle of the object**.

---

# Why @Component is Used

Before annotations, Spring beans were defined in **XML configuration files**.

Example (Old XML approach):

```xml
<bean id="userService" class="com.example.UserService"/>
```

With `@Component`, we can eliminate XML configuration.

Example:

```java
@Component
public class UserService {
}
```

Spring automatically detects this class and registers it as a **bean**.

---

# Syntax

```java
@Component
public class ClassName {
}
```

Optional naming:

```java
@Component("userService")
public class UserService {
}
```

If no name is given, Spring uses the **class name with camelCase**.

Example:

```
UserService → userService
```

---

# How @Component Works Internally

For `@Component` to work, Spring must **scan packages** for annotated classes.

This process is called **Component Scanning**.

Example:

```java
@ComponentScan("com.example")
```

Spring scans the package and finds classes annotated with:

```
@Component
@Service
@Repository
@Controller
```

Then it registers them as beans.

---

# Example

## Step 1: Create a Component

```java
import org.springframework.stereotype.Component;

@Component
public class UserService {

    public void display(){
        System.out.println("User Service Running");
    }

}
```

---

## Step 2: Enable Component Scan

```java
@Configuration
@ComponentScan("com.example")
public class AppConfig {
}
```

---

## Step 3: Use ApplicationContext

```java
ApplicationContext context =
new AnnotationConfigApplicationContext(AppConfig.class);

UserService service = context.getBean(UserService.class);

service.display();
```

Output:

```
User Service Running
```

---

# How Spring Registers @Component Beans

Process:

```
Application Start
      ↓
Component Scan
      ↓
Find @Component classes
      ↓
Register as Bean Definitions
      ↓
Create Bean Objects
      ↓
Store in IoC Container
```

---

# Bean Naming in @Component

Spring automatically assigns a **bean ID**.

Example:

```java
@Component
public class OrderService {}
```

Bean name:

```
orderService
```

You can also define a custom name.

```java
@Component("orderServiceBean")
public class OrderService {}
```

---

# @Component with Dependency Injection

Example:

```java
@Component
public class OrderService {

    @Autowired
    private UserService userService;

}
```

Spring automatically injects the dependency.

---

# Specialized Stereotype Annotations

Spring provides specialized versions of `@Component`.

All of these are internally based on `@Component`.

| Annotation | Layer | Purpose |
|------|------|------|
| @Component | Generic | Any Spring-managed bean |
| @Service | Service Layer | Business logic |
| @Repository | DAO Layer | Database operations |
| @Controller | Web Layer | Handles HTTP requests |

Example:

```java
@Service
public class PaymentService {
}
```

---

# @Component vs @Bean

| Feature | @Component | @Bean |
|------|------|------|
| Definition Style | Annotation on class | Annotation on method |
| Configuration Type | Class-based | Java configuration |
| Control | Automatic | Manual |
| Used In | Component scanning | Configuration classes |

Example:

```java
@Bean
public UserService userService(){
    return new UserService();
}
```

---

# Advantages of @Component

1. Eliminates XML configuration
2. Enables automatic bean detection
3. Improves readability
4. Reduces boilerplate code
5. Supports dependency injection

---

# Limitations

- Works only if **component scanning is enabled**
- Less control compared to `@Bean` in some cases

---

# Best Practices

Use `@Component` when:

- Creating **generic Spring beans**
- Classes that do not belong to a specific layer

Use specialized annotations when possible:

```
@Service → Business logic
@Repository → Data access
@Controller → Web layer
```

---

# Summary

`@Component` is a **core Spring annotation** used to mark a class as a **Spring-managed bean**.

Key points:

- Enables **automatic bean detection**
- Works with **component scanning**
- Managed by **Spring IoC container**
- Base annotation for `@Service`, `@Repository`, and `@Controller`

It is one of the most fundamental annotations used in **Spring and Spring Boot applications**.
