# @Repository Annotation in Spring

## Introduction

`@Repository` is a **Spring stereotype annotation** used to indicate that a class belongs to the **Data Access Layer (DAO Layer)** of an application.

The repository layer is responsible for **interacting with the database** and performing operations such as:

- Insert
- Update
- Delete
- Retrieve data

`@Repository` is a **specialized form of `@Component`**, which means Spring automatically detects it during **component scanning** and registers it as a **Spring Bean** in the IoC container.

---

# Role of Repository Layer

In a typical **Spring Boot layered architecture**, the layers are:

```
Controller Layer → Handles HTTP requests
        ↓
Service Layer → Business logic
        ↓
Repository Layer → Database interaction
        ↓
Database
```

The **Repository Layer**:

- Communicates directly with the database
- Executes queries
- Handles persistence logic
- Manages data retrieval and storage

---

# Syntax

```java
@Repository
public class ClassName {
}
```

Optional naming:

```java
@Repository("userRepository")
public class UserRepository {
}
```

If no name is provided, Spring generates a bean name automatically.

Example:

```
UserRepository → userRepository
```

---

# Example of @Repository

## Step 1: Repository Layer

```java
import org.springframework.stereotype.Repository;

@Repository
public class UserRepository {

    public String getUserFromDatabase(){
        return "User data fetched from database";
    }

}
```

This class interacts with the **database**.

---

## Step 2: Service Layer

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository){
        this.userRepository = userRepository;
    }

    public String fetchUser(){
        return userRepository.getUserFromDatabase();
    }

}
```

The service layer calls the repository.

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

Application flow:

```
Client Request
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

# How @Repository Works

When the Spring application starts:

```
Application Start
        ↓
Component Scan
        ↓
Spring finds @Repository classes
        ↓
Registers them as Beans
        ↓
Stored inside IoC Container
```

Spring then allows these beans to be injected into other classes.

---

# Exception Translation Feature

One of the **major advantages of `@Repository`** is **automatic exception translation**.

Database-related exceptions are converted into **Spring DataAccessException hierarchy**.

Example:

Without `@Repository`

```
SQLException
```

With `@Repository`

```
DataAccessException
```

This provides **consistent database exception handling** across different databases.

---

# Example with Spring Data JPA

In modern Spring Boot applications, repositories often extend **Spring Data interfaces**.

Example:

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

}
```

Spring automatically provides implementations for:

- save()
- findById()
- findAll()
- delete()

---

# @Repository vs @Component

| Feature | @Component | @Repository |
|------|------|------|
| Type | Generic stereotype | Specialized stereotype |
| Layer | Any layer | Data access layer |
| Exception Translation | No | Yes |
| Purpose | General bean | Database operations |

Internally:

```
@Repository = @Component
```

But it adds **database-specific behavior**.

---

# @Repository vs DAO Pattern

DAO (Data Access Object) is a **design pattern** used to separate persistence logic.

Example DAO:

```
UserDAO
ProductDAO
OrderDAO
```

In Spring:

```
@Repository replaces traditional DAO implementation
```

---

# Advantages of @Repository

1. Clearly defines **data access layer**
2. Enables **automatic bean detection**
3. Provides **exception translation**
4. Improves **code organization**
5. Works seamlessly with **Spring Data JPA**

---

# Best Practices

Use `@Repository` when:

- Writing database access logic
- Implementing DAO classes
- Using JPA or Hibernate
- Performing CRUD operations

Avoid placing:

- Business logic
- HTTP request handling

inside repository classes.

---

# Summary

`@Repository` is a **Spring stereotype annotation** used to mark classes that handle **database operations**.

Key points:

- Specialized version of `@Component`
- Used in the **data access layer**
- Automatically registered as a **Spring Bean**
- Provides **exception translation**
- Works with **Spring Data JPA and Hibernate**

It is an essential annotation in **Spring Boot layered architecture for database interaction**.
