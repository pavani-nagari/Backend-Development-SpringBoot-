# Spring Boot Testing
## Using @SpringBootTest and @WebMvcTest

## Introduction

Testing is a critical part of building reliable applications.  
Spring Boot provides powerful testing support through:

```
JUnit
Mockito
Spring Test Framework
```

Spring Boot testing allows developers to test:

```
Controllers
Services
Repositories
Full application context
REST APIs
```

Two commonly used annotations are:

```
@SpringBootTest
@WebMvcTest
```

These annotations help create **different levels of tests** depending on what part of the application needs testing.

---

# Types of Testing in Spring Boot

Spring Boot supports several testing levels:

```
Unit Testing
Integration Testing
Web Layer Testing
Repository Testing
```

Example in a banking system:

```
Controller → Test API endpoints
Service → Test business logic
Repository → Test database interaction
Full System → Test complete application flow
```

---

# 1. @SpringBootTest

## What is @SpringBootTest?

`@SpringBootTest` loads the **entire Spring Boot application context**.

This means Spring will start:

```
All Beans
All Configurations
All Services
Database connections
Security configurations
```

This type of test is typically used for **integration testing**.

---

# Example Banking Application

Suppose we have a **transaction service**.

```
TransactionController
TransactionService
TransactionRepository
```

---

# Example Test

```java
@SpringBootTest
class TransactionServiceTest {

    @Autowired
    private TransactionService transactionService;

    @Test
    void testTransactionProcessing() {

        boolean result = transactionService.processTransaction(500);

        assertTrue(result);
    }
}
```

---

# What Happens Internally?

```
Spring Boot starts application context
        ↓
All beans are loaded
        ↓
Test interacts with real services
```

---

# Banking Example

Test transaction processing:

```
Deposit
Withdrawal
Balance validation
Fraud checks
```

This verifies **end-to-end business logic**.

---

# Advantages of @SpringBootTest

```
Tests full application behavior
Validates real configurations
Good for integration testing
```

---

# Disadvantages

```
Slower tests
Loads entire application context
Higher memory usage
```

---

# 2. @WebMvcTest

## What is @WebMvcTest?

`@WebMvcTest` is used to test **only the web layer**.

It loads:

```
Controllers
Spring MVC configuration
Filters
Jackson configuration
```

But it **does NOT load**:

```
Services
Repositories
Database connections
```

This makes it **much faster than @SpringBootTest**.

---

# Banking Controller Example

Suppose we have:

```
TransactionController
```

---

# Controller Code

```java
@RestController
@RequestMapping("/transactions")
public class TransactionController {

    @GetMapping("/status")
    public String getStatus(){
        return "Transaction Service Running";
    }

}
```

---

# Test Using @WebMvcTest

```java
@WebMvcTest(TransactionController.class)
class TransactionControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testTransactionStatus() throws Exception {

        mockMvc.perform(get("/transactions/status"))
                .andExpect(status().isOk())
                .andExpect(content().string("Transaction Service Running"));
    }

}
```

---

# What Happens Internally?

```
Spring loads only web layer
        ↓
Controller is tested
        ↓
Service and repository layers are ignored
```

---

# Mocking Dependencies

Since services are not loaded, we usually **mock them** using Mockito.

Example:

```java
@MockBean
private TransactionService transactionService;
```

---

# Banking Example

Testing APIs such as:

```
GET /accounts/{id}
POST /transactions
GET /transactions/history
```

Example:

```java
mockMvc.perform(get("/accounts/1001"))
       .andExpect(status().isOk());
```

---

# MockMvc

`MockMvc` is used to simulate **HTTP requests without starting a real server**.

Example operations:

```
GET requests
POST requests
PUT requests
DELETE requests
```

Example:

```java
mockMvc.perform(post("/transactions"))
       .andExpect(status().isCreated());
```

---

# Comparison

| Feature | @SpringBootTest | @WebMvcTest |
|------|------|------|
| Loads full application | Yes | No |
| Loads controllers | Yes | Yes |
| Loads services | Yes | No |
| Loads repositories | Yes | No |
| Speed | Slower | Faster |
| Use case | Integration tests | Web layer tests |

---

# Real Banking Testing Strategy

Large banking applications use a **testing pyramid**:

```
Unit Tests (Service Logic)
        ↓
Controller Tests (@WebMvcTest)
        ↓
Integration Tests (@SpringBootTest)
```

Example:

```
Test transaction API
Test fraud detection logic
Test payment gateway integration
Test database operations
```

---

# Example Banking Test Flow

```
Client sends transaction request
        ↓
TransactionController
        ↓
TransactionService
        ↓
FraudDetectionService
        ↓
TransactionRepository
        ↓
Database
```

`@SpringBootTest` verifies the **entire flow works correctly**.

---

# Advantages of Spring Boot Testing

```
Improves reliability
Detects bugs early
Supports automated testing
Ensures system stability
```

---

# Summary

Spring Boot provides powerful testing support through annotations such as:

```
@SpringBootTest
@WebMvcTest
```

### @SpringBootTest
Used for **integration testing with full application context**.

### @WebMvcTest
Used for **testing only the web layer (controllers)**.

In large systems like **banking platforms**, both testing approaches are essential to ensure:

```
API reliability
Business logic correctness
System stability
```
