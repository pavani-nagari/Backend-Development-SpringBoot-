# JPA Annotations Explained

---

## 🔹 1. `@Entity`

### What it does  
Marks a Java class as a **persistent entity**, meaning it will be mapped to a database table.

### Why it matters (Finance)
In financial systems, entities represent real-world objects:
- Bank accounts  
- Transactions  
- Customers  

### Example
```java
import jakarta.persistence.*;

@Entity
public class Transaction {
}
```

👉 This tells JPA to store `Transaction` objects in the database.

---

## 🔹 2. `@Table`

### What it does  
Specifies the **exact table name** in the database.

### Why it matters (Finance)
- Financial systems often follow strict naming conventions  
- Helps map to existing legacy tables  

### Example
```java
@Entity
@Table(name = "financial_transactions")
public class Transaction {
}
```

👉 Avoids conflicts with reserved keywords like `transaction`.

---

## 🔹 3. `@Id`

### What it does  
Marks the **primary key** of the entity.

### Why it matters (Finance)
- Ensures each record is uniquely identifiable  
- Critical for auditing and traceability  

### Example
```java
@Id
private Long transactionId;
```

---

## 🔹 4. `@GeneratedValue`

### What it does  
Automatically generates values for the primary key.

### Strategies:
- `IDENTITY` → Auto-increment (common)  
- `SEQUENCE` → Uses DB sequence  
- `AUTO` → JPA decides  

### Why it matters (Finance)
- Prevents duplicate IDs  
- Supports safe concurrent transactions  

### Example
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long transactionId;
```

---

## 🔹 5. `@Column`

### What it does  
Maps a field to a database column and allows constraints.

### Common attributes:
- `name` → Column name  
- `nullable` → Cannot be null  
- `unique` → Must be unique  
- `length` → String size  
- `precision/scale` → Decimal control (important for money)  

---

## 💰 Full Financial Example: Transaction Entity

```java
@Entity
@Table(name = "financial_transactions")
public class Transaction {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long transactionId;

    @Column(name = "account_number", nullable = false, length = 20)
    private String accountNumber;

    @Column(name = "amount", nullable = false, precision = 12, scale = 2)
    private BigDecimal amount;

    @Column(name = "transaction_type", nullable = false)
    private String type; // CREDIT / DEBIT

    @Column(name = "status", nullable = false)
    private String status; // SUCCESS / FAILED / PENDING

    @Column(name = "created_at", nullable = false)
    private LocalDateTime createdAt;
}
```

---

## 🔍 Deep Financial Insights

### 1. Why `precision` and `scale` matter
```java
@Column(precision = 12, scale = 2)
```

- Total digits = 12  
- Decimal places = 2  

✔️ Valid: `1234567890.12`  
❌ Invalid: `1234567890123.45`

⚠️ Always use `BigDecimal` for money (never `double`).

---

### 2. Why `nullable = false` is critical
```java
@Column(nullable = false)
```

Prevents:
- Missing transaction data  
- Null account values  

💡 In finance, null data can lead to serious inconsistencies.

---

### 3. Why naming matters
```java
@Table(name = "financial_transactions")
@Column(name = "account_number")
```

- Matches enterprise database standards  
- Avoids SQL keyword conflicts  

---

## 🔁 Real-World Flow Example

When a user sends money:
1. Backend creates a `Transaction` entity  
2. JPA maps it to `financial_transactions` table  
3. Database generates the ID  
4. Data is stored with constraints  

---

## 🏦 Another Example: Bank Account Entity

```java
@Entity
@Table(name = "accounts")
public class Account {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long accountId;

    @Column(name = "customer_name", nullable = false)
    private String customerName;

    @Column(name = "balance", precision = 15, scale = 2)
    private BigDecimal balance;

    @Column(name = "account_type")
    private String accountType; // SAVINGS / CURRENT
}
```

---

## ⚠️ Common Mistakes

### ❌ Using `double` for money
```java
private double amount; // WRONG
```

✔️ Correct:
```java
private BigDecimal amount;
```

---

### ❌ Missing constraints
```java
@Column
private String accountNumber;
```

✔️ Better:
```java
@Column(nullable = false, unique = true)
```

---

### ❌ Relying on default table names
Can break in production systems with strict schemas.

---

## 🧠 Summary

| Annotation | Purpose | Finance Importance |
|-----------|--------|-------------------|
| `@Entity` | Maps class to table | Represents financial objects |
| `@Table` | Custom table name | Matches DB schema |
| `@Id` | Primary key | Unique identification |
| `@GeneratedValue` | Auto ID generation | Prevents duplicates |
| `@Column` | Field mapping & constraints | Ensures data integrity |

---

