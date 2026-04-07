# Security Baseline - Authoritative Requirements

**Version**: 1.0.0  
**Last Updated**: 2026-04-07  
**Owner**: Security Architecture Team  
**Well-Architected Pillar**: Security

## Overview

This document defines the **non-negotiable security requirements** for all applications. Every service MUST comply with these baselines before production deployment.

## Zero Trust Security Model

We follow a Zero Trust approach:
- **Never trust, always verify**
- **Assume breach**
- **Verify explicitly**
- **Use least privilege access**
- **Segment access**

## 🔒 Authentication & Identity

### MUST Implement ✅

1. **Azure Active Directory Integration**
   ```yaml
   Authentication:
     Provider: Azure AD / Azure AD B2C
     Protocol: OAuth 2.0 / OpenID Connect
     TokenValidation: Required
     ClaimsValidation: Required
   ```

2. **Service-to-Service Authentication**
   - Use Managed Identities for Azure resources
   - mTLS for inter-service communication
   - No shared secrets between services
   - API keys ONLY for external partners (with rotation policy)

3. **User Authentication**
   - Multi-factor Authentication (MFA) for production access
   - Session timeout: 8 hours max
   - Idle timeout: 30 minutes
   - Secure cookie configuration (HttpOnly, Secure, SameSite)

### Example: Java Spring Boot Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .oauth2Login(oauth2 -> oauth2
                .userInfoEndpoint(userInfo -> userInfo
                    .userAuthoritiesMapper(grantedAuthoritiesMapper())
                )
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt
                    .jwtAuthenticationConverter(jwtAuthenticationConverter())
                )
            )
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            );
        
        return http.build();
    }
}
```

### Example: React/TypeScript Frontend

```typescript
// Use MSAL for Azure AD authentication
import { PublicClientApplication } from "@azure/msal-browser";

const msalConfig = {
  auth: {
    clientId: process.env.REACT_APP_AZURE_CLIENT_ID!,
    authority: `https://login.microsoftonline.com/${process.env.REACT_APP_AZURE_TENANT_ID}`,
    redirectUri: window.location.origin,
  },
  cache: {
    cacheLocation: "sessionStorage", // or "localStorage"
    storeAuthStateInCookie: false,
  },
};

export const msalInstance = new PublicClientApplication(msalConfig);

// Protected API calls
export async function callProtectedAPI(endpoint: string) {
  const account = msalInstance.getActiveAccount();
  const response = await msalInstance.acquireTokenSilent({
    scopes: ["api://your-api/.default"],
    account: account!,
  });
  
  return fetch(endpoint, {
    headers: {
      Authorization: `Bearer ${response.accessToken}`,
      'Content-Type': 'application/json',
    },
  });
}
```

## 🔑 Secrets Management

### MUST Follow ✅

1. **Never Hardcode Secrets**
   - No secrets in code, configuration files, or environment variables in repos
   - All secrets stored in Azure Key Vault
   - Use Managed Identities to access Key Vault
   - Rotate secrets every 90 days (automatic where possible)

2. **Access Pattern**
   ```java
   // Java example using Azure SDK
   @Configuration
   public class SecretsConfig {
       
       @Bean
       public SecretClient secretClient() {
           String keyVaultUrl = "https://<vault-name>.vault.azure.net/";
           return new SecretClientBuilder()
               .vaultUrl(keyVaultUrl)
               .credential(new DefaultAzureCredentialBuilder().build())
               .buildClient();
       }
       
       @Bean
       public String databasePassword(SecretClient secretClient) {
           return secretClient.getSecret("database-password").getValue();
       }
   }
   ```

3. **Connection Strings**
   - Never log connection strings
   - Use connection string builders with Key Vault integration
   - Separate credentials for each environment

### DON'T ❌

- ❌ Store secrets in application.properties or appsettings.json
- ❌ Commit .env files to Git
- ❌ Use same secrets across environments
- ❌ Share credentials via email or chat
- ❌ Log secret values (even partially)

## 🛡️ Authorization & Access Control

### Role-Based Access Control (RBAC)

```typescript
// Example role definitions
export enum Role {
  VIEWER = "viewer",        // Read-only access
  EDITOR = "editor",        // Read + write
  ADMIN = "admin",          // Full access
  SERVICE_ACCOUNT = "service_account"
}

// Permission-based checks
export interface Permission {
  resource: string;
  action: "create" | "read" | "update" | "delete";
}

