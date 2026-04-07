# Golden Path: Java Spring Boot Web API

**Status**: Approved  
**Last Updated**: 2026-04-07  
**Maintainer**: Platform Engineering Team  
**Well-Architected Pillars**: All

## Overview

This is the **authoritative template** for building REST APIs using Java, Spring Boot, and Maven. When you scaffold a new API service, this is what gets generated.

## 🎯 What You Get

A production-ready API with:
- ✅ Azure AD authentication pre-configured
- ✅ PostgreSQL or MongoDB support
- ✅ OpenTelemetry observability
- ✅ Health checks and readiness probes
- ✅ Structured logging
- ✅ Error handling patterns
- ✅ CI/CD pipeline configurations
- ✅ Dockerfile and Kubernetes manifests

## 🏗️ Project Structure

```
my-service/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/organization/myservice/
│   │   │       ├── MyServiceApplication.java
│   │   │       ├── config/
│   │   │       │   ├── SecurityConfig.java
│   │   │       │   ├── DatabaseConfig.java
│   │   │       │   ├── ObservabilityConfig.java
│   │   │       │   └── CorsConfig.java
│   │   │       ├── controller/
│   │   │       │   ├── HealthController.java
│   │   │       │   └── ResourceController.java
│   │   │       ├── service/
│   │   │       │   └── ResourceService.java
│   │   │       ├── repository/
│   │   │       │   └── ResourceRepository.java
│   │   │       ├── model/
│   │   │       │   ├── entity/
│   │   │       │   └── dto/
│   │   │       ├── exception/
│   │   │       │   ├── GlobalExceptionHandler.java
│   │   │       │   └── BusinessException.java
│   │   │       └── util/
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── application-dev.yml
│   │       ├── application-prod.yml
│   │       └── db/migration/
│   │           └── V1__initial_schema.sql
│   └── test/
│       └── java/
│           └── com/organization/myservice/
│               ├── integration/
│               └── unit/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── cd.yml
├── k8s/
│   ├── deployment.yml
│   ├── service.yml
│   └── ingress.yml
├── Dockerfile
├── pom.xml
└── README.md
```

