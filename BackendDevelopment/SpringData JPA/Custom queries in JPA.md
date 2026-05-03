# Custom Queries in Spring Data JPA (@Query)

Custom queries using `@Query` in Spring Data JPA allow developers to write precise database queries using either:
- **JPQL (Java Persistence Query Language)**
- **Native SQL**

This is especially useful in financial systems where complex logic like profit calculation, fraud detection, and portfolio aggregation is required.

---

## 🔹 1. What is `@Query`?

`@Query` allows you to define custom queries directly in your repository interface.

```java
@Query("SELECT a FROM Account a WHERE a.balance > :amount")
List<Account> findRichAccounts(@Param("amount") BigDecimal amount);
```

---

## 🔹 2. JPQL (Java Persistence Query Language)

### ✅ What is JPQL?
- Works with **entities (Java objects)**, not database tables
- Database-independent
- Uses object-oriented syntax

---

### 📊 Financial Example: High-Value Accounts

#### Entity:
```java
@Entity
class Account {
    @Id
    Long id;
    String ownerName;
    BigDecimal balance;
}
```

#### JPQL Query:
```java
@Query("SELECT a FROM Account a WHERE a.balance > :amount")
List<Account> findAccountsWithBalanceGreaterThan(@Param("amount") BigDecimal amount);
```

#### Usage:
```java
findAccountsWithBalanceGreaterThan(new BigDecimal("10000"));
```

#### Behind the scenes:

JPQL:
```sql
SELECT a FROM Account a WHERE a.balance > 10000
```

SQL (generated):
```sql
SELECT * FROM account WHERE balance > 10000;
```

---

### 📊 Financial Example: Total Portfolio Value

```java
@Query("SELECT SUM(a.balance) FROM Account a")
BigDecimal getTotalBankHoldings();
```

💡 Calculates total assets under management.

---

## 🔹 3. Native SQL Queries

### ✅ What is Native SQL?
- Uses actual database SQL
- Database-specific
- More powerful and flexible

---

### 📊 Financial Example: High Balance Accounts

```java
@Query(value = "SELECT * FROM account WHERE balance > :amount", nativeQuery = true)
List<Account> findRichAccountsNative(@Param("amount") BigDecimal amount);
```

---

### 📊 Financial Example: Fraud Detection

```java
@Query(value = """
    SELECT * FROM transactions 
    WHERE amount > :amount 
    AND transaction_date > NOW() - INTERVAL '1 DAY'
    """, nativeQuery = true)
List<Transaction> findSuspiciousTransactions(@Param("amount") BigDecimal amount);
```

💡 Detects large transactions within the last 24 hours.

---

### 📊 Financial Example: Profit Calculation

```java
@Query(value = """
    SELECT SUM(revenue - cost) 
    FROM financial_records 
    WHERE year = :year
    """, nativeQuery = true)
BigDecimal calculateAnnualProfit(@Param("year") int year);
```

---

## 🔹 4. JPQL vs Native SQL

| Feature | JPQL | Native SQL |
|--------|------|-----------|
| Works on | Entities (objects) | Tables |
| Portability | ✅ Yes | ❌ No |
| Syntax | Object-oriented | SQL |
| Flexibility | Limited | High |
| Use case | Business logic | Complex queries |

---

## 🔹 5. When to Use What?

### ✅ Use JPQL when:
- You want clean, maintainable code
- You work with entity relationships
- You want database independence

👉 Example: Find accounts owned by users in a specific region

---

### ✅ Use Native SQL when:
- You need complex joins or window functions
- You require performance optimization
- You use database-specific features

👉 Example:
- Ranking customers by net worth
- Fraud detection
- Financial analytics

---

## 🔹 6. Advanced Financial Scenario

### 📊 Top 5 Customers by Net Worth

```java
@Query(value = """
    SELECT owner_name, SUM(balance) as total_balance
    FROM account
    GROUP BY owner_name
    ORDER BY total_balance DESC
    LIMIT 5
    """, nativeQuery = true)
List<Object[]> findTopCustomers();
```

---

## 🔹 7. Named vs Positional Parameters

### Named Parameters (Recommended)
```java
@Query("SELECT a FROM Account a WHERE a.balance > :amount")
```

### Positional Parameters
```java
@Query("SELECT a FROM Account a WHERE a.balance > ?1")
```

---

## 🔹 8. Modifying Queries (Important for Financial Systems)

### Example: Debit Account

```java
@Modifying
@Query("UPDATE Account a SET a.balance = a.balance - :amount WHERE a.id = :id")
void debitAccount(@Param("id") Long id, @Param("amount") BigDecimal amount);
```

💡 Used in:
- Withdrawals
- Transfers
- Fee deductions

---

## 🔚 Key Takeaways

- **JPQL** → Best for business-level queries using entities  
- **Native SQL** → Best for complex, performance-critical queries  

Financial systems typically use both:
- JPQL for daily operations  
- Native SQL for analytics and reporting  

---

## 🚀 Summary

| Scenario | Recommended Approach |
|---------|---------------------|
| Simple queries | JPQL |
| Complex analytics | Native SQL |
| Database portability | JPQL |
| Performance tuning | Native SQL |

---

## 📌 Next Steps (Optional)

You can extend this into:
- Transaction management  
- Concurrency handling (critical in finance)  
- Real-time fraud detection systems  
