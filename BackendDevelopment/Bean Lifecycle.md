# Spring Bean Lifecycle and Bean Scopes

In the Spring Framework, a **Bean** is an object that is instantiated, assembled, and managed by the **Spring IoC (Inversion of Control) container**.  

Spring manages the **entire lifecycle of a bean** from creation to destruction and allows developers to define **scopes** that control how many bean instances are created and how long they live.

---

# 1. Spring Bean Lifecycle

The **Bean Lifecycle** refers to the sequence of steps that a Spring bean goes through from its creation to destruction inside the Spring container.

## Bean Lifecycle Steps

### 1. Bean Instantiation
Spring creates an instance of the bean using the constructor.

Example:

```java
@Component
public class UserService {

    public UserService(){
        System.out.println("Bean instantiated");
    }

}
```

Spring creates the object when the application context starts.

---

### 2. Dependency Injection
After creating the bean, Spring injects required dependencies into it.

Example:

```java
@Component
public class OrderService {

    @Autowired
    private UserService userService;

}
```

Spring injects `UserService` into `OrderService`.

---

### 3. Bean Name Aware (Optional)

If a bean implements `BeanNameAware`, Spring provides the bean name.

```java
public class MyBean implements BeanNameAware {

    @Override
    public void setBeanName(String name) {
        System.out.println("Bean name is: " + name);
    }
}
```

---

### 4. Bean Factory Aware (Optional)

Spring provides access to the `BeanFactory`.

```java
public class MyBean implements BeanFactoryAware {

    @Override
    public void setBeanFactory(BeanFactory beanFactory) {
        System.out.println("BeanFactory set");
    }
}
```

---

### 5. Pre-Initialization (BeanPostProcessor)

Spring allows modification of beans **before initialization**.

```java
@Component
public class CustomBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        System.out.println("Before Initialization: " + beanName);
        return bean;
    }

}
```

---

### 6. Initialization

Spring calls initialization methods.

There are **three ways**:

### a) Using `@PostConstruct`

```java
@PostConstruct
public void init() {
    System.out.println("Bean initialized");
}
```

### b) Using `InitializingBean`

```java
public class MyBean implements InitializingBean {

    @Override
    public void afterPropertiesSet() {
        System.out.println("Bean initialized");
    }
}
```

### c) Using `init-method`

```java
@Bean(initMethod="init")
public MyBean myBean(){
    return new MyBean();
}
```

---

### 7. Bean Ready for Use

At this stage the bean is **fully initialized and ready to serve requests**.

---

### 8. Pre-Destruction

Before bean destruction Spring calls cleanup methods.

Using `@PreDestroy`

```java
@PreDestroy
public void destroy() {
    System.out.println("Bean destroyed");
}
```

---

### 9. Bean Destruction

Occurs when the application context shuts down.

Another method:

```java
@Bean(destroyMethod="cleanup")
```

---

## Complete Lifecycle Flow

```
Instantiation
     ↓
Dependency Injection
     ↓
Aware Interfaces
     ↓
BeanPostProcessor (Before Initialization)
     ↓
Initialization
     ↓
Bean Ready for Use
     ↓
BeanPostProcessor (After Initialization)
     ↓
PreDestroy
     ↓
Bean Destroyed
```

---

# 2. Spring Bean Scopes

**Bean Scope** defines how many instances of a bean Spring creates and how long they live.

Spring supports multiple scopes.

---

# 2.1 Singleton Scope

Singleton is the **default scope** in Spring.

Only **one instance of the bean is created per Spring container**.

Every request for that bean returns the **same object**.

## Example

```java
@Component
@Scope("singleton")
public class UserService {
}
```

## Behavior

```
Application Start
        ↓
Spring creates ONE bean instance
        ↓
All requests use SAME instance
```

## Characteristics

- Default scope
- Only one instance per container
- Shared across the application
- Memory efficient

## Example Scenario

A **Service layer bean**

```
UserService
OrderService
ProductService
```

These usually remain singleton.

---

# 2.2 Prototype Scope

In **Prototype scope**, Spring creates **a new bean instance every time it is requested**.

## Example

```java
@Component
@Scope("prototype")
public class User {
}
```

## Behavior

```
Request 1 → New Object
Request 2 → New Object
Request 3 → New Object
```

## Characteristics

- Multiple bean instances
- Created every time requested
- Spring does NOT manage full lifecycle after creation
- Destruction callbacks are not called automatically

## Example Scenario

Objects that maintain **state** such as:

```
UserSession
ShoppingCart
Temporary objects
```

---

# 2.3 Request Scope

Used in **Web Applications**.

A **new bean instance is created for every HTTP request**.

## Example

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST)
public class RequestBean {
}
```

## Behavior

```
HTTP Request 1 → New Bean
HTTP Request 2 → New Bean
HTTP Request 3 → New Bean
```

Each request gets its own instance.

## Example Scenario

```
UserRequestData
FormData
RequestContext
```

---

# 2.4 Session Scope

A **single bean instance is created per HTTP session**.

All requests within the same session use the **same bean instance**.

## Example

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION)
public class SessionBean {
}
```

## Behavior

```
User Session Start
       ↓
Bean Created
       ↓
All requests use same bean
       ↓
Session Ends → Bean destroyed
```

## Example Scenario

```
ShoppingCart
UserLoginSession
UserPreferences
```

---

# Bean Scope Comparison

| Scope | Instances Created | Lifetime | Use Case |
|-----|-----|-----|-----|
| Singleton | One per container | Application lifetime | Services, repositories |
| Prototype | New instance every request | Until garbage collected | Stateful objects |
| Request | One per HTTP request | Request duration | Request data |
| Session | One per HTTP session | Session duration | User session data |

---

# Summary

Spring Bean Lifecycle manages **how beans are created, initialized, used, and destroyed** by the container.

Bean Scopes control **how many instances are created and how long they exist**.

Important concepts:

- **Singleton** → One instance per container
- **Prototype** → New instance every request
- **Request** → One instance per HTTP request
- **Session** → One instance per user session

Understanding lifecycle and scopes helps build **scalable, efficient Spring applications**.