## 🔧 Required Dependencies (pom.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
    </parent>
    
    <groupId>com.organization</groupId>
    <artifactId>my-service</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <name>my-service</name>
    
    <properties>
        <java.version>21</java.version>
        <azure.version>5.7.0</azure.version>
        <opentelemetry.version>1.32.0</opentelemetry.version>
    </properties>
    
    <dependencies>
        <!-- Spring Boot Starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <!-- Security: Azure AD OAuth2 -->
        <dependency>
            <groupId>com.azure.spring</groupId>
            <artifactId>spring-cloud-azure-starter-active-directory</artifactId>
        </dependency>
        
        <!-- Database: PostgreSQL -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        
        <!-- Database Migrations: Flyway -->
        <dependency>
            <groupId>org.flywaydb</groupId>
            <artifactId>flyway-core</artifactId>
        </dependency>
        
        <!-- Observability: OpenTelemetry -->
        <dependency>
            <groupId>io.opentelemetry.instrumentation</groupId>
            <artifactId>opentelemetry-spring-boot-starter</artifactId>
            <version>${opentelemetry.version}</version>
        </dependency>
        
        <!-- Azure SDK: Key Vault -->
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-security-keyvault-secrets</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-identity</artifactId>
        </dependency>
        
        <!-- Validation -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        
        <!-- Resilience: Circuit Breaker -->
        <dependency>
            <groupId>io.github.resilience4j</groupId>
            <artifactId>resilience4j-spring-boot3</artifactId>
            <version>2.1.0</version>
        </dependency>
        
        <!-- Health Checks -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        
        <!-- Logging: Structured JSON -->
        <dependency>
            <groupId>net.logstash.logback</groupId>
            <artifactId>logstash-logback-encoder</artifactId>
            <version>7.4</version>
        </dependency>
        
        <!-- Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>postgresql</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.azure.spring</groupId>
                <artifactId>spring-cloud-azure-dependencies</artifactId>
                <version>${azure.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            
            <!-- Security: Dependency Vulnerability Scanning -->
            <plugin>
                <groupId>org.owasp</groupId>
                <artifactId>dependency-check-maven</artifactId>
                <version>9.0.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>check</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

## 📝 Application Configuration (application.yml)

```yaml
# application.yml - Base configuration
spring:
  application:
    name: my-service
  
  # Security: Azure AD
  cloud:
    azure:
      active-directory:
        enabled: true
        profile:
          tenant-id: ${AZURE_TENANT_ID}
        credential:
          client-id: ${AZURE_CLIENT_ID}
          client-secret: ${AZURE_CLIENT_SECRET}
        app-id-uri: api://${AZURE_CLIENT_ID}
        authorization-clients:
          graph:
            scopes:
              - https://graph.microsoft.com/User.Read
  
  # Database: PostgreSQL
  datasource:
    url: jdbc:postgresql://${DB_HOST:localhost}:${DB_PORT:5432}/${DB_NAME:myservice}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
      leak-detection-threshold: 60000
  
  # JPA
  jpa:
    hibernate:
      ddl-auto: validate  # Use Flyway for schema management
    show-sql: false
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
        format_sql: true
  
  # Flyway Migrations
  flyway:
    enabled: true
    baseline-on-migrate: true
    locations: classpath:db/migration

# Actuator: Health Checks
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
      base-path: /actuator
  endpoint:
    health:
      show-details: when-authorized
      probes:
        enabled: true
  health:
    readiness-state:
      enabled: true
    liveness-state:
      enabled: true
  metrics:
    distribution:
      percentiles-histogram:
        http.server.requests: true

# Observability: OpenTelemetry
otel:
  traces:
    exporter: otlp
  metrics:
    exporter: otlp
  exporter:
    otlp:
      endpoint: ${OTEL_EXPORTER_OTLP_ENDPOINT:http://localhost:4317}
  service:
    name: ${spring.application.name}

# Resilience4j: Circuit Breaker
resilience4j:
  circuitbreaker:
    configs:
      default:
        slidingWindowSize: 100
        permittedNumberOfCallsInHalfOpenState: 10
        waitDurationInOpenState: 10000
        failureRateThreshold: 50
        slowCallRateThreshold: 50
        slowCallDurationThreshold: 2000

# Logging
logging:
  level:
    root: INFO
    com.organization: DEBUG
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %logger{36} - %msg%n"
```

## 🔐 Security Configuration

```java
package com.organization.myservice.config;

import com.azure.spring.cloud.autoconfigure.aad.AadWebSecurityConfigurerAdapter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;

/**
 * Security configuration - MUST implement for all services
 * Based on: security-baseline Layer 1 MCP
 */
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // Authentication: Azure AD OAuth2
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> {})
            )
            
            // Authorization: Endpoint rules
            .authorizeHttpRequests(authz -> authz
                // Public endpoints
                .requestMatchers("/actuator/health/**", "/actuator/info").permitAll()
                
                // All other endpoints require authentication
                .anyRequest().authenticated()
            )
            
            // Session: Stateless (no server-side sessions)
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            
            // CSRF: Not needed for stateless API
            .csrf(csrf -> csrf.disable())
            
            // Headers: Security hardening
            .headers(headers -> headers
                .contentSecurityPolicy(csp -> csp
                    .policyDirectives("default-src 'self'")
                )
                .frameOptions(frame -> frame.deny())
                .xssProtection(xss -> xss.block(true))
            );
        
        return http.build();
    }
}
```

## 🎯 Controller Example

```java
package com.organization.myservice.controller;

import com.organization.myservice.model.dto.ResourceRequest;
import com.organization.myservice.model.dto.ResourceResponse;
import com.organization.myservice.service.ResourceService;
import io.opentelemetry.api.trace.Span;
import io.opentelemetry.api.trace.Tracer;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.jwt.Jwt;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.UUID;

/**
 * Resource REST Controller
 * 
 * Standards:
 * - Azure AD authentication required (via SecurityConfig)
 * - Input validation with @Valid
 * - Structured logging with correlation IDs
 * - OpenTelemetry distributed tracing
 * - Standard error responses (via GlobalExceptionHandler)
 */
@RestController
@RequestMapping("/api/v1/resources")
@RequiredArgsConstructor
@Slf4j
public class ResourceController {
    
    private final ResourceService resourceService;
    private final Tracer tracer;
    
    /**
     * List all resources
     * Required permission: read:resources
     */
    @GetMapping
    @PreAuthorize("hasAuthority('SCOPE_read:resources')")
    public ResponseEntity<List<ResourceResponse>> listResources(
            @AuthenticationPrincipal Jwt jwt) {
        
        Span span = tracer.spanBuilder("listResources").startSpan();
        try {
            String userId = jwt.getSubject();
            log.info("Listing resources for user: {}", userId);
            
            List<ResourceResponse> resources = resourceService.findAll(userId);
            return ResponseEntity.ok(resources);
            
        } finally {
            span.end();
        }
    }
    
    /**
     * Get resource by ID
     * Required permission: read:resources
     */
    @GetMapping("/{id}")
    @PreAuthorize("hasAuthority('SCOPE_read:resources')")
    public ResponseEntity<ResourceResponse> getResource(
            @PathVariable UUID id,
            @AuthenticationPrincipal Jwt jwt) {
        
        log.info("Getting resource: {} for user: {}", id, jwt.getSubject());
        
        ResourceResponse resource = resourceService.findById(id, jwt.getSubject());
        return ResponseEntity.ok(resource);
    }
    
    /**
     * Create new resource
     * Required permission: write:resources
     */
    @PostMapping
    @PreAuthorize("hasAuthority('SCOPE_write:resources')")
    public ResponseEntity<ResourceResponse> createResource(
            @Valid @RequestBody ResourceRequest request,
            @AuthenticationPrincipal Jwt jwt) {
        
        log.info("Creating resource for user: {}", jwt.getSubject());
        
        ResourceResponse resource = resourceService.create(request, jwt.getSubject());
        return ResponseEntity.status(HttpStatus.CREATED).body(resource);
    }
    
    /**
     * Update existing resource
     * Required permission: write:resources
     */
    @PutMapping("/{id}")
    @PreAuthorize("hasAuthority('SCOPE_write:resources')")
    public ResponseEntity<ResourceResponse> updateResource(
            @PathVariable UUID id,
            @Valid @RequestBody ResourceRequest request,
            @AuthenticationPrincipal Jwt jwt) {
        
        log.info("Updating resource: {} for user: {}", id, jwt.getSubject());
        
        ResourceResponse resource = resourceService.update(id, request, jwt.getSubject());
        return ResponseEntity.ok(resource);
    }
    
    /**
     * Delete resource
     * Required permission: delete:resources
     */
    @DeleteMapping("/{id}")
    @PreAuthorize("hasAuthority('SCOPE_delete:resources')")
    public ResponseEntity<Void> deleteResource(
            @PathVariable UUID id,
            @AuthenticationPrincipal Jwt jwt) {
        
        log.info("Deleting resource: {} for user: {}", id, jwt.getSubject());
        
        resourceService.delete(id, jwt.getSubject());
        return ResponseEntity.noContent().build();
    }
}
```

## 🚨 Global Exception Handler

```java
package com.organization.myservice.exception;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.Instant;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

/**
 * Global exception handler - Standard error response format
 * 
 * All errors return:
 * {
 *   "timestamp": "2026-04-07T10:15:30Z",
 *   "requestId": "uuid",
 *   "status": 400,
 *   "error": "Bad Request",
 *   "message": "Validation failed",
 *   "details": {...}
 * }
 */
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException ex) {
        
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach(error -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .requestId(UUID.randomUUID().toString())
            .status(HttpStatus.BAD_REQUEST.value())
            .error("Bad Request")
            .message("Validation failed")
            .details(errors)
            .build();
        
        log.warn("Validation error: {}", errors);
        
        return ResponseEntity.badRequest().body(error);
    }
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(
            ResourceNotFoundException ex) {
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .requestId(UUID.randomUUID().toString())
            .status(HttpStatus.NOT_FOUND.value())
            .error("Not Found")
            .message(ex.getMessage())
            .build();
        
        log.warn("Resource not found: {}", ex.getMessage());
        
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<ErrorResponse> handleAccessDenied(
            AccessDeniedException ex) {
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .requestId(UUID.randomUUID().toString())
            .status(HttpStatus.FORBIDDEN.value())
            .error("Forbidden")
            .message("Access denied")
            .build();
        
        log.warn("Access denied: {}", ex.getMessage());
        
        return ResponseEntity.status(HttpStatus.FORBIDDEN).body(error);
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .requestId(UUID.randomUUID().toString())
            .status(HttpStatus.INTERNAL_SERVER_ERROR.value())
            .error("Internal Server Error")
            .message("An unexpected error occurred")
            .build();
        
        log.error("Unexpected error", ex);
        
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

## 🐳 Dockerfile (Multi-stage Build)

```dockerfile
# Build stage
FROM maven:3.9-eclipse-temurin-21-alpine AS build
WORKDIR /app

# Copy pom.xml and download dependencies (cached layer)
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy source and build
COPY src ./src
RUN mvn clean package -DskipTests

# Runtime stage
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy JAR from build stage
COPY --from=build /app/target/*.jar app.jar

# Set ownership
RUN chown -R appuser:appgroup /app

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

# Run application
ENTRYPOINT ["java", \
  "-XX:+UseContainerSupport", \
  "-XX:MaxRAMPercentage=75.0", \
  "-Djava.security.egd=file:/dev/./urandom", \
  "-jar", "app.jar"]
```

## ☸️ Kubernetes Deployment

```yaml
# k8s/deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-service
  labels:
    app: my-service
    version: v1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-service
  template:
    metadata:
      labels:
        app: my-service
        version: v1
    spec:
      serviceAccountName: my-service
      containers:
      - name: my-service
        image: myregistry.azurecr.io/my-service:latest
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: AZURE_CLIENT_ID
          valueFrom:
            secretKeyRef:
              name: azure-credentials
              key: client-id
        - name: AZURE_TENANT_ID
          valueFrom:
            secretKeyRef:
              name: azure-credentials
              key: tenant-id
        - name: DB_HOST
          value: "postgres.database.azure.com"
        - name: DB_NAME
          value: "myservice"
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: database-credentials
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-credentials
              key: password
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 3
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 3
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
```

## 📊 Non-Functional Requirements

| Requirement | Target | Enforcement |
|-------------|--------|-------------|
| **Response Time** | P95 < 200ms | Load testing in CI |
| **Availability** | 99.9% | SLO monitoring |
| **Error Rate** | < 0.1% | Alerting thresholds |
| **Security Scan** | Zero high/critical | CI pipeline fails |
| **Test Coverage** | > 80% | CI pipeline enforcement |
| **Build Time** | < 5 minutes | Optimize dependencies |

## ✅ Pre-Production Checklist

Before deploying to production:

- [ ] Azure AD authentication configured
- [ ] Secrets in Key Vault (no hardcoded secrets)
- [ ] Database migrations tested
- [ ] Health checks responding correctly
- [ ] OpenTelemetry tracing configured
- [ ] Structured logging implemented
- [ ] Error handling tested
- [ ] Security headers configured
- [ ] Dependency vulnerabilities scanned
- [ ] Load testing completed
- [ ] Kubernetes resource limits set
- [ ] Horizontal Pod Autoscaler configured
- [ ] Alert rules configured
- [ ] Documentation updated

## 🚀 Getting Started

To create a new service from this template:

```bash
# Use the scaffold-web-api skill
@workspace /scaffold-web-api my-new-service \
  --database=postgresql \
  --include-mongodb-example=false
```

This will generate a complete, production-ready service!

## 📚 Related

- [ADR-001: Microservices Architecture](../../layer1-context/architecture-patterns/adr/001-microservices-architecture.md)
- [Security Baseline](../../layer1-context/security-baseline/index.md)
- [Frontend Golden Path](../frontend-spa/index.md)
