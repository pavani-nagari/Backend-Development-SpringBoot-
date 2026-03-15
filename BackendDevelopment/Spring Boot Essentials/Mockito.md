# Mockito in Spring Boot Testing

## Introduction

**Mockito** is a popular Java testing framework used to **mock dependencies during unit testing**.

Mocking means creating **fake implementations of objects** so that we can test a class **in isolation** without relying on real dependencies such as:

```
Database
External APIs
Payment gateways
Microservices
```

Mockito is commonly used together with:

```
JUnit
Spring Boot Test
@WebMvcTest
@SpringBootTest
```

---

# Why Mockito is Important

In real-world applications like **banking systems**, services depend on many other components.

Example:

```
TransactionController
        ↓
TransactionService
        ↓
TransactionRepository
        ↓
Database
```

When testing **TransactionService**, we don't want to access the real database.

Instead we **mock TransactionRepository** using Mockito.

---

# Adding Mockito Dependency

Spring Boot testing starter already includes Mockito.

### Maven

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-test</artifactId>
 <scope>test</scope>
</dependency>
```

This dependency includes:

```
JUnit
Mockito
Spring Test Framework
AssertJ
```

---

# Core Mockito Annotations

| Annotation | Purpose |
|------|------|
| @Mock | Creates a mock object |
| @InjectMocks | Injects mock dependencies |
| @MockBean | Creates mock inside Spring context |
| Mockito.when() | Defines mock behavior |
| Mockito.verify() | Verifies method calls |

---

# 1. @Mock

`@Mock` creates a **fake object** of a class.

Example:

```java
@Mock
private TransactionRepository transactionRepository;
```

This creates a **mock repository instead of a real database connection**.

---

# 2. @InjectMocks

`@InjectMocks` creates the class being tested and **injects the mock dependencies into it**.

Example:

```java
@InjectMocks
private TransactionService transactionService;
```

Spring injects the mocked repository into the service.

---

# Banking Example

### TransactionService

```java
@Service
public class TransactionService {

    @Autowired
    private TransactionRepository transactionRepository;

    public boolean processTransaction(double amount){

        if(amount <= 0){
            return false;
        }

        transactionRepository.save(new Transaction(amount));

        return true;
    }

}
```

---

# Mockito Test Example

```java
@ExtendWith(MockitoExtension.class)
class TransactionServiceTest {

    @Mock
    private TransactionRepository transactionRepository;

    @InjectMocks
    private TransactionService transactionService;

    @Test
    void testTransactionProcessing(){

        boolean result = transactionService.processTransaction(500);

        assertTrue(result);

        Mockito.verify(transactionRepository)
               .save(Mockito.any(Transaction.class));
    }

}
```

---

# Explanation

```
@Mock → creates fake repository
@InjectMocks → injects repository into service
verify() → ensures save() was called
```

This tests **business logic without accessing a real database**.

---

# 3. Mockito.when()

Used to define **mock behavior**.

Example:

```java
Mockito.when(transactionRepository.findById(1L))
       .thenReturn(Optional.of(new Transaction(500)));
```

This means:

```
If repository.findById(1L) is called
Return fake transaction object
```

---

# Banking Example

### Service Method

```java
public Transaction getTransaction(Long id){
    return transactionRepository.findById(id).orElse(null);
}
```

---

### Test with Mockito

```java
@Test
void testGetTransaction(){

    Transaction transaction = new Transaction(500);

    Mockito.when(transactionRepository.findById(1L))
           .thenReturn(Optional.of(transaction));

    Transaction result = transactionService.getTransaction(1L);

    assertEquals(500, result.getAmount());

}
```

---

# 4. Mockito.verify()

`verify()` ensures that a **specific method was called**.

Example:

```java
Mockito.verify(transactionRepository).save(Mockito.any());
```

This confirms that **save() was invoked** during the test.

---

# Verify Number of Calls

Example:

```java
Mockito.verify(transactionRepository, Mockito.times(1))
       .save(Mockito.any());
```

Other options:

```
times(n)
never()
atLeastOnce()
```

---

# 5. @MockBean (Spring Boot)

`@MockBean` is used when testing **Spring Boot context**.

It replaces a bean in the Spring container with a **mock object**.

---

# Example with Controller Testing

### Controller

```java
@RestController
@RequestMapping("/transactions")
public class TransactionController {

    @Autowired
    private TransactionService transactionService;

    @GetMapping("/{id}")
    public Transaction getTransaction(@PathVariable Long id){
        return transactionService.getTransaction(id);
    }

}
```

---

# Test with @WebMvcTest

```java
@WebMvcTest(TransactionController.class)
class TransactionControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private TransactionService transactionService;

    @Test
    void testGetTransaction() throws Exception {

        Transaction transaction = new Transaction(500);

        Mockito.when(transactionService.getTransaction(1L))
               .thenReturn(transaction);

        mockMvc.perform(get("/transactions/1"))
               .andExpect(status().isOk());

    }

}
```

---

# Real Banking Example

Mockito helps simulate:

```
Payment gateway responses
Fraud detection results
Database queries
Third-party APIs
```

Example:

```
Mock payment gateway success
Mock fraud detection failure
Mock transaction approval
```

This allows **safe testing without touching real systems**.

---

# Advantages of Mockito

```
Isolates business logic
Eliminates dependency on databases
Speeds up testing
Supports behavior verification
Simplifies unit testing
```

---

# Mockito vs Real Dependencies

Without Mockito:

```
Service → Repository → Database
```

With Mockito:

```
Service → Mock Repository (Fake)
```

This makes tests **fast and reliable**.

---

# Summary

Mockito is a powerful testing framework used for **mocking dependencies**.

Key concepts:

```
@Mock → creates fake objects
@InjectMocks → injects mocks
@MockBean → replaces Spring beans
Mockito.when() → define behavior
Mockito.verify() → check interactions
```

Mockito is widely used in **Spring Boot applications**, especially in **enterprise systems like banking platforms**, where services depend on multiple external components.
