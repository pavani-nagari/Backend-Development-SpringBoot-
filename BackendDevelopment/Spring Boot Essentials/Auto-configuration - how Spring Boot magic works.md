# Spring Boot Auto-Configuration
## How Spring Boot "Magic" Works (With Banking Sector Examples)

## Introduction

One of the biggest advantages of **Spring Boot** is **Auto-Configuration**.

Spring Boot automatically configures the application based on:

- Dependencies present in the project
- Configuration properties
- Environment settings

This removes the need for **manual configuration**, making application development **faster and easier**.

This automatic setup is often referred to as:

```
Spring Boot Magic
```

But internally, it is implemented through **Auto-Configuration classes and conditional annotations**.

---

# What is Auto-Configuration?

Auto-Configuration is a feature where **Spring Boot automatically configures beans based on the libraries available in the classpath**.

Example:

If your project includes:

```
spring-boot-starter-web
```

Spring Boot automatically configures:

- Embedded Tomcat Server
- DispatcherServlet
- Jackson JSON converter
- Spring MVC configuration

You do not need to configure them manually.

---

# Banking Application Example

Imagine we are building a **Banking API**.

Dependencies:

```
spring-boot-starter-web
spring-boot-starter-data-jpa
mysql-connector
spring-boot-starter-security
```

Spring Boot automatically configures:

| Dependency | Auto Configuration |
|------|------|
| Web Starter | REST controllers, Tomcat |
| Data JPA | Hibernate, EntityManager |
| MySQL | DataSource |
| Security | Basic authentication |

Without auto-configuration, developers would have to configure all of these manually.

---

# Example Banking Application

## Dependencies

```
spring-boot-starter-web
spring-boot-starter-data-jpa
mysql-connector-j
```

---

# application.properties

```
spring.datasource.url=jdbc:mysql://localhost:3306/bankdb
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

Spring Boot automatically creates:

- DataSource
- Hibernate configuration
- Transaction manager
- Entity manager

---

# Banking Example: Account Entity

```java
@Entity
public class Account {

    @Id
    @GeneratedValue
    private Long id;

    private String accountHolderName;
    private double balance;

}
```

---

# Banking Repository

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
}
```

Spring Boot automatically provides:

```
save()
findById()
findAll()
delete()
```

No manual implementation needed.

---

# Banking Service

```java
@Service
public class AccountService {

    private final AccountRepository repository;

    public AccountService(AccountRepository repository){
        this.repository = repository;
    }

    public List<Account> getAllAccounts(){
        return repository.findAll();
    }

}
```

---

# Banking Controller

```java
@RestController
@RequestMapping("/accounts")
public class AccountController {

    private final AccountService accountService;

    public AccountController(AccountService accountService){
        this.accountService = accountService;
    }

    @GetMapping
    public List<Account> getAccounts(){
        return accountService.getAllAccounts();
    }

}
```

No configuration needed for:

```
Tomcat
Spring MVC
JSON conversion
Hibernate
Database connection
```

Spring Boot configures everything automatically.

---

# How Spring Boot Auto-Configuration Works Internally

When the application starts:

```
@SpringBootApplication
        ↓
@EnableAutoConfiguration
        ↓
Spring Boot scans AutoConfiguration classes
        ↓
Checks classpath dependencies
        ↓
Applies matching configurations
```

---

# @SpringBootApplication

This annotation is the **entry point of Spring Boot**.

```
@SpringBootApplication
```

It internally includes:

```
@Configuration
@ComponentScan
@EnableAutoConfiguration
```

---

# @EnableAutoConfiguration

This annotation tells Spring Boot:

```
"Automatically configure beans based on dependencies"
```

Example:

If the project includes:

```
spring-boot-starter-data-jpa
```

Spring Boot loads:

```
HibernateJpaAutoConfiguration
DataSourceAutoConfiguration
TransactionAutoConfiguration
```

---

# Conditional Configuration

Spring Boot uses **Conditional Annotations** to decide whether to configure a bean.

Examples:

| Annotation | Purpose |
|------|------|
| @ConditionalOnClass | Runs if a class exists |
| @ConditionalOnMissingBean | Runs if bean is missing |
| @ConditionalOnProperty | Runs if property exists |

Example:

```java
@ConditionalOnClass(DataSource.class)
@Configuration
public class DataSourceAutoConfiguration {
}
```

This means:

```
If DataSource library exists → configure DataSource
```

---

# Banking Example of Conditional Configuration

If the banking system includes:

```
spring-boot-starter-data-jpa
```

Spring Boot automatically configures:

```
EntityManager
Hibernate
TransactionManager
```

But if the dependency is not present, configuration will **not run**.

---

# Where Auto-Configuration Classes Are Stored

Spring Boot auto-configurations are located in:

```
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

These classes include:

```
DataSourceAutoConfiguration
HibernateJpaAutoConfiguration
WebMvcAutoConfiguration
SecurityAutoConfiguration
```

---

# Example: Web Auto Configuration

When you add:

```
spring-boot-starter-web
```

Spring Boot configures:

```
Embedded Tomcat
DispatcherServlet
JSON converters
RequestMappingHandlerMapping
```

Your controller works instantly.

---

# Real Banking Features Enabled by Auto Configuration

Spring Boot automatically configures features such as:

### REST APIs

```
@RestController
@GetMapping
@PostMapping
```

---

### Database Connectivity

```
DataSource
Hibernate
JPA
Transaction Manager
```

---

### Security

```
Authentication
Authorization
Password Encoding
```

---

### Monitoring

```
Spring Boot Actuator
Health checks
Metrics
```

---

# Advantages of Auto-Configuration

1. Reduces boilerplate configuration
2. Faster development
3. Production-ready defaults
4. Easy integration with third-party libraries
5. Simplifies enterprise application setup

---

# When Developers Override Auto Configuration

Sometimes banking systems require **custom configurations**.

Example:

Custom transaction manager.

```java
@Configuration
public class TransactionConfig {

    @Bean
    public PlatformTransactionManager transactionManager(){
        return new CustomTransactionManager();
    }

}
```

Spring Boot will **use the custom bean instead of the auto-configured one**.

---

# Summary

Spring Boot Auto-Configuration automatically configures application components based on:

- Project dependencies
- Configuration properties
- Environment settings

Key concepts:

```
@SpringBootApplication
@EnableAutoConfiguration
Conditional Configuration
AutoConfiguration Classes
```

In enterprise systems such as **banking platforms**, auto-configuration simplifies the setup of:

- REST APIs
- Databases
- Security
- Transactions
- Monitoring

This allows developers to focus on **business logic instead of infrastructure configuration**.
