# Spring Boot Annotations – Detailed Explanation

Annotations in Spring Boot are metadata added directly above classes, methods, fields, or parameters to tell the Spring framework how to manage and configure them. They help configure beans, enable auto-configuration, manage dependency injection, build REST APIs, handle exceptions, validate input, and map database entities. Annotations work closely with IoC (Inversion of Control), Dependency Injection, Component Scanning, and Auto-Configuration.

---

## 1️⃣ Core Spring Boot Annotations

These annotations are responsible for bootstrapping and configuring the application.

### 🔹 `@SpringBootApplication`

This is the most important annotation in Spring Boot. It is placed on the main class and acts as the entry point of the application.

Internally, it combines:

- `@SpringBootConfiguration`  
- `@EnableAutoConfiguration`  
- `@ComponentScan`  

#### What It Actually Does

- Starts embedded server (Tomcat by default)  
- Scans all components in the base package  
- Applies auto-configuration  
- Registers beans in `ApplicationContext`  
- Triggers Spring Boot startup lifecycle  

It eliminates the need to manually configure:

- XML files  
- `DispatcherServlet`  
- Component scanning  

Without this annotation, the application will not start properly.

---

### 🔹 `@SpringBootConfiguration`

This is a specialized version of `@Configuration`.

- Tells Spring that:  
  “This class contains bean definitions and configuration logic.”  
- Rarely used directly because `@SpringBootApplication` already includes it.  
- Marks the class as a source of bean definitions for the IoC container.  

---

### 🔹 `@EnableAutoConfiguration`

This is one of the most powerful features of Spring Boot. It enables auto-configuration, meaning Spring Boot automatically configures beans based on:

- Dependencies present in the classpath  
- `application.properties`  
- Existing beans  

#### Example

If you add:

- `spring-boot-starter-data-jpa`  

Spring Boot automatically:

- Configures `DataSource`  
- Configures Hibernate  
- Configures Transaction Manager  
- Connects to the database  

You don’t write the configuration manually.

#### Behind the Scenes

- Spring Boot reads `META-INF/spring.factories` (or newer mechanisms in recent versions) to decide what to auto-configure.

### 🔹 `@ComponentScan`

Spring uses component scanning to detect classes annotated with:

- `@Component`  
- `@Service`  
- `@Repository`  
- `@Controller`  
- `@RestController`  

It scans the package of the main class and its sub-packages. If your classes are outside that package, they won’t be detected unless explicitly configured.

---

## 2️⃣ Spring Bean Annotations

These annotations define objects managed by the Spring IoC container.

### 🔹 `@Component`

- Marks a class as a generic Spring-managed bean.  
- During component scanning, Spring:  
  - Detects this class  
  - Creates an object  
  - Stores it in the `ApplicationContext`  
- Commonly used when the class does not clearly fit into Service/Repository/Controller categories.

---

### 🔹 `@Service`

- Used in the **Service Layer**.  
- Semantically more meaningful than `@Component`.  
- Indicates: “This class contains business logic.”  
- No functional difference from `@Component`, but improves readability and intent.

---

### 🔹 `@Repository`

- Used in the **DAO (Data Access) Layer**.  
- Special feature: Converts database exceptions into Spring’s `DataAccessException` hierarchy automatically.  
- Example: Instead of raw `SQLException`, you get consistent Spring data access exceptions.

### 🔹 `@Configuration`

- Used to define configuration classes.  
- Spring treats methods inside this class annotated with `@Bean` as bean definitions.  
- Effectively replaces traditional XML-based configuration.

---

### 🔹 `@Bean`

- Used inside a `@Configuration` class.  
- Explicitly creates and registers a bean.  
- Commonly used when:  
  - Creating third-party library objects  
  - Custom bean configuration is needed  
  - You need lifecycle control over the bean  

---

## 3️⃣ Dependency Injection Annotations

Dependency Injection is core to the Spring framework.

### 🔹 `@Autowired`

- Automatically injects dependencies **by type**.  
- Spring searches for a matching bean in the `ApplicationContext` and injects it.  
- Supports:  
  - Field injection  
  - Setter injection  
  - Constructor injection (**recommended**)  

