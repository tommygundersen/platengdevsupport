---
name: scaffold-web-api

applyTo:
  - '**/*.{java,xml,yml,yaml}'
  - '**/pom.xml'
  - '**/application*.{yml,yaml,properties}'
  
description: >
  Scaffold a production-ready Java Spring Boot REST API following organizational standards:
  Azure AD authentication, PostgreSQL/MongoDB support, OpenTelemetry observability,
  security baselines, and CI/CD pipelines. Based on: mcp-servers/layer2-goldenpaths/web-api

tools:
  allow:
    - create_file
    - replace_string_in_file
    - multi_replace_string_in_file
    - run_in_terminal
  deny: []
---

# Scaffold Web API Skill

You are an expert at scaffolding production-ready Java Spring Boot REST APIs following organizational standards.

## Your Mission

When the user invokes this skill with a command like:
- `@workspace /scaffold-web-api my-service`
- `scaffold a new web API called order-service`
- `create a REST API for managing products`

You will generate a complete, production-ready Spring Boot application based on the **Golden Path: Web API**.

## What You Generate

### 1. Project Structure

Create the following structure:
```
<service-name>/
├── src/main/java/com/organization/<service-name>/
│   ├── <ServiceName>Application.java
│   ├── config/
│   │   ├── SecurityConfig.java
│   │   ├── DatabaseConfig.java
│   │   ├── ObservabilityConfig.java
│   │   └── CorsConfig.java
│   ├── controller/
│   │   ├── HealthController.java
│   │   └── <Resource>Controller.java
│   ├── service/
│   │   └── <Resource>Service.java
│   ├── repository/
│   │   └── <Resource>Repository.java
│   ├── model/
│   │   ├── entity/<Resource>.java
│   │   └── dto/
│   │       ├── <Resource>Request.java
│   │       └── <Resource>Response.java
│   ├── exception/
│   │   ├── GlobalExceptionHandler.java
│   │   ├── BusinessException.java
│   │   └── ResourceNotFoundException.java
│   └── util/
├── src/main/resources/
│   ├── application.yml
│   ├── application-dev.yml
│   ├── application-prod.yml
│   └── db/migration/
│       └── V1__initial_schema.sql
├── src/test/java/
├── .github/workflows/
│   ├── ci.yml
│   └── cd.yml
├── k8s/
│   ├── deployment.yml
│   ├── service.yml
│   └── ingress.yml
├── Dockerfile
├── pom.xml
└── README.md
```

### 2. Required Dependencies (pom.xml)

MUST include:
- Spring Boot 3.2+ (Java 21)
- Spring Security with Azure AD OAuth2
- Spring Data JPA + PostgreSQL (or MongoDB if specified)
- Flyway for database migrations
- OpenTelemetry for observability
- Azure SDK (Key Vault, Identity)
- Resilience4j for circuit breakers
- Spring Boot Actuator for health checks
- Logstash Logback Encoder for structured logging
- OWASP Dependency Check Maven Plugin

**Reference**: See `mcp-servers/layer2-goldenpaths/web-api/index.md` for exact dependency versions.

### 3. Security Configuration (MUST)

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        // Azure AD OAuth2 JWT authentication
        // Stateless sessions
        // CSRF disabled for API
        // Security headers configured
    }
}
```

**Requirements**:
- Azure AD authentication (OAuth2 JWT)
- Stateless session management
- Role-based access control annotations
- Security headers (from Layer 1 Security Baseline)

### 4. Application Configuration

```yaml
spring:
  application:
    name: <service-name>
  cloud:
    azure:
      active-directory:
        enabled: true
        tenant-id: ${AZURE_TENANT_ID}
        credential:
          client-id: ${AZURE_CLIENT_ID}
  datasource:
    url: jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
  flyway:
    enabled: true

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  health:
    readiness-state:
      enabled: true
    liveness-state:
      enabled: true
```

### 5. Controller Pattern

```java
@RestController
@RequestMapping("/api/v1/<resources>")
@RequiredArgsConstructor
@Slf4j
public class <Resource>Controller {
    private final <Resource>Service service;
    private final Tracer tracer;
    
    @GetMapping
    @PreAuthorize("hasAuthority('SCOPE_read:<resources>')")
    public ResponseEntity<List<<Resource>Response>> list(
            @AuthenticationPrincipal Jwt jwt) {
        // Implementation with tracing
    }
    
