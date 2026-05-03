# Projections in Spring Data JPA
## Interface-Based Projections

Projections in Spring Data JPA allow you to fetch **only the required data** instead of the entire entity. 
---

## 🔹 1. What are Projections?

A **projection** is a way to retrieve a subset of fields from an entity.

Instead of loading the full entity:
```java
class Account {
    Long id;
    String ownerName;
    BigDecimal balance;
    String accountType;
    String branch;
}
```

You can fetch only what you need, like:
- ownerName
- balance

---

## 🔹 2. Interface-Based Projections

Spring Data JPA allows you to define projections using **interfaces**.

### ✅ Key Idea:
You define getter methods, and Spring automatically maps query results to them.

---

## 🔹 3. Basic Example

### 📊 Financial Scenario: Account Summary View

Instead of fetching full account details, we only want:
- Customer name
- Balance

### Projection Interface:
```java
public interface AccountSummary {
    String getOwnerName();
    BigDecimal getBalance();
}
```

---

### Repository:
```java
@Query("SELECT a.ownerName AS ownerName, a.balance AS balance FROM Account a")
List<AccountSummary> getAccountSummaries();
```

---

### 💡 Result:
Instead of full `Account` objects, you get lightweight projections:
- Faster queries
- Less memory usage
- Better performance

---

## 🔹 4. Derived Query with Projections (No @Query Needed)

Spring can automatically map projections:

```java
List<AccountSummary> findByBalanceGreaterThan(BigDecimal amount);
```

💡 Spring internally selects only required fields.

---

## 🔹 5. Closed vs Open Projections

### ✅ Closed Projection (Recommended)

Only includes direct entity fields:

```java
public interface AccountSummary {
    String getOwnerName();
    BigDecimal getBalance();
}
```

✔ Fast  
✔ Optimized query  

---

### ⚠️ Open Projection (Uses Expressions)

```java
public interface AccountSummary {
    
    @Value("#{target.ownerName + ' - ' + target.accountType}")
    String getDisplayName();
}
```

❗ Uses Spring Expression Language (SpEL)  
❗ Fetches full entity internally → slower  

---

## 🔹 6. Nested Projections

### 📊 Financial Scenario: Account + Branch Info

#### Entity:
```java
class Account {
    String ownerName;
    BigDecimal balance;
    Branch branch;
}

class Branch {
    String branchName;
}
```

---

### Projection:
```java
public interface AccountWithBranch {
    String getOwnerName();
    BigDecimal getBalance();
    BranchInfo getBranch();

    interface BranchInfo {
        String getBranchName();
    }
}
```

---

### Repository:
```java
List<AccountWithBranch> findAllProjectedBy();
```

💡 Fetches nested structured data efficiently.

---

## 🔹 7. Native Query with Projections

You can also use projections with native SQL:

```java
@Query(value = """
    SELECT owner_name AS ownerName, balance 
    FROM account 
    WHERE balance > :amount
    """, nativeQuery = true)
List<AccountSummary> findHighValueAccounts(BigDecimal amount);
```

✔ Column aliases must match interface method names  

---

## 🔹 8. Real Financial Use Cases

### 📊 1. Customer Dashboard
Only fetch:
- Name
- Balance
- Account type

---

### 📊 2. Leaderboard (Top Customers)

```java
public interface TopCustomer {
    String getOwnerName();
    BigDecimal getTotalBalance();
}
```

```java
@Query("""
    SELECT a.ownerName AS ownerName, SUM(a.balance) AS totalBalance
    FROM Account a
    GROUP BY a.ownerName
    ORDER BY totalBalance DESC
""")
List<TopCustomer> getTopCustomers();
```

---

### 📊 3. Transaction Summary

```java
public interface TransactionSummary {
    BigDecimal getAmount();
    String getType();
}
```

---

## 🔹 9. Advantages

✔ Fetch only required columns  
✔ Improves performance  
✔ Reduces memory usage  
✔ Cleaner API responses  
✔ Ideal for read-heavy financial systems  

---

## 🔹 10. Limitations

❗ No complex transformations (unless using open projections)  
❗ Nested projections can become complex  
❗ Not suitable for updates  

---

## 🔹 11. Projection vs DTO

| Feature | Projection | DTO |
|--------|-----------|-----|
| Definition | Interface | Class |
| Boilerplate | Low | Medium |
| Flexibility | Limited | High |
| Performance | High | High |
| Use case | Read-only views | Complex transformations |

---

## 🔚 Key Takeaways

- Interface-based projections are **lightweight and efficient**
- Best for **read-only financial queries**
- Use **closed projections** for performance
- Use **native SQL + projections** for complex analytics

---

## 🚀 Summary

| Scenario | Best Approach |
|---------|--------------|
| Simple field selection | Projection |
| Complex logic | DTO |
| High-performance reads | Projection |
| Aggregated financial data | Projection + JPQL |  

---
