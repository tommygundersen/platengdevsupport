# Chat Commands Quick Reference

This is your quick reference for using GitHub Copilot with organizational standards.

> **Note**: This repository is a reference implementation. In production, Layer 1 (standards), Layer 2 (templates), and Skills would be in separate repositories. See [README.md](../README.md) for details.

## 🎬 Complete Workflow (First Time)

```
1. Clone repo          → git clone .../platengdevsupport.git
2. Open in VS Code     → code platengdevsupport/
3. Wait for indexing   → Watch status bar: "Indexing workspace..."
4. Verify skills       → @workspace list skills
5. Use skills!         → @workspace /scaffold-web-api my-service
```

**That's it!** Copilot now knows your standards.

## 🚀 Quick Commands

### Verify Setup First

```bash
# Check you're in the right folder
pwd  # Should show: .../platengdevsupport

# List available skills
@workspace list skills

# Should see:
# - apply-security-baseline
# - scaffold-frontend  
# - scaffold-web-api

# Test context awareness
@workspace show me ADR-001

# Should reference microservices architecture
```

### Scaffold New Services

```
@workspace /scaffold-web-api <name>
```
Creates Java Spring Boot REST API with all best practices

```
@workspace /scaffold-frontend <name>
```
Creates React/TypeScript SPA with all best practices

### Security

```
@workspace /apply-security-baseline
```
Audits and fixes security issues

### Learning

```
@workspace show me database per service pattern
```

```
@workspace how do I implement circuit breakers?
```

```
@workspace what's our authentication standard?
```

## 📝 Command Templates

### Creating a Complete Feature

```
# Backend
@workspace /scaffold-web-api inventory-service

# Frontend
@workspace /scaffold-frontend inventory-dashboard

# Security check
@workspace /apply-security-baseline
```

### Enhancing Existing Code

```
# Add authentication
@workspace add Azure AD authentication to this controller

# Add health checks
@workspace implement Kubernetes health checks

# Add observability
@workspace add OpenTelemetry tracing to this service

# Add validation
@workspace add input validation to these endpoints

# Add error handling
@workspace implement global exception handler
```

### Architecture Questions

```
@workspace why do we use API Gateway?

@workspace show me the saga pattern for distributed transactions

@workspace what's the difference between eventual consistency and strong consistency?

@workspace how should services communicate in our architecture?
```

### Security Questions

```
@workspace how do I store secrets securely?

@workspace show me how to prevent SQL injection

@workspace how do I implement RBAC?

@workspace what security headers are required?
```

## 🎯 Usage Patterns

### Pattern 1: New Microservice + Frontend

```markdown
1. @workspace /scaffold-web-api product-catalog
   - Copilot asks: Resource? → "Product"
   - Copilot asks: Database? → "PostgreSQL"
   - ✅ Complete API generated

2. @workspace /scaffold-frontend product-admin
   - Copilot asks: Resource? → "Product"
   - Copilot asks: Auth? → "Azure AD B2C"
   - ✅ Complete SPA generated

3. git add . && git commit -m "feat: product catalog service"
   - ✅ CI/CD runs automatically
   - ✅ Deploys to Azure
```

### Pattern 2: Secure Existing Application

```markdown
1. @workspace /apply-security-baseline
   - ✅ Copilot audits codebase
   - ✅ Shows all security issues
   - ✅ Prioritizes by severity

2. Review findings and approve fixes
   - ✅ Copilot fixes critical issues
   - ✅ Moves secrets to Key Vault
   - ✅ Adds authentication
   - ✅ Fixes SQL injection

3. @workspace run security scan
   - ✅ Verify all issues resolved
```

### Pattern 3: Learn and Apply

```markdown
1. @workspace show me circuit breaker pattern
   - ✅ Copilot explains pattern
   - ✅ Shows example from golden path

2. @workspace add circuit breaker to OrderService
   - ✅ Copilot implements Resilience4j
   - ✅ Configures sensible defaults
   - ✅ Adds tests
```

## 💡 Tips and Tricks

### Be Specific
❌ "add security"
✅ "add Azure AD authentication with RBAC to my REST API"

### Reference Architecture
❌ "how do I do this?"
✅ "show me the approved pattern for microservices communication"

### Iterate
```
1. @workspace /scaffold-web-api my-service
2. @workspace add Redis caching to this service
3. @workspace add Kafka integration for events
```

### Ask Why
```
@workspace why do we use database-per-service?
@workspace what are the trade-offs of eventual consistency?
@workspace why is Azure AD required?
```

## 🔍 Troubleshooting

### Command Not Working?

```
# Check if workspace is indexed
@workspace refresh

# Be more explicit
Instead of: "/scaffold api"
Try: "/scaffold-web-api order-service"

# Check skill is available
@workspace list available skills
```

### Generated Code Has Issues?

```
# Run diagnostics
@workspace check for errors in generated code

# Fix specific issue
@workspace fix the authentication configuration

# Regenerate
@workspace delete and regenerate SecurityConfig.java
```

## 📚 What Gets Automatically Applied

When you use these commands, Copilot **automatically** includes:

### Security ✅
- Azure AD authentication
- Secrets in Key Vault
- Input validation
- SQL injection prevention
- XSS prevention
- Security headers
- Dependency scanning

### Reliability ✅
- Health checks
- Circuit breakers
- Retry logic
- Error handling

### Observability ✅
- Structured logging
- Distributed tracing
- Metrics
- Correlation IDs

### Operations ✅
- Docker files
- Kubernetes manifests
- CI/CD pipelines
- Documentation

## 🎓 Learning Resources

```
@workspace show me examples of:
- REST API best practices
- React security patterns
- Database migration patterns
- Error handling strategies
- Testing strategies
```

## 📞 Get Help

- Slack: #platform-engineering
- Office Hours: Tuesdays 2-4 PM
- Documentation: /docs folder
- Examples: /templates folder

## 🔄 Keep This Updated

This is a living document. When you discover useful commands or patterns:

```
@workspace add this pattern to the quick reference

# Or manually:
# Edit: docs/quick-reference.md
# Submit PR
```

---

**Remember**: These commands generate **production-ready** code. Focus on your business logic—we've got the plumbing covered! 🚀
