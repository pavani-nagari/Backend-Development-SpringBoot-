# Spring Boot Actuator
## Monitoring and Managing Spring Boot Applications (Banking Sector Examples)

## Introduction

**Spring Boot Actuator** provides **production-ready features** to monitor and manage applications.

It exposes **built-in endpoints** that provide information about:

- Application health
- System metrics
- Application configuration
- Environment properties
- Application logs
- Thread usage

These endpoints help **DevOps teams and engineers monitor applications in production**.

In enterprise systems such as **banking platforms**, monitoring is critical to ensure:

- System availability
- Transaction reliability
- Performance monitoring
- Security compliance

---

# Why Actuator is Important in Banking Systems

Banking systems must operate **24/7 without downtime**.

Actuator helps monitor:

```
Transaction services
Payment gateways
Loan processing services
Fraud detection systems
Customer account services
```

If a service fails, monitoring tools can detect the issue immediately.

---

# Adding Actuator Dependency

### Maven

```xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Once added, Spring Boot automatically enables **Actuator endpoints**.

---

# Example Banking Application Structure

```
Banking Application
        ↓
Spring Boot Application
        ↓
Actuator monitors services
```

Example services:

```
Account Service
Transaction Service
Payment Service
Loan Service
```

Actuator provides **health and performance data for these services**.

---

# Important Actuator Endpoints

By default, endpoints are exposed under:

```
/actuator
```

Example:

```
http://localhost:8080/actuator
```

---

# 1. Health Endpoint

```
/actuator/health
```

Provides application health status.

Example response:

```
{
 "status": "UP"
}
```

### Banking Example

Checks:

- Database connection
- Payment gateway connectivity
- External APIs

Example response:

```
{
 "status": "UP",
 "components": {
   "db": {
     "status": "UP"
   }
 }
}
```

If the database fails:

```
{
 "status": "DOWN"
}
```

---

# 2. Metrics Endpoint

```
/actuator/metrics
```

Provides system metrics such as:

- CPU usage
- Memory usage
- Request count
- JVM statistics

### Banking Example

Monitor:

```
Number of transactions per minute
API response times
System memory usage
```

Example request:

```
/actuator/metrics/http.server.requests
```

---

# 3. Info Endpoint

```
/actuator/info
```

Displays custom application information.

Example configuration:

### application.properties

```
management.info.env.enabled=true

info.app.name=Banking Transaction Service
info.app.version=1.0
info.app.team=Payments Team
```

Response:

```
{
 "app": {
   "name": "Banking Transaction Service",
   "version": "1.0",
   "team": "Payments Team"
 }
}
```

---

# 4. Environment Endpoint

```
/actuator/env
```

Displays environment configuration properties.

Example:

```
spring.datasource.url
server.port
spring.profiles.active
```

Useful for **debugging configuration issues**.

---

# 5. Beans Endpoint

```
/actuator/beans
```

Lists all **Spring beans in the application context**.

Example:

```
accountService
transactionService
dataSource
entityManager
```

Helps developers understand **application structure**.

---

# 6. Loggers Endpoint

```
/actuator/loggers
```

Allows **viewing and modifying log levels dynamically**.

Example:

Change logging level without restarting application.

```
POST /actuator/loggers/com.bank.transaction
```

---

# Exposing Actuator Endpoints

By default, only **health and info endpoints are exposed**.

To expose more endpoints:

### application.properties

```
management.endpoints.web.exposure.include=*
```

Or expose specific endpoints:

```
management.endpoints.web.exposure.include=health,info,metrics
```

---

# Custom Health Indicator (Banking Example)

Banks often monitor external systems such as **payment gateways**.

Example:

```java
@Component
public class PaymentGatewayHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {

        boolean paymentGatewayAvailable = true;

        if(paymentGatewayAvailable){
            return Health.up().withDetail("Payment Gateway", "Available").build();
        }

        return Health.down().withDetail("Payment Gateway", "Not reachable").build();
    }
}
```

Response:

```
{
 "status": "UP",
 "components": {
   "paymentGateway": {
     "status": "UP"
   }
 }
}
```

---

# Actuator with Monitoring Tools

Actuator integrates with monitoring systems such as:

```
Prometheus
Grafana
Elastic Stack
Datadog
New Relic
```

Example banking monitoring stack:

```
Spring Boot Actuator
        ↓
Prometheus collects metrics
        ↓
Grafana dashboard visualizes metrics
```

---

# Security Considerations

Actuator endpoints expose **sensitive system information**.

Banks usually secure them using **Spring Security**.

Example:

```
/actuator/health → Public
/actuator/metrics → Admin only
```

Configuration example:

```java
http
  .authorizeRequests()
  .requestMatchers("/actuator/**").hasRole("ADMIN");
```

---

# Advantages of Spring Boot Actuator

1. Provides real-time monitoring
2. Detects system failures quickly
3. Helps track application performance
4. Enables DevOps observability
5. Integrates with monitoring tools

---

# Real Banking Production Monitoring Example

```
Customer Transaction API
        ↓
Spring Boot Actuator
        ↓
Prometheus collects metrics
        ↓
Grafana dashboard shows:

• Transactions per second
• API latency
• Database response time
• System memory usage
```

If the **transaction service slows down**, engineers can detect it immediately.

---

# Summary

Spring Boot Actuator provides **production-ready monitoring features** for Spring Boot applications.

Key capabilities:

```
Health Monitoring
Performance Metrics
Application Info
Environment Details
Logging Control
```

In enterprise systems like **banking platforms**, Actuator is essential for:

- Service monitoring
- Failure detection
- Performance optimization
- Operational visibility
