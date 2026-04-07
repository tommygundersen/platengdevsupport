# ADR-002: API Gateway Pattern

**Status**: Accepted  
**Date**: 2026-01-20  
**Deciders**: Architecture Review Board  
**Well-Architected Pillars**: Security, Performance Efficiency, Reliability

## Context

With microservices architecture, clients need a single entry point rather than connecting directly to dozens of backend services. We need centralized cross-cutting concerns like authentication, rate limiting, and routing.

## Decision

All external API traffic MUST route through an **API Gateway** with the following requirements:

### Gateway Responsibilities

#### MUST Implement ✅

1. **Authentication & Authorization**
   - Azure AD B2C integration for external users
   - Azure AD for internal users
   - JWT validation and claims extraction
   - API key management for service accounts

2. **Rate Limiting**
   - Per-client rate limits
   - Per-endpoint throttling
   - Quota management
   - Circuit breaker integration

3. **Routing & Load Balancing**
   - Path-based routing to backend services
   - Header-based routing (A/B testing, canary)
   - Service discovery integration
   - Automatic retry with exponential backoff

4. **Security**
   - TLS/SSL termination
   - CORS policy enforcement
   - Request validation
   - Response sanitization
   - OWASP Top 10 protections

5. **Observability**
   - Request/response logging
   - Distributed tracing (OpenTelemetry)
   - Metrics collection (latency, errors, requests/sec)
   - Correlation ID injection

### Gateway MUST NOT ❌

- **Business Logic**: No business rules in gateway
- **Data Transformation**: Minimal data mapping only
- **State Management**: Stateless only
- **Database Access**: No direct DB calls
- **Service Orchestration**: Use BFF pattern instead

## Technology Choice

**Primary**: Azure API Management (APIM)
**Alternative**: Kong Gateway (for Kubernetes deployments)

### Configuration Example

```yaml
# API Gateway Route Definition
routes:
  - path: /api/orders/*
    backend: orders-service
    auth: required
    rateLimit: 100/minute
    timeout: 30s
    
  - path: /api/products/*
    backend: products-service
    auth: optional
    rateLimit: 1000/minute
    caching: 5m
    timeout: 10s
```

## Routing Patterns

### Standard Pattern
```
Client → API Gateway → Backend Service
         ↓
      [Auth, Rate Limit, Route]
```

### Backend-for-Frontend (BFF) Pattern
```
Mobile App → API Gateway → Mobile BFF → Services
Web App    → API Gateway → Web BFF    → Services
                           ↓
                      [Aggregation Logic]
```

## Non-Functional Requirements

| Requirement | Target | Measurement |
|-------------|--------|-------------|
| **Latency** | < 50ms added | P95 latency |
| **Availability** | 99.95% | Monthly uptime |
| **Throughput** | 10,000 req/sec | Per gateway instance |
| **Error Rate** | < 0.1% | 4xx/5xx responses |

## Security Policies

### Mandatory Headers

All requests must include:
```
X-Request-ID: <correlation-id>
Authorization: Bearer <jwt-token>
X-Client-Version: <app-version>
```

All responses include:
```
X-Request-ID: <correlation-id>
X-RateLimit-Remaining: <count>
X-RateLimit-Reset: <timestamp>
Strict-Transport-Security: max-age=31536000
```

### DO ✅

- Validate all input at gateway
- Enforce HTTPS only
- Log all 4xx/5xx responses
- Implement request signing for service accounts
- Use mTLS for backend communication

### DON'T ❌

- Log sensitive data (tokens, passwords, PII)
- Allow direct backend access
- Bypass authentication for "internal" endpoints
- Trust client-provided headers without validation

## Consequences

### Positive
- ✅ Single point of entry for security policies
- ✅ Centralized monitoring and logging
- ✅ Backend services isolated from internet
- ✅ Rate limiting and DDoS protection
- ✅ Simplified client development

### Negative
- ❌ Single point of failure (mitigated by HA deployment)
- ❌ Additional latency hop (~20-50ms)
- ❌ Potential bottleneck (mitigated by horizontal scaling)

## Implementation Checklist

- [ ] Deploy API Gateway in HA mode (minimum 2 instances)
- [ ] Configure Azure AD integration
- [ ] Set up rate limiting policies
- [ ] Implement distributed tracing
- [ ] Configure backend health checks
- [ ] Set up alerts for error rates > 0.5%
- [ ] Document all API routes
- [ ] Configure CORS policies
- [ ] Implement circuit breakers
- [ ] Set up metrics dashboard

## Related

- ADR-001: Microservices Architecture
- Security Baseline: Authentication Requirements
- Platform Assumptions: Azure Services
