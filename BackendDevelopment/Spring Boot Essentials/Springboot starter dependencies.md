# Spring Boot Starter Dependencies
## Understanding What Each Starter Provides (With Banking Sector Examples)

## Introduction

Spring Boot provides **Starter Dependencies** to simplify dependency management.

A **Spring Boot Starter** is a **pre-configured dependency bundle** that includes all libraries required for a specific functionality.

Instead of adding many dependencies manually, developers add **one starter**, and Spring Boot automatically pulls the required libraries.

Example:

Without starter:

```
spring-web
spring-webmvc
jackson-databind
tomcat
validation-api
```

With starter:

```
spring-boot-starter-web
```

This makes project setup **faster and simpler**.

---

# Why Starters are Important

In enterprise systems like **banking platforms**, applications require multiple capabilities such as:

- REST APIs
- Database connectivity
- Security
- Messaging
- Monitoring

Starters provide **ready-to-use configurations** for these features.

---

# How Starter Dependencies Work

When you add a starter:

```
spring-boot-starter-*
```

Spring Boot:

1. Downloads required libraries
2. Applies **Auto-Configuration**
3. Configures default settings

Example flow:

```
Add starter dependency
        ↓
Spring Boot loads required libraries
        ↓
Auto-configuration activates
        ↓
Application ready to run
```

---

# Example: Banking API Dependency

### Maven Dependency

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This automatically includes:

- Spring MVC
- Embedded Tomcat
- Jackson JSON converter
- Validation support

---

# Common Spring Boot Starters

## 1. spring-boot-starter-web

Used for building **REST APIs and web applications**.

Includes:

- Spring MVC
- Embedded Tomcat
- Jackson (JSON conversion)
- Validation API

### Banking Example

Used for APIs like:

```
GET /accounts
POST /transfer
GET /transactions
```

Controller Example:

```java
@RestController
@RequestMapping("/accounts")
public class AccountController {

    @GetMapping
    public String getAccounts(){
        return "Account list";
    }

}
```

---

# 2. spring-boot-starter-data-jpa

Used for **database operations using JPA and Hibernate**.

Includes:

- Spring Data JPA
- Hibernate ORM
- Transaction management
- JPA APIs

### Banking Example

Handling bank accounts in database.

```java
@Entity
public class Account {

    @Id
    private Long id;

    private String accountHolderName;
    private double balance;

}
```

Repository:

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
}
```

Spring Boot automatically provides CRUD methods.

---

# 3. spring-boot-starter-security

Provides **authentication and authorization features**.

Includes:

- Spring Security
- Password encryption
- Authentication filters
- Role-based access control

### Banking Example

Securing APIs such as:

```
/transfer
/account/balance
/loan/apply
```

Security Configuration:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .anyRequest()
            .authenticated();
    }

}
```

---

# 4. spring-boot-starter-validation

Used for **input validation in APIs**.

Includes:

- Hibernate Validator
- Jakarta Validation API

### Banking Example

Validating transaction requests.

```java
public class TransferRequest {

    @NotNull
    private String fromAccount;

    @NotNull
    private String toAccount;

    @Min(1)
    private double amount;

}
```

---

# 5. spring-boot-starter-test

Used for **unit testing and integration testing**.

Includes:

- JUnit
- Mockito
- Spring Test
- AssertJ

### Banking Example

Testing transaction service.

```java
@SpringBootTest
public class TransactionServiceTest {

    @Autowired
    private TransactionService service;

    @Test
    void testTransaction(){
        assertNotNull(service);
    }

}
```

---

# 6. spring-boot-starter-actuator

Provides **monitoring and health checks**.

Includes endpoints for:

- Application health
- Metrics
- System status

### Banking Example

Monitoring banking microservices.

```
/actuator/health
/actuator/metrics
/actuator/info
```

Example response:

```
{
 "status": "UP"
}
```

---

# 7. spring-boot-starter-cache

Used to **improve performance by caching data**.

### Banking Example

Caching frequently accessed data such as:

```
Exchange rates
Branch information
Customer profile data
```

Example:

```java
@Cacheable("accounts")
public Account getAccount(Long id){
    return repository.findById(id).orElse(null);
}
```

---

# Real Banking Microservice Example

Dependencies used in a **Banking Transaction Service**.

```
spring-boot-starter-web
spring-boot-starter-data-jpa
spring-boot-starter-security
spring-boot-starter-validation
spring-boot-starter-actuator
mysql-connector-j
```

Capabilities provided:

| Starter | Banking Use Case |
|------|------|
| Web | REST APIs |
| Data JPA | Account database |
| Security | Authentication |
| Validation | Transaction input validation |
| Actuator | System monitoring |

---

# Advantages of Starter Dependencies

1. Simplifies dependency management
2. Reduces configuration effort
3. Provides production-ready defaults
4. Works seamlessly with auto-configuration
5. Faster development

---

# Custom Starters

Large organizations (including banks) sometimes create **custom starters**.

Example:

```
bank-security-starter
bank-logging-starter
bank-audit-starter
```

These starters enforce **company-wide standards**.

---

# Summary

Spring Boot Starter Dependencies are **pre-configured dependency bundles** that simplify project setup.

Key ideas:

```
Starter → Dependency bundle
Auto Configuration → Automatic setup
Production-ready defaults
```

In enterprise systems such as **banking applications**, starters enable rapid development of:

- REST APIs
- Secure systems
- Database services
- Monitoring tools
- Microservices architectures
