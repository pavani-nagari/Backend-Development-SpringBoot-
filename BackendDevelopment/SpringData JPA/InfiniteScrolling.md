# Infinite Scrolling APIs (Spring Boot + REST)

Infinite scrolling APIs are used to load data continuously as the user scrolls, instead of using page numbers. This approach is widely used in modern apps like feeds, dashboards, and financial transaction histories.

---

# 🔹 1. What is Infinite Scrolling?

Instead of traditional pagination:

```
Page 1 → Page 2 → Page 3
```

Infinite scrolling loads data like:

```
Scroll ↓ → More data loads automatically → Scroll ↓ → More data loads
```

---

# 🔹 2. How It Works

1. Client loads initial data (first batch)
2. User scrolls down
3. Client requests next batch
4. Server returns next set of results
5. Repeat until no data left

---

# 🔹 3. Types of Infinite Scrolling APIs

## ✅ 1. Offset-Based (Simple but less efficient)

```http
GET /transactions?page=0&size=10
GET /transactions?page=1&size=10
```

✔ Easy to implement  
❌ Performance issues for large datasets  
❌ Can cause duplicate/missing data if records change  

---

## ✅ 2. Cursor-Based (Recommended)

Uses a cursor (like last seen ID or timestamp):

```http
GET /transactions?lastId=100&size=10
```

✔ Stable results  
✔ Better performance  
✔ Used in production systems  

---

# 🔹 4. Financial Example (Cursor-Based)

### 📊 Entity

```java
@Entity
class Transaction {
    @Id
    Long id;
    BigDecimal amount;
    LocalDateTime transactionDate;
}
```

---

### 📊 Repository Query

```java
@Query("""
    SELECT t FROM Transaction t
    WHERE t.id < :lastId
    ORDER BY t.id DESC
""")
List<Transaction> findNextTransactions(
    @Param("lastId") Long lastId,
    Pageable pageable
);
```

---

### 📊 Service Layer

```java
public List<Transaction> getNextTransactions(Long lastId, int size) {
    Pageable pageable = PageRequest.of(0, size);
    return repository.findNextTransactions(lastId, pageable);
}
```

---

### 📊 REST API Controller

```java
@GetMapping("/transactions/scroll")
public List<Transaction> scrollTransactions(
    @RequestParam(required = false) Long lastId,
    @RequestParam(defaultValue = "10") int size
) {
    if (lastId == null) {
        return repository.findAll(PageRequest.of(0, size)).getContent();
    }
    return service.getNextTransactions(lastId, size);
}
```

---

### 📊 API Flow

#### First request:
```http
GET /transactions/scroll
```

#### Response:
```json
[
  { "id": 100, "amount": 5000 },
  { "id": 99, "amount": 2000 }
]
```

#### Next request:
```http
GET /transactions/scroll?lastId=99
```

---

# 🔹 5. Why Cursor-Based is Better

| Feature | Offset-Based | Cursor-Based |
|--------|-------------|-------------|
| Performance | Slower for large data | Fast |
| Stability | Can shift results | Stable |
| Duplicates | Possible | Avoided |
| Scaling | Poor | Excellent |

---

# 🔹 6. Real Financial Use Cases

### 📊 1. Transaction History
- Scroll through past transactions efficiently

### 📊 2. Fraud Monitoring Dashboard
- Continuously load suspicious activity logs

### 📊 3. Trading Systems
- Stream stock transactions or orders

---

# 🔹 7. Advantages

✔ Smooth user experience  
✔ High performance for large datasets  
✔ No page navigation needed  
✔ Ideal for real-time systems  

---

# 🔹 8. Challenges

❌ Harder than pagination  
❌ Requires stable sorting (ID or timestamp)  
❌ No direct page jumping (like page 10)  

---

# 🔹 9. Best Practices

✔ Always sort by indexed column (id or timestamp)  
✔ Use descending order for feeds  
✔ Keep page size small (10–50 records)  
✔ Ensure stable ordering  

---

# 🔚 Key Takeaways

- Infinite scrolling = **modern alternative to pagination**
- Cursor-based approach is the **industry standard**
- Very useful in **financial systems, feeds, and dashboards**
- Provides better UX and scalability

---

# 🚀 Summary

| Approach | Use Case |
|----------|----------|
| Offset pagination | Simple apps |
| Cursor-based scrolling | Production-grade systems |
| Infinite scrolling | Feeds, dashboards, financial apps |

---