export function checkPermission(
  user: User,
  permission: Permission
): boolean {
  return user.roles.some(role => 
    rolePermissions[role]?.includes(permission)
  );
}
```

### Principle of Least Privilege

- Grant minimum permissions necessary
- Use resource-level permissions (not just service-level)
- Regular access reviews (quarterly)
- Time-bound elevated access (for admin operations)

## 🔐 Data Protection

### Data Classification

| Level | Description | Examples | Encryption |
|-------|-------------|----------|------------|
| **Public** | Can be disclosed | Marketing content | Optional |
| **Internal** | Internal use only | Business metrics | At-rest |
| **Confidential** | Sensitive business data | Customer PII, Financial | At-rest + in-transit |
| **Restricted** | Highly sensitive | Payment cards, Health data | At-rest + in-transit + field-level |

### Encryption Requirements

#### MUST Implement ✅

1. **Data at Rest**
   ```yaml
   Database:
     Encryption: AES-256
     StorageEncryption: Azure Storage Service Encryption (SSE)
     DiskEncryption: Azure Disk Encryption
   ```

2. **Data in Transit**
   - TLS 1.2 minimum (TLS 1.3 preferred)
   - No SSL or TLS 1.0/1.1
   - HTTPS only for all endpoints
   - Certificate pinning for mobile apps

3. **Field-Level Encryption**
   ```java
   // For PII and payment data
   @Entity
   public class UserProfile {
       @Column(name = "ssn")
       @Convert(converter = SensitiveDataConverter.class)
       private String socialSecurityNumber;
       
       @Column(name = "credit_card")
       @Convert(converter = SensitiveDataConverter.class)
       private String creditCardNumber;
   }
   
   @Component
   public class SensitiveDataConverter 
           implements AttributeConverter<String, String> {
       
       @Autowired
       private EncryptionService encryptionService;
       
       @Override
       public String convertToDatabaseColumn(String attribute) {
           return encryptionService.encrypt(attribute);
       }
       
       @Override
       public String convertToEntityAttribute(String dbData) {
           return encryptionService.decrypt(dbData);
       }
   }
   ```

### Personal Identifiable Information (PII)

**PII Fields Require Special Handling**:
- Email addresses, phone numbers
- Names, addresses
- Government IDs (SSN, passport numbers)
- Biometric data
- IP addresses (in some jurisdictions)

**Requirements**:
- ✅ Encrypt at rest and in transit
- ✅ Mask in logs and error messages
- ✅ Audit all access
- ✅ Support data deletion (right to be forgotten)
- ✅ Geographic data residency compliance

## 🚫 Input Validation & Sanitization

### MUST Implement ✅

```java
// Backend validation (Java)
public class OrderRequest {
    
    @NotNull(message = "Customer ID is required")
    @Pattern(regexp = "^[a-zA-Z0-9-]+$")
    private String customerId;
    
    @NotNull
    @DecimalMin(value = "0.01")
    @DecimalMax(value = "999999.99")
    private BigDecimal amount;
    
    @NotEmpty
    @Size(max = 500)
    @NoHtml // Custom validator to prevent XSS
    private String description;
}

@RestController
public class OrderController {
    
    @PostMapping("/orders")
    public ResponseEntity<Order> createOrder(
            @Valid @RequestBody OrderRequest request) {
        // Validation happens automatically
        return ResponseEntity.ok(orderService.create(request));
    }
}
```

```typescript
// Frontend validation (React/TypeScript)
import { z } from "zod";

const OrderSchema = z.object({
  customerId: z.string().regex(/^[a-zA-Z0-9-]+$/),
  amount: z.number().min(0.01).max(999999.99),
  description: z.string().max(500),
});

export function CreateOrderForm() {
  const [formData, setFormData] = useState<OrderFormData>({});
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    // Client-side validation
    const result = OrderSchema.safeParse(formData);
    if (!result.success) {
      showErrors(result.error);
      return;
    }
    
    // API call
    await createOrder(result.data);
  };
}
```

### SQL Injection Prevention

```java
// ✅ GOOD: Use parameterized queries
@Repository
public class OrderRepository {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    public List<Order> findByCustomer(String customerId) {
        String sql = "SELECT * FROM orders WHERE customer_id = ?";
        return jdbcTemplate.query(sql, 
            new Object[]{customerId}, 
            orderRowMapper);
    }
}

// ❌ BAD: String concatenation
public List<Order> findByCustomerBad(String customerId) {
    String sql = "SELECT * FROM orders WHERE customer_id = '" + customerId + "'";
    return jdbcTemplate.query(sql, orderRowMapper); // SQL injection vulnerability!
}
```

### XSS Prevention

```typescript
// ✅ GOOD: React automatically escapes
export function UserComment({ comment }: { comment: string }) {
  return <div>{comment}</div>; // Automatically escaped
}

// ❌ BAD: Using dangerouslySetInnerHTML without sanitization
export function UserCommentBad({ comment }: { comment: string }) {
  return <div dangerouslySetInnerHTML={{ __html: comment }} />; 
  // XSS vulnerability!
}

// ✅ GOOD: If you must use HTML, sanitize first
import DOMPurify from 'dompurify';

