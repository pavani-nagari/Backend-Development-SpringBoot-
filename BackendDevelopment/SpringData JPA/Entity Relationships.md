# 📘 Spring Data JPA — Entity Relationships (Finance Example)

## 📌 Overview

In real-world applications like banking, trading, or fintech platforms, data is highly interconnected.

Spring Data JPA provides annotations to model these relationships:

- `@OneToMany`
- `@ManyToOne`
- `@ManyToMany`

We’ll use a Finance System example with:

- Customers 👤
- Accounts 💳
- Transactions 💸
- Investment Portfolios 📊
- Stocks 📈

---

# 🧩 1. One-to-Many Relationship (`@OneToMany`)

## 📌 Definition
One entity is related to multiple instances of another entity.

## 💰 Real-World Example
👉 One Customer can have multiple Bank Accounts

---

## 🗂️ Database Structure

```
Customer (1) -------- (Many) Account
```

---

## 💡 Entity Design

### 👤 Customer Entity

```java
@Entity
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "customer", cascade = CascadeType.ALL)
    private List<Account> accounts;
}
```

---

### 💳 Account Entity

```java
@Entity
public class Account {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String accountNumber;
    private Double balance;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;
}
```

---

## ⚙️ Key Concepts

- `mappedBy = "customer"` → Account owns the relationship
- `cascade = ALL` → operations on Customer affect Accounts
- Foreign key is stored in `Account` table

---

## 📊 Real Use Case

- A user logs in → fetch all accounts
- Admin views → all accounts of a customer
- Delete customer → delete all accounts (if cascade applied)

---

# 🔁 2. Many-to-One Relationship (`@ManyToOne`)

## 📌 Definition
Many entities belong to one entity.

## 💰 Real-World Example
👉 Many Transactions belong to one Account

---

## 🗂️ Database Structure

```
Account (1) -------- (Many) Transaction
```

---

## 💡 Entity Design

### 💸 Transaction Entity

```java
@Entity
public class Transaction {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private Double amount;
    private String type; // CREDIT / DEBIT

    @ManyToOne
    @JoinColumn(name = "account_id")
    private Account account;
}
```

---

### 💳 Account Entity (updated)

```java
@OneToMany(mappedBy = "account")
private List<Transaction> transactions;
```

---

## ⚙️ Key Concepts

- Foreign key `account_id` in `Transaction`
- Efficient for querying:
  - Get all transactions of an account
  - Audit logs, statements

---

## 📊 Real Use Case

- Bank statement generation
- Fraud detection systems
- Monthly spending reports

---

# 🔄 3. Many-to-Many Relationship (`@ManyToMany`)

## 📌 Definition
Many entities are related to many other entities.

## 💰 Real-World Example
👉 A Portfolio can have multiple Stocks  
👉 A Stock can belong to multiple Portfolios

---

## 🗂️ Database Structure

```
Portfolio (Many) ---- (Many) Stock
         \            /
          \          /
        portfolio_stock (Join Table)
```

---

## 💡 Entity Design

### 📊 Portfolio Entity

```java
@Entity
public class Portfolio {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String portfolioName;

    @ManyToMany
    @JoinTable(
        name = "portfolio_stock",
        joinColumns = @JoinColumn(name = "portfolio_id"),
        inverseJoinColumns = @JoinColumn(name = "stock_id")
    )
    private List<Stock> stocks;
}
```

---

### 📈 Stock Entity

```java
@Entity
public class Stock {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String symbol;
    private String companyName;

    @ManyToMany(mappedBy = "stocks")
    private List<Portfolio> portfolios;
}
```

---

## ⚙️ Key Concepts

- Join Table: `portfolio_stock`
- `joinColumns` → current entity
- `inverseJoinColumns` → other entity
- Bidirectional relationship

---

## 📊 Real Use Case

- Investment apps (like Robinhood, Zerodha)
- Track portfolios across users
- Stock recommendation systems

---

# ⚠️ Important Best Practices

## 🚀 1. Avoid Infinite Recursion (JSON)

```java
@JsonManagedReference
@JsonBackReference
```

OR

```java
@JsonIgnore
```

---

## 🚀 2. Fetch Types

```java
@OneToMany(fetch = FetchType.LAZY)  // default
@ManyToOne(fetch = FetchType.EAGER) // default
```

- LAZY → loads when needed (better performance)
- EAGER → loads immediately

---

## 🚀 3. Cascade Types

```java
cascade = {
    CascadeType.PERSIST,
    CascadeType.MERGE,
    CascadeType.REMOVE
}
```

Use carefully (especially REMOVE in finance apps ❗)

---

## 🚀 4. Use DTOs (Important for APIs)

Avoid exposing entities directly:

```java
class AccountDTO {
    private String accountNumber;
    private Double balance;
}
```

---

# 🧠 Summary Table

| Relationship   | Example                  | Foreign Key Location |
|---------------|------------------------|---------------------|
| OneToMany     | Customer → Accounts    | Account table       |
| ManyToOne     | Transaction → Account  | Transaction table   |
| ManyToMany    | Portfolio ↔ Stocks     | Join table          |

---

# 🎯 Final Insight

In finance systems, relationships are critical because:

- Data consistency must be maintained
- Transactions must be traceable
- Relationships impact performance heavily

👉 Always design relationships based on:
- Query patterns
- Performance
- Data integrity
