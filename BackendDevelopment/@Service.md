# @Service Annotation in Spring

## Introduction

`@Service` is a **Spring stereotype annotation** used to indicate that a class belongs to the **Service Layer** of the application.

The service layer contains **business logic** that processes data between the **Controller layer** and the **Repository (DAO) layer**.

`@Service` is a **specialized version of `@Component`**, meaning it automatically registers the class as a **Spring Bean** and allows Spring to manage it in the **IoC Container**.

---

# What is the Service Layer?

In a typical **Spring Boot application architecture**, the layers are:

```
Controller Layer → Handles HTTP requests
        ↓
Service Layer → Business Logic
        ↓
Repository Layer → Database Operations
```

The **Service layer**:

- Implements application **business rules**
- Coordinates multiple **repositories**
- Handles **transactions**
- Contains reusable **business logic**

---

# Syntax

```java
@Service
public class ClassName {
}
```

Optional naming:

```java
@Service("paymentService")
public class PaymentService {
}
```

If no name is provided, Spring automatically generates a bean name using **camelCase**.

Example:

```
PaymentService → paymentService
```

---

# Example of @Service

## Step 1: Repository Layer

```java
@Repository
public class UserRepository {

    public String getUser(){
        return "User Data from Database";
    }

}
```

---

## Step 2: Service Layer

```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository){
        this.userRepository = userRepository;
    }

    public String fetchUser(){
        return userRepository.getUser();
    }

}
```

The `UserService` class contains **business logic** and communicates with the repository.

---

## Step 3: Controller Layer

```java
@RestController
public class UserController {

    private final UserService userService;

    public UserController(UserService userService){
        this.userService = userService;
    }

    @GetMapping("/user")
    public String getUser(){
        return userService.fetchUser();
    }

}
```

Flow:

```
HTTP Request
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Database
```

---

# How @Service Works

When Spring starts the application:

```
Application Start
        ↓
Component Scan
        ↓
Find classes annotated with @Service
        ↓
Register as Spring Beans
        ↓
Store inside IoC Container
```

Spring automatically creates the object and manages it.

---

# Dependency Injection in @Service

`@Service` beans can inject other beans using `@Autowired`.

Example:

```java
@Service
public class PaymentService {

    @Autowired
    private OrderRepository orderRepository;

}
```

Spring automatically injects the required dependency.

---

# @Service vs @Component

| Feature | @Component | @Service |
|------|------|------|
| Type | Generic stereotype | Specialized stereotype |
| Layer | Any layer | Service layer |
| Purpose | General bean | Business logic |
| Spring Behavior | Same as Service | Same as Component |

Internally:

```
@Service = @Component
```

But `@Service` gives **semantic meaning** to the class.

---

# Why Use @Service Instead of @Component?

Although both work the same, `@Service` is preferred for **better architecture clarity**.

Example:

Bad Practice:

```java
@Component
public class PaymentService {}
```

Better Practice:

```java
@Service
public class PaymentService {}
```

Because it clearly indicates the **business logic layer**.

---

# @Service with Transactions

Service classes often manage **database transactions**.

Example:

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder(){
        // business logic
    }

}
```

The service layer is the **best place to manage transactions**.

---

# Advantages of @Service

1. Clearly defines **business logic layer**
2. Enables **automatic bean registration**
3. Works with **dependency injection**
4. Improves **code readability**
5. Supports **transaction management**

---

# Best Practices

Use `@Service` when:

- Implementing **business logic**
- Coordinating multiple repositories
- Managing transactions
- Creating reusable service classes

Avoid placing:

- Database queries
- HTTP request handling

inside the service layer.

---

# Summary

`@Service` is a **Spring stereotype annotation** used to mark classes that contain **business logic**.

Key points:

- Specialized version of `@Component`
- Used in the **Service layer**
- Automatically registered as a **Spring Bean**
- Supports **dependency injection**
- Often used with **transaction management**

It is a fundamental annotation in **Spring Boot layered architecture**.
