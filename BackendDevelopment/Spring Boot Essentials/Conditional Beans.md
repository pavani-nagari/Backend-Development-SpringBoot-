# Spring Boot Conditional Beans
## Using @ConditionalOnProperty and Other Conditional Annotations

## Introduction

In large applications, some components should only be created **under specific conditions**.

For example:

```
Enable payment gateway only in production
Enable mock services in development
Enable fraud detection only if configured
```

Spring Boot provides **Conditional Beans** to solve this.

Conditional beans allow Spring to **create beans only when certain conditions are satisfied**.

This helps in:

- Environment-based configuration
- Feature toggling
- Microservices flexibility
- Testing vs production behavior

---

# What Are Conditional Beans?

Conditional beans are **Spring beans that are loaded only if certain conditions are met**.

Spring Boot provides many conditional annotations such as:

```
@ConditionalOnProperty
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnBean
@ConditionalOnExpression
@ConditionalOnResource
```

These annotations are heavily used in **Spring Boot Auto-Configuration**.

---

# 1. @ConditionalOnProperty

## What is it?

This annotation creates a bean **only if a specific property exists and has a certain value**.

---

# Example Syntax

```java
@ConditionalOnProperty(
    name = "feature.payment.enabled",
    havingValue = "true",
    matchIfMissing = false
)
```

Parameters:

| Parameter | Meaning |
|------|------|
| name | Property name |
| havingValue | Required value |
| matchIfMissing | If property is missing |

---

# Banking Example: Payment Gateway Feature Toggle

Suppose a bank wants to **enable or disable payment gateway integration** through configuration.

---

# application.properties

```
feature.payment.enabled=true
```

---

# Payment Service Bean

```java
@Service
@ConditionalOnProperty(
        name = "feature.payment.enabled",
        havingValue = "true"
)
public class PaymentGatewayService {

    public void processPayment() {
        System.out.println("Processing payment through external gateway");
    }

}
```

---

# Behavior

If:

```
feature.payment.enabled=true
```

Bean will be created.

If:

```
feature.payment.enabled=false
```

Bean will **NOT be loaded into Spring context**.

---

# Banking Use Case

Banks use this for:

```
Enable fraud detection
Enable payment gateway
Enable analytics module
Enable experimental features
```

---

# 2. @ConditionalOnMissingBean

## What is it?

Creates a bean **only if another bean of the same type does not already exist**.

---

# Example

```java
@Bean
@ConditionalOnMissingBean
public NotificationService notificationService(){
    return new EmailNotificationService();
}
```

---

# Banking Example

A bank may allow **custom notification services**.

If none is provided, Spring loads a **default email service**.

```
Default Notification → Email
Custom Notification → SMS or Push Notification
```

---

# 3. @ConditionalOnBean

## What is it?

Loads a bean **only if another specific bean exists**.

---

# Example

```java
@Bean
@ConditionalOnBean(DataSource.class)
public TransactionRepository transactionRepository(){
    return new TransactionRepository();
}
```

---

# Banking Example

Transaction repository should only load **if a database connection exists**.

```
DataSource Bean exists
       ↓
TransactionRepository Bean loads
```

---

# 4. @ConditionalOnClass

## What is it?

Creates a bean **only if a specific class exists on the classpath**.

Used heavily in **Spring Boot auto-configuration**.

---

# Example

```java
@ConditionalOnClass(name = "com.mysql.cj.jdbc.Driver")
@Configuration
public class MySQLConfig {
}
```

---

# Banking Example

If MySQL driver is present:

```
MySQL configuration loads
```

If PostgreSQL driver is present:

```
PostgreSQL configuration loads
```

This allows Spring Boot to **auto-detect dependencies**.

---

# 5. @ConditionalOnExpression

## What is it?

Evaluates **Spring Expression Language (SpEL)** to decide whether to create a bean.

---

# Example

```java
@Bean
@ConditionalOnExpression("${bank.transaction.limit} > 5000")
public HighValueTransactionMonitor monitor(){
    return new HighValueTransactionMonitor();
}
```

---

# Banking Example

Monitor transactions **only if the limit exceeds a threshold**.

---

# 6. @ConditionalOnResource

## What is it?

Creates a bean **only if a specific resource exists**.

---

# Example

```java
@ConditionalOnResource(resources = "classpath:fraud-rules.yml")
@Bean
public FraudDetectionService fraudDetectionService(){
    return new FraudDetectionService();
}
```

---

# Banking Example

If fraud detection rule file exists:

```
fraud-rules.yml
```

Fraud detection service loads.

Otherwise, it is disabled.

---

# Real Banking System Example

A banking application may have multiple optional modules:

```
Payment Gateway
Fraud Detection
Notification Service
Analytics Engine
```

Configuration file:

### application.yml

```
feature:
  payment:
    enabled: true
  fraud:
    enabled: false
```

Behavior:

```
Payment Service → Loaded
Fraud Detection → Disabled
```

This makes systems **modular and configurable**.

---

# Why Conditional Beans Are Important

Conditional beans are heavily used for:

```
Feature toggles
Environment-specific behavior
Microservices configuration
Auto-configuration frameworks
Testing and mocking
```

---

# Spring Boot Auto-Configuration Uses Conditional Beans

Spring Boot internally uses these annotations.

Example:

```
If Spring MVC is present
      ↓
Enable Web configuration

If JDBC dependency exists
      ↓
Enable DataSource configuration
```

This is how **Spring Boot automatically configures applications**.

---

# Advantages

1. Flexible application configuration
2. Enables feature toggles
3. Supports modular architecture
4. Simplifies environment-specific setup
5. Reduces manual configuration

---

# Summary

Conditional beans allow Spring Boot to **create beans dynamically based on conditions**.

Common annotations include:

```
@ConditionalOnProperty
@ConditionalOnBean
@ConditionalOnMissingBean
@ConditionalOnClass
@ConditionalOnExpression
@ConditionalOnResource
```

These are essential in **enterprise applications like banking systems**, where features must be enabled or disabled depending on configuration and environment.
