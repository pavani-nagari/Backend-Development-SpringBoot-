# Pagination and Sorting in Spring Boot
## Spring Data JPA (with Financial Examples)

Pagination and sorting are essential in real-world applications, especially in **financial systems**, where datasets can be massive (millions of transactions, accounts, etc.).

They help:
- Improve performance
- Reduce memory usage
- Provide better user experience

---

## 🔹 1. What is Pagination?

Pagination means dividing data into **pages** instead of fetching everything at once.

### 📊 Financial Example:
Instead of loading **1 million transactions**, load:
- 20 transactions per page

---

## 🔹 2. Spring Data JPA Support

Spring provides built-in support using:
- `Pageable`
- `Page`
- `Slice`

---

## 🔹 3. Basic Pagination Example

### Entity:
```java
@Entity
class Transaction {
    @Id
    Long id;
    BigDecimal amount;
    String type;
    LocalDateTime transactionDate;
}
```

---

### Repository:
```java
public interface TransactionRepository extends JpaRepository<Transaction, Long> {

    Page<Transaction> findAll(Pageable pageable);
}
```

---

### Service Usage:
```java
Pageable pageable = PageRequest.of(0, 10); // page 0, size 10
Page<Transaction> page = transactionRepository.findAll(pageable);

List<Transaction> transactions = page.getContent();
```

---

### 💡 Output:
- Page 0 → First 10 transactions
- Page 1 → Next 10 transactions

---

## 🔹 4. Understanding `Page`

```java
Page<Transaction> page = repository.findAll(pageable);
```

### Useful Methods:
```java
page.getContent();        // List of data
page.getTotalElements(); // Total records
page.getTotalPages();    // Total pages
page.getNumber();        // Current page
page.hasNext();          // Next page exists?
```

---

## 🔹 5. Pagination with Sorting

### 📊 Financial Example:
Sort transactions by **amount descending**

```java
Pageable pageable = PageRequest.of(
    0, 
    10, 
    Sort.by("amount").descending()
);
```

---

### Multiple Sorting:
```java
Pageable pageable = PageRequest.of(
    0,
    10,
    Sort.by("amount").descending()
        .and(Sort.by("transactionDate").ascending())
);
```

---

## 🔹 6. Custom Query with Pagination

```java
@Query("SELECT t FROM Transaction t WHERE t.amount > :amount")
Page<Transaction> findHighValueTransactions(
    @Param("amount") BigDecimal amount,
    Pageable pageable
);
```

---

### Usage:
```java
Pageable pageable = PageRequest.of(0, 5);
Page<Transaction> result = repo.findHighValueTransactions(
    new BigDecimal("10000"),
    pageable
);
```

---

## 🔹 7. Native Query with Pagination

```java
@Query(
    value = "SELECT * FROM transactions WHERE amount > :amount",
    countQuery = "SELECT COUNT(*) FROM transactions WHERE amount > :amount",
    nativeQuery = true
)
Page<Transaction> findHighValueTransactionsNative(
    BigDecimal amount,
    Pageable pageable
);
```

💡 `countQuery` is required for pagination metadata.

---

## 🔹 8. REST API Example

### Controller:
```java
@GetMapping("/transactions")
public Page<Transaction> getTransactions(
    @RequestParam int page,
    @RequestParam int size
) {
    Pageable pageable = PageRequest.of(page, size);
    return transactionRepository.findAll(pageable);
}
```

---

### API Call:
```
GET /transactions?page=0&size=10
```

---

## 🔹 9. Sorting via API

```java
@GetMapping("/transactions")
public Page<Transaction> getTransactions(
    @RequestParam int page,
    @RequestParam int size,
    @RequestParam String sortBy
) {
    Pageable pageable = PageRequest.of(
        page,
        size,
        Sort.by(sortBy).descending()
    );
    return repository.findAll(pageable);
}
```

---

### API Call:
```
GET /transactions?page=0&size=10&sortBy=amount
```

---

## 🔹 10. Slice vs Page

### Page:
- Includes total count
- Slightly slower

### Slice:
- No total count
- Faster

```java
Slice<Transaction> findByAmountGreaterThan(
    BigDecimal amount,
    Pageable pageable
);
```

---

## 🔹 11. Real Financial Use Cases

### 📊 1. Transaction History
- Paginate transactions for a user
- Sort by latest first

---

### 📊 2. Fraud Monitoring Dashboard
- Show high-value transactions
- Paginated + sorted by risk level

---

### 📊 3. Customer Accounts List
- Sort by balance descending
- Paginated for UI performance

---

## 🔹 12. Best Practices

✔ Always use pagination for large datasets  
✔ Keep page size reasonable (10–50)  
✔ Use indexes for sorted columns  
✔ Avoid very large page numbers (performance hit)  

---

## 🔹 13. Common Mistakes

❗ Fetching all data without pagination  
❗ Missing `countQuery` in native queries  
❗ Sorting on non-indexed columns  
❗ Using very large page sizes  

---

## 🔚 Key Takeaways

- Pagination improves performance and scalability  
- Sorting enhances usability  
- `Pageable` is the core interface  
- Combine pagination + sorting for best results  

---

## 🚀 Summary

| Feature | Purpose |
|--------|--------|
| Pagination | Limit data per request |
| Sorting | Order results |
| Page | Full metadata |
| Slice | Lightweight pagination |

---
 
