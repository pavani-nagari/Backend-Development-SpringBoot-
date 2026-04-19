# Spring Data JPA Repository Patterns (CrudRepository vs JpaRepository)


---

# 🔹 What is Repository Pattern?

The Repository Pattern is used to **abstract database access logic** so developers do not need to write SQL queries manually.

Instead of:
- Writing SQL queries  
- Managing database connections  
- Handling CRUD logic manually  

We define an interface, and Spring automatically implements it.

---

## 🏦 Why it matters in Financial Systems

Financial applications (banking, payments, trading) require:
- High data integrity  
- Fast access to transactions  
- Auditability and traceability  
- Scalable data access  

Repository pattern helps by:
- Reducing boilerplate code  
- Standardizing data access  
- Improving maintainability  
- Supporting scalable query operations  

---

# 🔹 1. CrudRepository

## What it provides

Basic CRUD operations:
- Create
- Read
- Update
- Delete

---

## Key Methods

```java
save(entity)
findById(id)
findAll()
deleteById(id)
existsById(id)
```

---

## 💰 Financial Example (Transaction Repository)

```java
import org.springframework.data.repository.CrudRepository;

public interface TransactionRepository 
        extends CrudRepository<Transaction, Long> {
}
```

---

## 🔍 Usage Example

```java
@Autowired
private TransactionRepository transactionRepository;

// Create
Transaction tx = new Transaction();
transactionRepository.save(tx);

// Read
Optional<Transaction> tx = transactionRepository.findById(1L);

// Delete
transactionRepository.deleteById(1L);
```

---

## ⚠️ Limitations

- Returns `Iterable` instead of `List`
- No pagination support
- No sorting support
- Limited for enterprise-grade systems

👉 Suitable only for simple applications.

---

# 🔹 2. JpaRepository

## What it provides

Extends:
- `CrudRepository`
- `PagingAndSortingRepository`

### Adds:
- Pagination
- Sorting
- Batch operations
- Flush control

---

## Key Methods

```java
findAll(Pageable pageable)
findAll(Sort sort)
saveAndFlush(entity)
deleteAllInBatch()
```

---

## 💰 Financial Example (Production Repository)

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface TransactionRepository 
        extends JpaRepository<Transaction, Long> {
}
```

---

## 🔍 Usage Example

---

### 📄 Pagination (Important for finance dashboards)

```java
Page<Transaction> page = transactionRepository.findAll(
    PageRequest.of(0, 10)
);
```

👉 Example:
- Fetch latest 10 transactions for an account

---

### 🔽 Sorting

```java
List<Transaction> txs = transactionRepository.findAll(
    Sort.by("createdAt").descending()
);
```

👉 Example:
- Show latest transactions first in banking apps

---

### ⚡ Batch Delete

```java
transactionRepository.deleteAllInBatch();
```

👉 Example:
- Archive old transaction records

---

# 🔹 Custom Query Methods

Spring Data JPA automatically generates queries based on method names.

---

## 💰 Examples

### Find by account number

```java
List<Transaction> findByAccountNumber(String accountNumber);
```

---

### Find by status

```java
List<Transaction> findByStatus(String status);
```

---

### Find recent transactions

```java
List<Transaction> findByCreatedAtAfter(LocalDateTime date);
```

---

### Combined conditions

```java
List<Transaction> findByAccountNumberAndStatus(
    String accountNumber,
    String status
);
```

👉 Example:
- Retrieve all FAILED transactions for a specific account

---

# 🔹 Custom JPQL Query

Used when method names are not enough.

```java
@Query("SELECT t FROM Transaction t WHERE t.amount > :amount")
List<Transaction> findHighValueTransactions(
    @Param("amount") BigDecimal amount
);
```

👉 Example:
- Detect large transactions for fraud analysis

---

# 🔁 CrudRepository vs JpaRepository

| Feature | CrudRepository | JpaRepository |
|--------|---------------|--------------|
| Basic CRUD | ✅ | ✅ |
| Pagination | ❌ | ✅ |
| Sorting | ❌ | ✅ |
| Batch operations | ❌ | ✅ |
| Enterprise readiness | ❌ | ✅ |

---

# 🧠 Best Practice (Finance Systems)

✔ Always prefer:

```java
JpaRepository
```

Because:
- Supports large datasets
- Enables pagination (critical for dashboards)
- Provides sorting and batching
- Better performance control

---

# ⚠️ Common Mistakes

## ❌ Using CrudRepository in large systems
Leads to:
- Poor scalability
- Performance issues
- Lack of pagination

---

## ❌ Fetching all data

```java
findAll();
```

👉 Dangerous in financial systems:
- Can overload memory
- Slow performance

✔ Use pagination instead.

---

## ❌ No indexing strategy
Leads to slow queries in:
- Transaction search
- Fraud detection systems

---

# 🏁 Summary

- **CrudRepository** → Basic CRUD operations (simple apps)
- **JpaRepository** → Full-featured enterprise repository
- Supports:
  - Pagination
  - Sorting
  - Batch operations
  - Custom queries

---