    @PostMapping
    @PreAuthorize("hasAuthority('SCOPE_write:<resources>')")
    public ResponseEntity<<Resource>Response> create(
            @Valid @RequestBody <Resource>Request request,
            @AuthenticationPrincipal Jwt jwt) {
        // Implementation with validation
    }
}
```

### 6. Global Exception Handler

MUST include standardized error responses:
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ExceptionHandler(ResourceNotFoundException.class)
    @ExceptionHandler(AccessDeniedException.class)
    @ExceptionHandler(Exception.class)
    // Standard JSON error format
}
```

### 7. Dockerfile (Multi-stage)

```dockerfile
FROM maven:3.9-eclipse-temurin-21-alpine AS build
# Build stage

FROM eclipse-temurin:21-jre-alpine
# Runtime stage with non-root user
# Health check included
```

### 8. Kubernetes Manifests

- Deployment with 3 replicas
- Liveness and readiness probes
- Resource limits and requests
- Azure Managed Identity integration
- Service and Ingress

### 9. CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
- Security scanning (OWASP Dependency Check)
- Unit and integration tests
- Docker build and push to ACR
- Kubernetes deployment
```

## Parameters

When scaffolding, ask the user:

1. **Service Name**: What is the service called?
   - Example: "order-service", "product-catalog"
   - Used for: package names, Kubernetes resources, Docker image

2. **Resource Type**: What primary resource does this API manage?
   - Example: "Order", "Product", "Customer"
   - Used for: entity names, controller paths, database tables

3. **Database Type**: PostgreSQL or MongoDB?
   - Default: PostgreSQL
   - Affects: dependencies, repository implementation, config

4. **Azure Environment**: Which Azure subscription/resource group?
   - Used for: Key Vault references, database connections, deployment targets

## Mandatory Checklist

Before completing scaffolding, ensure:

- [ ] Azure AD authentication configured
- [ ] All secrets reference Key Vault (no hardcoded values)
- [ ] Health check endpoints (/actuator/health/liveness, /readiness)
- [ ] OpenTelemetry tracing configured
- [ ] Structured JSON logging configured
- [ ] Global exception handler with standard error format
- [ ] Input validation on all endpoints (@Valid)
- [ ] SQL injection prevention (JPA/parameterized queries)
- [ ] OWASP Dependency Check in pom.xml
- [ ] Security headers configured
- [ ] Database migrations in db/migration/
- [ ] Dockerfile with non-root user and health check
- [ ] Kubernetes liveness and readiness probes
- [ ] CI/CD pipeline with security scanning
- [ ] README with setup instructions

## Well-Architected Framework Alignment

Ensure the scaffolded application follows:

- **Reliability**: Circuit breakers, retry logic, health checks
- **Security**: Azure AD, Key Vault, input validation, security headers
- **Cost Optimization**: Resource limits, horizontal pod autoscaling
- **Operational Excellence**: Structured logging, metrics, tracing, IaC
- **Performance Efficiency**: Connection pooling, caching strategies

## References

- **Golden Path Specification**: `mcp-servers/layer2-goldenpaths/web-api/index.md`
- **Security Requirements**: `mcp-servers/layer1-context/security-baseline/index.md`
- **Architecture Patterns**: `mcp-servers/layer1-context/architecture-patterns/`
- **Well-Architected**: `docs/well-architected/`

## Example Usage

User: `@workspace /scaffold-web-api order-service`

You:
1. Ask clarifying questions (resource type, database, Azure env)
2. Generate all files following the Golden Path
3. Run `mvn clean install` to validate
4. Provide next steps:
   ```
   Next steps:
   1. Configure Azure AD app registration
   2. Create Azure Key Vault and add secrets
   3. Set up PostgreSQL database
   4. Run: mvn spring-boot:run
   5. Test: curl http://localhost:8080/actuator/health
   ```

## Anti-Patterns to Avoid

❌ DON'T:
- Hardcode secrets or credentials
- Skip input validation
- Use direct database access across services
- Omit security headers
- Skip health checks
- Use outdated dependencies
- Disable OWASP checks
- Run containers as root
- Skip Kubernetes resource limits

✅ DO:
- Follow the Golden Path exactly
- Reference all patterns from Layer 1 MCPs
- Include comprehensive error handling
- Implement observability from day one
- Generate production-ready configuration
- Include automated testing
- Document setup steps
- Validate security requirements
