# Spring Profiles and Environment-Specific Configurations

## Introduction

In real-world applications, software runs in **different environments**, such as:

- Development
- Testing
- Staging
- Production

Each environment may require **different configurations** such as:

- Database credentials
- API endpoints
- Logging levels
- Feature toggles

Spring provides a powerful feature called **Profiles** that allows developers to **separate configuration based on environment**.

---

# What is a Spring Profile?

A **Spring Profile** is a mechanism that allows you to **activate or deactivate specific beans depending on the environment**.

It helps load **different configurations for different environments**.

Example environments:

```
dev
test
prod
```

---

# Why Profiles are Needed

Different environments require different settings.

Example:

| Environment | Database |
|------|------|
| Development | H2 / Local MySQL |
| Testing | Test Database |
| Production | Production Database |

Without profiles, you would have to manually change configurations each time.

Profiles allow Spring to **automatically load the correct configuration**.

---

# @Profile Annotation

The `@Profile` annotation is used to specify **which environment a bean should be active in**.

## Syntax

```java
@Profile("dev")
```

---

# Example Using @Profile

## Development Configuration

```java
@Configuration
@Profile("dev")
public class DevDatabaseConfig {

    @Bean
    public DataSource dataSource(){
        return new HikariDataSource();
    }

}
```

---

## Production Configuration

```java
@Configuration
@Profile("prod")
public class ProdDatabaseConfig {

    @Bean
    public DataSource dataSource(){
        return new ProductionDataSource();
    }

}
```

Only **one configuration will be loaded depending on the active profile**.

---

# Activating Profiles

Profiles can be activated in several ways.

---

# 1. Using application.properties

```
spring.profiles.active=dev
```

or

```
spring.profiles.active=prod
```

---

# 2. Using Command Line

```
-Dspring.profiles.active=dev
```

Example:

```
java -jar app.jar --spring.profiles.active=prod
```

---

# 3. Using Environment Variables

Example:

```
SPRING_PROFILES_ACTIVE=dev
```

---

# 4. Programmatically

```java
SpringApplication app = new SpringApplication(App.class);
app.setAdditionalProfiles("dev");
app.run(args);
```

---

# Profile-Specific Properties Files

Spring Boot allows **separate configuration files for each profile**.

File structure:

```
application.properties
application-dev.properties
application-test.properties
application-prod.properties
```

Example:

---

## application-dev.properties

```
server.port=8081
spring.datasource.url=jdbc:mysql://localhost/devdb
spring.datasource.username=root
spring.datasource.password=root
```

---

## application-prod.properties

```
server.port=8080
spring.datasource.url=jdbc:mysql://prod-server/proddb
spring.datasource.username=admin
spring.datasource.password=securepassword
```

Spring loads the correct file based on the **active profile**.

---

# Example Spring Boot Flow

```
Application Start
        ↓
Check Active Profile
        ↓
Load application-{profile}.properties
        ↓
Create Beans based on @Profile
        ↓
Application Ready
```

---

# Multiple Profiles

Spring allows activating **multiple profiles simultaneously**.

Example:

```
spring.profiles.active=dev,cloud
```

This loads configurations for both profiles.

---

# Default Profile

If no profile is specified, Spring uses the **default profile**.

You can define default configuration using:

```
application.properties
```

or

```
@Profile("default")
```

Example:

```java
@Profile("default")
@Component
public class DefaultConfig {
}
```

---

# Environment Interface

Spring provides the `Environment` interface to access profiles and properties programmatically.

Example:

```java
@Autowired
private Environment environment;

public void printProfile(){
    System.out.println(Arrays.toString(environment.getActiveProfiles()));
}
```

---

# Benefits of Spring Profiles

1. Clean separation of configurations
2. Easy environment switching
3. Improves security (production credentials separated)
4. Reduces manual configuration changes
5. Supports multiple environments easily

---

# Best Practices

Use profiles for:

- Database configuration
- Logging levels
- External service URLs
- Feature flags
- Security settings

Common profiles:

```
dev
test
staging
prod
```

---

# Summary

Spring Profiles allow applications to **load different configurations based on the active environment**.

Key concepts:

- `@Profile` annotation activates beans for specific environments
- `spring.profiles.active` selects the active profile
- Profile-specific configuration files allow environment-based settings

Profiles are widely used in **Spring Boot applications to manage environment-specific configurations efficiently**.