Constructor injection is preferred because:

- Makes dependencies mandatory  
- Easier to unit test  
- Helps avoid `NullPointerException`  

---

### 🔹 `@Qualifier`

- Used when **multiple beans of the same type** exist.  
- Helps Spring decide exactly which bean to inject.  

Example scenario:

- Two implementations of `NotificationService`:  
  - `EmailService`  
  - `SmsService`  

Spring would be ambiguous without `@Qualifier`, so you specify which one to inject.

---

### 🔹 `@Primary`

- Marks one bean as the **default** among multiple candidates.  
- If no `@Qualifier` is specified, Spring injects the bean marked with `@Primary`.

## 4️⃣ Web & REST API Annotations

These annotations help build REST APIs.

---

### 🔹 `@RestController`

- Combination of:  
  - `@Controller`  
  - `@ResponseBody`  
- Indicates that:  
  - This class handles HTTP requests  
  - Return values should be converted to JSON automatically  
- Commonly used for REST API controllers.

---

### 🔹 `@RequestMapping`

- Maps HTTP requests to classes or methods.  
- Can define:  
  - URL path  
  - HTTP method  
  - Consumes/produces content type  
- Can be placed at:  
  - Class level  
  - Method level  

---

### 🔹 HTTP Method Annotations

These are shortcuts for `@RequestMapping(method = ...)` and improve readability:

- `@GetMapping` → Fetch data  
- `@PostMapping` → Create data  
- `@PutMapping` → Update data  
- `@DeleteMapping` → Delete data  

---

### 🔹 `@PathVariable`

- Used to extract dynamic values from the URL path.  
- Example:  
  - URL: `/users/10`  
  - `10` is mapped into a method parameter.

---

### 🔹 `@RequestParam`

- Used to extract query parameters from the URL.  
- Example:  
  - URL: `/search?keyword=java`  
  - `keyword` is mapped into a method parameter.

---

### 🔹 `@RequestBody`

- Maps the JSON request body to a Java object.  
- Mainly used in:  
  - `POST`  
  - `PUT`  
- Spring automatically converts JSON to Java using Jackson.

## 5️⃣ Configuration & Properties Annotations

### 🔹 `@Value`

- Injects a single property value from:  
  - `application.properties`  
  - `application.yml`  
- Example:  
  - `server.port=8080`  
  - `@Value("${server.port}")` injects `8080` into the field or parameter.

---

### 🔹 `@ConfigurationProperties`

- Binds a group of related properties into a POJO.  
- More powerful and cleaner than using multiple `@Value` annotations.  
- Useful for:  
  - Large configuration blocks  
  - Type-safe binding  
  - Keeping configuration code organized  

---

## 6️⃣ Validation Annotations

Used to validate input data, typically in combination with `@Valid` and Hibernate Validator.

Common annotations:

- `@NotNull`  
- `@NotBlank`  
- `@Email`  
- `@Size`  

If validation fails, Spring automatically throws `MethodArgumentNotValidException`.

---

## 7️⃣ Exception Handling Annotations

### 🔹 `@ExceptionHandler`

- Handles specific exceptions inside a controller.  
- Allows you to return custom error responses for particular exception types.

---

### 🔹 `@ControllerAdvice`

- Provides **global exception handling** across the entire application.  
- Used to:  
  - Standardize error responses  
  - Handle validation errors  
  - Catch and process runtime exceptions  

---

## 8️⃣ JPA & Database Annotations

Used with Spring Data JPA for ORM mapping.

### 🔹 `@Entity`

- Marks a class as a database entity.  
- Maps the class to a table in the database.

---

### 🔹 `@Table`

- Specifies the table name for the entity.  

---

### 🔹 `@Id`

- Defines the primary key field of the entity.  

---

### 🔹 `@GeneratedValue`

- Auto-generates the primary key value.  
- Common strategies:  
  - `AUTO`  
  - `IDENTITY`  
  - `SEQUENCE`  

---

### 🔹 `@Column`

- Maps a field to a table column.  
- Allows customization of:  
  - `name`  
  - `nullable`  
  - `unique`  
  - `length`  


