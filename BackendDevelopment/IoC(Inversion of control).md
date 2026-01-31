# Spring IoC (Inversion of Control) – Complete Explanation with Payment Example

Spring IoC (Inversion of Control) is the **core concept of the Spring Framework**.  
It changes the way objects are created and managed in a Java application.

Instead of the developer creating objects using the `new` keyword, **Spring creates, manages, and injects objects for us**.  
This shift of control from the developer to the framework is called **Inversion of Control (IoC)**.

Spring achieves IoC using a technique called **Dependency Injection (DI)**.

---

## What is Dependency Injection (DI)?

Dependency Injection means:
> *A class should not create the objects it depends on. Instead, those objects should be provided (injected) from outside.*

Spring injects dependencies using:
- XML configuration
- Java-based configuration
- Annotations

---

## Types of IoC Containers in Spring

Spring provides two main IoC containers:

### 1. BeanFactory
- The most basic IoC container
- Provides dependency injection and lifecycle management
- Lightweight and memory efficient
- Rarely used in real-world applications

### 2. ApplicationContext
- Built on top of BeanFactory
- Provides enterprise features such as:
  - Event handling
  - Internationalization
  - Annotation support
- **Most commonly used container**

---

## Key Features of Spring IoC Container

- **Dependency Injection** – Automatically injects required dependencies
- **Lifecycle Management** – Handles creation, initialization, and destruction of beans
- **Configuration Flexibility** – XML, Java Config, and Annotations
- **Loose Coupling** – Components depend on interfaces, not implementations

---

## Practical Example: Payment System

### Real-world Scenario

Consider an **Online Shopping Application** that supports multiple payment methods:
- Credit Card
- PayPal

The application should be flexible enough to switch payment methods **without changing business logic code**.

---

## Step 1: Create the Payment Interface

The interface defines *what* the system does, not *how* it does it.

```java
public interface Payment {
    void pay();
}
```
## Step 2: Implement the Interface
```java
Credit Card Payment Implementation
public class CreditCardPayment implements Payment {

    @Override
    public void pay() {
        System.out.println("Payment done using Credit Card");
    }
}

PayPal Payment Implementation
public class PayPalPayment implements Payment {

    @Override
    public void pay() {
        System.out.println("Payment done using PayPal");
    }
}
```

At this point, we have multiple implementations of the same interface.

## Step 3: Without Spring IoC (Tight Coupling)
```java
public class ShoppingApp {

    public static void main(String[] args) {

        Payment payment = new CreditCardPayment();
        payment.pay();
    }
}
```
**Explanation**

ShoppingApp directly creates CreditCardPayment

The application is tightly coupled

To switch to PayPal, source code must be changed
```java
Payment payment = new PayPalPayment();
```


❌ This violates loose coupling and open-closed principle.

## Step 4: Using Spring IoC with XML Configuration

Now, Spring takes control of object creation.
```java
beans.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans/"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="payment" class="CreditCardPayment"/>
</beans>
```

**Explanation**

Spring is told which class to instantiate

Object creation responsibility moves to Spring

Developer does not use new

## Step 5: Retrieving Bean Using ApplicationContext
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class ShoppingApp {

    public static void main(String[] args) {

        ApplicationContext context =
            new ClassPathXmlApplicationContext("beans.xml");

        Payment payment = context.getBean("payment", Payment.class);
        payment.pay();
    }
}
```
**Output**
Payment done using Credit Card

Switching Payment Method

Only change in beans.xml:

<bean id="payment" class="PayPalPayment"/>


✅ Java code remains unchanged
✅ Loose coupling achieved

## Step 6: Java-Based Configuration (No XML)

Modern Spring applications prefer Java configuration.

Configuration Class
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public Payment payment() {
        return new CreditCardPayment();
    }
}
```
**Explanation**

@Configuration marks this as a Spring configuration class

@Bean tells Spring to manage this object

Main Class
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ShoppingApp {

    public static void main(String[] args) {

        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        Payment payment = context.getBean(Payment.class);
        payment.pay();
    }
}
```

To switch payment method:

return new PayPalPayment();

### Step 7: Annotation-Based Dependency Injection

This is the most commonly used approach in Spring Boot.

Enable Component Scanning
```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}
```
Annotate Implementations
```java
@Component
public class CreditCardPayment implements Payment {

    @Override
    public void pay() {
        System.out.println("Payment done using Credit Card");
    }
}

@Component
public class PayPalPayment implements Payment {

    @Override
    public void pay() {
        System.out.println("Payment done using PayPal");
    }
}
```

Inject Dependency Using @Autowired
```java
@Component
public class ShoppingApp {

    @Autowired
    private Payment payment;

    public void checkout() {
        payment.pay();
    }

    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AppConfig.class);

        ShoppingApp app = context.getBean(ShoppingApp.class);
        app.checkout();
    }
}
```
Problem: Multiple Beans of Same Type

Spring finds two Payment beans and throws:

NoUniqueBeanDefinitionException

Solutions to Resolve the Exception
1. Using @Primary
```java
@Component
@Primary
public class CreditCardPayment implements Payment {
}

```
Spring injects this bean by default.

2. Using @Qualifier
```java
@Autowired
@Qualifier("payPalPayment")
private Payment payment;

```
Specifies exactly which bean to inject.

3. Explicit Bean Names
```java
@Component("creditCardBean")
public class CreditCardPayment implements Payment {
}

@Component("paypalBean")
public class PayPalPayment implements Payment {
}

@Autowired
@Qualifier("paypalBean")
private Payment payment;
```
Final Conceptual Summary
Without IoC
Application → creates dependency using new

With IoC
Spring Container → creates dependency → injects into application

## Why Spring IoC is Important

Promotes loose coupling

Makes applications easier to maintain

Simplifies testing (mocking dependencies)

Allows easy switching of implementations

Forms the foundation of Spring Boot

The @Configuration annotation is used to indicate that the class contains Spring bean definitions. It tells the spring to treat this class as a source of bean definitions and to process the  @Bean methods to register beans in the application context. This enables dependency injection and configuration management in a centralized way
