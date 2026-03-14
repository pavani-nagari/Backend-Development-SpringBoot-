# Spring BeanFactory vs ApplicationContext

In the Spring Framework, the **IoC (Inversion of Control) container** is responsible for creating, configuring, and managing beans.

Spring provides two main container implementations:

- **BeanFactory**
- **ApplicationContext**

Both are used to manage beans, but **ApplicationContext is more advanced and commonly used in modern Spring applications**.

---

# 1. BeanFactory

## Definition

`BeanFactory` is the **simplest container in Spring**.  
It provides basic functionality for **dependency injection and bean management**.

It follows **lazy initialization**, meaning beans are created **only when they are requested**.

---

## Key Characteristics

- Basic container
- Uses **lazy initialization**
- Lightweight
- Does not support advanced features like events, AOP, or internationalization
- Mostly used in **memory-constrained environments**

---

## BeanFactory Interface

```java
public interface BeanFactory
```

It is the **root interface of the Spring IoC container**.

---

## Example

```java
Resource resource = new ClassPathResource("beans.xml");

BeanFactory factory = new XmlBeanFactory(resource);

UserService userService = (UserService) factory.getBean("userService");
```

### Explanation

1. Load configuration file
2. Create BeanFactory container
3. Retrieve bean using `getBean()`

The bean is **created only when `getBean()` is called**.

---

## BeanFactory Behavior

```
Application Start
       ↓
Container Created
       ↓
Bean NOT created yet
       ↓
getBean() called
       ↓
Bean created
```

---

## When to Use BeanFactory

Rarely used today, but suitable for:

- Lightweight applications
- Memory-sensitive systems
- Simple dependency injection

---

# 2. ApplicationContext

## Definition

`ApplicationContext` is the **advanced container in Spring** built on top of `BeanFactory`.

It provides **all BeanFactory features plus many enterprise features**.

---

## Key Characteristics

- Advanced Spring container
- Uses **eager initialization** by default
- Supports **AOP**
- Supports **event handling**
- Supports **internationalization (i18n)**
- Supports **annotation-based configuration**
- Automatically registers **BeanPostProcessors**

---

## ApplicationContext Interface

```java
public interface ApplicationContext extends BeanFactory
```

This means **ApplicationContext is a child of BeanFactory** and includes all its functionality.

---

## Example

```java
ApplicationContext context =
        new ClassPathXmlApplicationContext("beans.xml");

UserService userService = context.getBean(UserService.class);
```

---

## ApplicationContext Behavior

```
Application Start
       ↓
Container Created
       ↓
All Singleton Beans Created
       ↓
Beans ready to use
```

Beans are created **during container startup**, not when requested.

---

## Types of ApplicationContext

### 1. ClassPathXmlApplicationContext

Loads configuration from the **classpath**.

```java
ApplicationContext context =
new ClassPathXmlApplicationContext("beans.xml");
```

---

### 2. FileSystemXmlApplicationContext

Loads configuration from **filesystem path**.

```java
ApplicationContext context =
new FileSystemXmlApplicationContext("config/beans.xml");
```

---

### 3. AnnotationConfigApplicationContext

Used for **Java-based configuration**.

```java
ApplicationContext context =
new AnnotationConfigApplicationContext(AppConfig.class);
```

---

### 4. WebApplicationContext

Used in **Spring Web Applications**.

---

# BeanFactory vs ApplicationContext

| Feature | BeanFactory | ApplicationContext |
|------|------|------|
| Initialization | Lazy | Eager |
| Performance | Faster startup | Slightly slower |
| Memory Usage | Less | More |
| Event Handling | Not supported | Supported |
| AOP Support | Limited | Full support |
| Internationalization | Not supported | Supported |
| BeanPostProcessor | Manual | Automatic |
| Recommended Usage | Rarely used | Widely used |

---

# Lazy vs Eager Initialization

## BeanFactory (Lazy)

Bean created only when requested.

```
getBean() → Bean Created
```

---

## ApplicationContext (Eager)

Beans created during container startup.

```
Application Start → Beans Created
```

---

# Simple Example Using ApplicationContext

```java
@Component
public class UserService {

    public void display(){
        System.out.println("User Service Running");
    }

}
```

Main class:

```java
ApplicationContext context =
new AnnotationConfigApplicationContext(AppConfig.class);

UserService service = context.getBean(UserService.class);

service.display();
```

---

# Why ApplicationContext is Preferred

Most modern Spring applications use **ApplicationContext** because it provides:

- Dependency Injection
- Event propagation
- Annotation support
- AOP integration
- Automatic bean lifecycle handling

Frameworks like:

- Spring Boot
- Spring MVC
- Spring Security

all use **ApplicationContext internally**.

---

# Summary

`BeanFactory` and `ApplicationContext` are both **Spring IoC containers** used to manage beans.

Key differences:

- **BeanFactory** → Basic container with lazy initialization.
- **ApplicationContext** → Advanced container with enterprise features.

In modern Spring development, **ApplicationContext is the recommended container**.