export function UserCommentSafe({ comment }: { comment: string }) {
  const sanitized = DOMPurify.sanitize(comment);
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

## 🔍 Security Logging & Monitoring

### MUST Log ✅

1. **Authentication Events**
   - Login attempts (success/failure)
   - Logout events
   - Token refresh
   - Password changes
   - MFA challenges

2. **Authorization Events**
   - Access denied events
   - Privilege escalation attempts
   - Role/permission changes

3. **Data Access**
   - PII access (who, what, when)
   - Sensitive data exports
   - Bulk data operations

4. **Security Events**
   - Suspected attacks (SQL injection, XSS attempts)
   - Rate limit violations
   - Certificate errors
   - Configuration changes

### MUST NOT Log ❌

- ❌ Passwords or password hashes
- ❌ Complete credit card numbers
- ❌ Social Security Numbers
- ❌ Full authentication tokens
- ❌ Encryption keys
- ❌ Complete PII in plaintext

### Secure Logging Example

```java
@Aspect
@Component
public class SecurityAuditAspect {
    
    @Autowired
    private AuditLogger auditLogger;
    
    @Around("@annotation(Audited)")
    public Object auditMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        String user = SecurityContextHolder.getContext()
            .getAuthentication().getName();
        String method = joinPoint.getSignature().getName();
        String params = maskSensitiveData(joinPoint.getArgs());
        
        auditLogger.info("User: {}, Method: {}, Params: {}", 
            user, method, params);
        
        try {
            Object result = joinPoint.proceed();
            auditLogger.info("User: {}, Method: {}, Status: SUCCESS", 
                user, method);
            return result;
        } catch (Exception e) {
            auditLogger.error("User: {}, Method: {}, Status: FAILURE, Error: {}", 
                user, method, e.getMessage());
            throw e;
        }
    }
    
    private String maskSensitiveData(Object[] args) {
        // Mask credit cards, SSN, passwords, etc.
        // Return safe representation
    }
}
```

## 🚨 Security Headers

### Required HTTP Headers

```java
@Configuration
public class SecurityHeadersConfig {
    
    @Bean
    public FilterRegistrationBean<SecurityHeadersFilter> securityHeaders() {
        FilterRegistrationBean<SecurityHeadersFilter> registration = 
            new FilterRegistrationBean<>();
        registration.setFilter(new SecurityHeadersFilter());
        registration.addUrlPatterns("/*");
        return registration;
    }
}

public class SecurityHeadersFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException {
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        
        // MUST include these headers
        httpResponse.setHeader("Strict-Transport-Security", 
            "max-age=31536000; includeSubDomains");
        httpResponse.setHeader("X-Content-Type-Options", "nosniff");
        httpResponse.setHeader("X-Frame-Options", "DENY");
        httpResponse.setHeader("X-XSS-Protection", "1; mode=block");
        httpResponse.setHeader("Content-Security-Policy", 
            "default-src 'self'; script-src 'self'; object-src 'none'");
        httpResponse.setHeader("Referrer-Policy", "strict-origin-when-cross-origin");
        httpResponse.setHeader("Permissions-Policy", 
            "geolocation=(), microphone=(), camera=()");
        
        chain.doFilter(request, response);
    }
}
```

## 🔄 Dependency Management

### MUST Follow ✅

1. **Regular Updates**
   - Update dependencies monthly (minimum)
   - Critical security patches within 7 days
   - Monitor CVE databases

2. **Vulnerability Scanning**
   ```yaml
   # Maven (pom.xml)
   <build>
     <plugins>
       <plugin>
         <groupId>org.owasp</groupId>
         <artifactId>dependency-check-maven</artifactId>
         <version>8.0.0</version>
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
   ```
   
   ```json
   // npm (package.json)
   {
     "scripts": {
       "audit": "npm audit",
       "audit:fix": "npm audit fix"
     }
   }
   ```

3. **Approved Libraries**
   - Use organization-vetted libraries only
   - No dependencies from untrusted sources
   - Regular license compliance checks

## 📋 Security Checklist

Before production deployment:

- [ ] Azure AD authentication configured
- [ ] All secrets in Key Vault
- [ ] HTTPS/TLS 1.2+ enforced
- [ ] input validation on all endpoints
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention implemented
- [ ] CSRF protection enabled
- [ ] Security headers configured
- [ ] PII encrypted at rest and in transit
- [ ] Security logging implemented
- [ ] Dependency vulnerability scan passed
- [ ] Penetration test completed
- [ ] Security code review completed

## 🚫 Common Vulnerabilities to Avoid

OWASP Top 10:

1. **Broken Access Control** → Use RBAC, check on every request
2. **Cryptographic Failures** → Use TLS 1.2+, encrypt sensitive data
3. **Injection** → Use parameterized queries, validation
4. **Insecure Design** → Follow security by design principles
5. **Security Misconfiguration** → Harden defaults, disable debug mode
6. **Vulnerable Components** → Keep dependencies updated
7. **Authentication Failures** → Use MFA, strong session management
8. **Data Integrity Failures** → Validate integrity, use HMAC
9. **Logging Failures** → Log security events, monitor actively
10. **SSRF** → Validate and sanitize URLs, whitelist destinations

## 📚 Related Documents

- [Compliance Regulations](../compliance-regulations/)
- [Platform Security Assumptions](../platform-assumptions/security.md)
- [Incident Response Plan](./incident-response.md)

## 📞 Contact

- **Security Team**: security@organization.com
- **Security Incident**: security-incident@organization.com (24/7)
- **Security Champions**: #security-champions (Slack)
