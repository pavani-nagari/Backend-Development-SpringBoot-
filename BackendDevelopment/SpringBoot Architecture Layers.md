# Spring Boot Architecture Layers

Spring Boot follows a layered architecture pattern to keep applications modular, maintainable, scalable, and easy to test. Although projects can vary, most Spring Boot applications are structured into four primary layers:

- Presentation Layer  
- Service Layer  
- Repository (Data Access) Layer  
- Database Layer  

This section focuses on the **Presentation Layer (Controller Layer)**.

---

## 1️⃣ Presentation Layer (Controller Layer)

### Purpose

This layer handles HTTP requests and responses. It is the entry point of your application.

### Responsibilities

- Accept client requests (REST API calls)  
- Validate inputs  
- Call appropriate service methods  
- Return responses (JSON/XML)  
- Handle exceptions (via `@ControllerAdvice`)  

### Key Annotations

- `@RestController`  
- `@Controller`  
- `@RequestMapping`  
- `@GetMapping`, `@PostMapping`, etc.  
- `@Valid`  

### Example

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUserById(id);
    }
}
```

### Important Notes

- Should **not** contain business logic  
- Keeps APIs clean and thin  
- Acts as a bridge between frontend and backend  

---

## 2️⃣ Service Layer (Business Logic Layer)

### Purpose

Contains the core business logic of the application.

### Responsibilities

- Implement business rules  
- Perform calculations  
- Manage transactions  
- Coordinate multiple repository calls  
- Apply validation rules beyond simple request validation  

### Key Annotations

- `@Service`  
- `@Transactional`  

### Example

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("User not found"));
    }
}
```
### Why This Layer Is Important

- Separates business logic from controllers  
- Makes application testable (unit testing services)  
- Keeps controllers clean  

---

## 3️⃣ Repository Layer (Data Access Layer - DAO)

### Purpose

Handles interaction with the database.

### Responsibilities

- Perform CRUD operations  
- Write custom queries  
- Communicate with JPA/Hibernate  

Spring Boot commonly uses:

- Spring Data JPA  
- Hibernate (ORM framework)  

### Key Annotations

- `@Repository`  
- `JpaRepository`  
- `CrudRepository`  

### Example

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```
### What Happens Behind the Scenes

Spring Boot auto-configures:

- Entity Manager  
- Transaction Manager  
- Database connection  
- SQL generation  

This layer abstracts raw SQL from the rest of the application.

---

## 4️⃣ Database Layer

### Purpose

Actual storage of data.

### Can Be

- MySQL  
- PostgreSQL  
- MongoDB  
- Oracle  
- H2 (in-memory database)  

Spring Boot connects to the database using:

- JDBC  
- JPA  
- Hibernate  

### Configuration Example (`application.properties`)

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```

## 🔄 How Data Flows Through Layers

```text
Client Request
      ⬇
Controller Layer
      ⬇
Service Layer
      ⬇
Repository Layer
      ⬇
Database
```
Response flows back in reverse order.

## 🧠 Why Layered Architecture Is Important
```text
✅ Separation of concerns

✅ Easy maintenance

✅ Better testability

✅ Scalability

✅ Clean code structure
```

## 📌 Real-World Example
```text
If you're building an e-commerce app:

Controller → Handles /orders API

Service → Validates stock, applies discount rules

Repository → Saves order to the database

Database → Stores order data
```

## 🎯 Advanced Concepts Related to Layers
```text
DTO Layer (optional) → Prevents exposing entities directly

Exception Handling Layer → Global error management

Security Layer → Spring Security integration

Configuration Layer → Externalized configurations

Aspect Layer (AOP) → Logging, auditing
```
