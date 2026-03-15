# Spring Property Management
## (@Value and @ConfigurationProperties) with Banking Sector Examples

## Introduction

In real-world applications, many configuration values should **not be hardcoded in the code**.  
Examples include:

- Database credentials
- API keys
- Interest rates
- Payment gateway URLs
- Feature flags
- Security settings

Spring provides **Property Management** to store these values in external configuration files such as:

```
application.properties
application.yml
environment variables
```

These values can be injected into Spring beans using:

- `@Value`
- `@ConfigurationProperties`

---

# Why Property Management is Important

In industries like **banking**, applications must run in multiple environments and handle **sensitive configurations securely**.

Example banking configurations:

```
loan.interest.rate
payment.gateway.url
fraud.detection.enabled
transaction.limit
database.credentials
```

Instead of hardcoding these values in Java classes, Spring reads them from configuration files.

---

# application.properties Example (Banking System)

```
bank.name=Global Trust Bank
bank.loan.interest-rate=7.5
bank.transaction.limit=50000
bank.fraud.detection.enabled=true

payment.gateway.url=https://payments.globalbank.com/api
payment.gateway.timeout=5000
```

---

# 1. Using @Value Annotation

## What is @Value?

`@Value` is used to **inject property values directly into Spring beans**.

It reads values from:

- `application.properties`
- `application.yml`
- Environment variables

---

# Syntax

```
@Value("${property.name}")
```

---

# Banking Example: Transaction Service

## application.properties

```
bank.transaction.limit=50000
bank.name=Global Trust Bank
```

---

## TransactionService.java

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

@Service
public class TransactionService {

    @Value("${bank.transaction.limit}")
    private double transactionLimit;

    @Value("${bank.name}")
    private String bankName;

    public void processTransaction(double amount){

        if(amount > transactionLimit){
            System.out.println("Transaction exceeds allowed limit!");
        }else{
            System.out.println("Transaction processed by " + bankName);
        }

    }
}
```

---

# Output

```
Transaction processed by Global Trust Bank
```

---

# Advantages of @Value

- Simple to use
- Good for **single property values**
- Works well for small configurations

---

# Limitations of @Value

- Hard to manage **large configuration groups**
- No type-safe mapping
- Difficult to maintain when properties grow

Because of these limitations, Spring recommends **`@ConfigurationProperties` for complex configurations**.

---

# 2. Using @ConfigurationProperties

## What is @ConfigurationProperties?

`@ConfigurationProperties` is used to **map a group of related properties into a Java object**.

It is ideal for **large structured configurations**.

---

# Banking Example

Suppose a banking system has many loan configuration settings.

## application.properties

```
bank.loan.interest-rate=7.5
bank.loan.max-amount=1000000
bank.loan.min-amount=10000
bank.loan.processing-fee=2.5
```

---

# Step 1: Create Configuration Class

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "bank.loan")
public class LoanProperties {

    private double interestRate;
    private double maxAmount;
    private double minAmount;
    private double processingFee;

    public double getInterestRate() {
        return interestRate;
    }

    public void setInterestRate(double interestRate) {
        this.interestRate = interestRate;
    }

    public double getMaxAmount() {
        return maxAmount;
    }

    public void setMaxAmount(double maxAmount) {
        this.maxAmount = maxAmount;
    }

    public double getMinAmount() {
        return minAmount;
    }

    public void setMinAmount(double minAmount) {
        this.minAmount = minAmount;
    }

    public double getProcessingFee() {
        return processingFee;
    }

    public void setProcessingFee(double processingFee) {
        this.processingFee = processingFee;
    }

}
```

---

# Step 2: Use It in Loan Service

```java
import org.springframework.stereotype.Service;

@Service
public class LoanService {

    private final LoanProperties loanProperties;

    public LoanService(LoanProperties loanProperties){
        this.loanProperties = loanProperties;
    }

    public void calculateLoan(){

        System.out.println("Loan Interest Rate: " + loanProperties.getInterestRate());
        System.out.println("Maximum Loan Amount: " + loanProperties.getMaxAmount());
        System.out.println("Processing Fee: " + loanProperties.getProcessingFee());

    }

}
```

---

# Output

```
Loan Interest Rate: 7.5
Maximum Loan Amount: 1000000
Processing Fee: 2.5
```

---

# Real-World Banking Use Cases

Banks commonly store configurations like:

### Fraud Detection

```
fraud.detection.enabled=true
fraud.max.transactions.per.minute=20
fraud.alert.email=security@bank.com
```

### Payment Gateway

```
payment.gateway.url=https://secure-pay.bank.com
payment.gateway.timeout=5000
payment.gateway.retries=3
```

### Loan Configuration

```
loan.home.interest=6.5
loan.car.interest=8.2
loan.personal.interest=11.5
```

These are usually mapped using **`@ConfigurationProperties`**.

---

# @Value vs @ConfigurationProperties

| Feature | @Value | @ConfigurationProperties |
|------|------|------|
| Best for | Single values | Grouped properties |
| Type Safety | No | Yes |
| Maintainability | Low for large configs | High |
| Real-world usage | Small configs | Enterprise applications |

---

# Best Practice in Enterprise Banking Systems

Use:

```
@ConfigurationProperties
```

for:

- Loan configuration
- Payment gateway configuration
- Fraud detection settings
- Transaction limits
- Security configuration

Use:

```
@Value
```

for:

- Small properties
- Feature flags
- Simple configuration values

---

# Summary

Spring Property Management allows applications to **externalize configuration** and inject values dynamically.

Two major approaches:

### @Value
- Injects individual property values
- Simple and lightweight

### @ConfigurationProperties
- Maps structured configuration into Java objects
- Preferred for large enterprise systems like banking platforms

This approach ensures **clean code, environment flexibility, and secure configuration management** in modern Spring applications.
