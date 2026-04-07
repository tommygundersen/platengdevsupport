# Developer Usage Guide

Welcome to the Platform Engineering Developer Support framework! This guide shows you how to use GitHub Copilot to build applications that automatically follow organizational best practices.

## ⚠️ Note: Reference Implementation

**This repository demonstrates how the components work together.** In a real organization, Layer 1 (standards), Layer 2 (templates), Skills (tools), and SDKs would live in separate repositories with different owners and release cycles. See the main [README](../README.md) for production deployment guidance.

---

## 🔍 How Copilot Discovers Your Standards (Understanding the Magic)

Before diving into commands, understanding how this works helps you set it up correctly.

### The Automatic Discovery Process

```
┌─────────────────────────────────────────────────────────────────┐
│ Step 1: You Open Workspace                                     │
│ $ code platengdevsupport/                                       │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 2: Copilot Indexes Workspace (automatic, 1-2 min)         │
│                                                                 │
│  Discovers:                                                     │
│  📁 skills/**/*.md         → Registers as /commands             │
│  📁 mcp-servers/layer1-*/  → Indexes for rules & standards      │
│  📁 mcp-servers/layer2-*/  → Indexes for templates & examples   │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 3: Skills Become Available in Chat                        │
│                                                                 │
│  /scaffold-web-api      ← from skills/scaffold-web-api/        │
│  /scaffold-frontend     ← from skills/scaffold-frontend/       │
│  /apply-security-baseline ← from skills/apply-security-...     │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│ Step 4: You Invoke Skills                                      │
│                                                                 │
│  @workspace /scaffold-web-api my-service                        │
│       ↓                                                         │
│  Skill executes → references golden paths → applies ADRs       │
│       ↓                                                         │
│  Production-ready code generated ✓                             │
└─────────────────────────────────────────────────────────────────┘
```

### Required Workspace Structure

For automatic discovery to work:

✅ **Must have `skills/` folder in workspace root**
```
platengdevsupport/        ← Open this level in VS Code
├── skills/               ← Copilot finds skills here
│   ├── scaffold-web-api/
│   │   └── SKILL.md
│   └── scaffold-frontend/
│       └── SKILL.md
```

✅ **Each SKILL.md needs YAML frontmatter**
```yaml
---
name: skill-command-name     # REQUIRED: becomes /skill-command-name
description: >               # REQUIRED
  What this skill does
---
```

❌ **Common mistakes**
- Opening subfolder: `code skills/` ← Won't work
- Missing name field in YAML ← Skill won't register  
- Skill not in `skills/` folder ← Won't be discovered

### Verify It's Working

```bash
# 1. Check you're in the right directory
pwd  # Should show: .../platengdevsupport

# 2. In Copilot Chat, list skills
@workspace list skills
# Should see: apply-security-baseline, scaffold-frontend, scaffold-web-api

# 3. Test context awareness
@workspace what does ADR-001 say about microservices?
# Should reference your actual ADR-001 content

# 4. If nothing works
@workspace refresh  # Re-index workspace
# Or: Reload VS Code window (Ctrl+Shift+P → Developer: Reload Window)
```

---

## 🎯 Quick Start

### Simple Commands You Can Use

Just type these commands in GitHub Copilot Chat:

```
@workspace /scaffold-web-api order-service
```

```
@workspace /scaffold-frontend customer-portal
```

```
@workspace /apply-security-baseline
```

That's it! Copilot will:
- Ask clarifying questions
- Generate production-ready code
- Apply security best practices
- Include complete CI/CD pipelines
- Add comprehensive documentation

## 📚 Available Commands

### 1. Scaffold Web API

Creates a production-ready Java Spring Boot REST API.

**Command**:
```
@workspace /scaffold-web-api <service-name>
```

**Examples**:
```
@workspace /scaffold-web-api order-service
```
```
scaffold a new API for managing products
```
```
create a REST API called customer-service
```

**What You Get**:
- ✅ Java 21 + Spring Boot 3.2+
- ✅ Azure AD OAuth2 authentication
- ✅ PostgreSQL with Flyway migrations (or MongoDB)
- ✅ OpenTelemetry observability
- ✅ Health checks (liveness/readiness)
- ✅ Global exception handling
- ✅ Input validation
- ✅ Security headers
- ✅ Dockerfile (multi-stage, non-root user)
- ✅ Kubernetes manifests
- ✅ CI/CD pipeline with security scanning

**Copilot Will Ask**:
- What resource does this API manage? (e.g., "Order", "Product")
- PostgreSQL or MongoDB?
- Which Azure subscription/resource group?

**Time Saved**: ~4-8 hours of setup and configuration

---

### 2. Scaffold Frontend

Creates a production-ready React/TypeScript Single Page Application.

**Command**:
```
@workspace /scaffold-frontend <app-name>
```

**Examples**:
```
@workspace /scaffold-frontend admin-portal
```
```
create a React app for customer management
```
```
scaffold a new SPA called dashboard
```

**What You Get**:
- ✅ React 18 + TypeScript 5
- ✅ Vite for fast builds
- ✅ Azure AD B2C authentication (MSAL)
- ✅ React Query for API state
- ✅ React Router
- ✅ Tailwind CSS
- ✅ Form validation (React Hook Form + Zod)
- ✅ OpenTelemetry browser tracing
- ✅ Error boundaries
- ✅ Secure API client (axios with auto-token-refresh)
- ✅ ESLint with security rules
- ✅ CI/CD for Azure Static Web Apps

**Copilot Will Ask**:
- What does this app manage? (e.g., "Orders", "Customers")
- Azure AD or Azure AD B2C?
- Styling preference (Tailwind or Material-UI)?

**Time Saved**: ~6-10 hours of setup and configuration

---

### 3. Apply Security Baseline

Audits and fixes security issues in existing applications.

**Command**:
```
@workspace /apply-security-baseline
```

**Examples**:
```
check security of my application
```
```
apply security best practices
```
```
audit this codebase for vulnerabilities
```

**What It Does**:

1. **Audits** your application:
   - Authentication/authorization
   - Hardcoded secrets
   - SQL injection vulnerabilities
   - XSS vulnerabilities
   - Missing security headers
   - Dependency vulnerabilities
   - Missing input validation
   - Insecure logging

2. **Prioritizes** issues:
   - P0 (Critical): Fix immediately
   - P1 (High): Fix this sprint
   - P2 (Medium): Fix next sprint
   - P3 (Low): Backlog

3. **Applies** fixes:
   - Moves secrets to Azure Key Vault
   - Adds Azure AD authentication
   - Fixes SQL injection (parameterized queries)
   - Prevents XSS (sanitization)
   - Adds security headers
   - Updates vulnerable dependencies
   - Adds input validation
   - Implements security logging

4. **Reports** what was changed

**Time Saved**: ~2-3 days of security review and fixes

---

### 4. Custom Requests

You can also ask Copilot naturally:

**Architecture Questions**:
```
What's our approved pattern for microservices communication?
```
```
How should I implement distributed transactions?
```
```
Show me the database-per-service pattern
```

**Security Questions**:
```
How do I store secrets securely?
```
```
What authentication should I use?
```
```
How do I prevent SQL injection?
```

**Implementation Help**:
```
Add circuit breaker to my service
```
```
Implement health checks
```
```
Add structured logging
```
```
Create a Kubernetes deployment for my app
```

Copilot will reference:
- Architecture Decision Records (ADRs)
- Security Baseline
- Golden Path templates
- Well-Architected Framework guidance

---

## 🎓 How It Works

### The Framework Layers

```
┌─────────────────────────────────────────┐
│  GitHub Copilot                         │
│  (Your AI Coding Assistant)             │
└─────────────┬───────────────────────────┘
              │
              ├─► Skills (Easy Commands)
              │   - /scaffold-web-api
              │   - /scaffold-frontend
              │   - /apply-security-baseline
              │
              ├─► Layer 2: Golden Paths (Templates)
              │   - Production-ready code examples
              │   - Working implementations
              │   - Complete CI/CD pipelines
              │
              └─► Layer 1: Context (Rules)
                  - Architecture Decision Records
                  - Security Requirements
                  - Compliance Standards
```

**Layer 1 - Authoritative Context** (The Rules):
- Architecture Decision Records (WHY we build this way)
- Security Baseline (MUST-HAVE security requirements)
- Compliance Regulations (Legal/regulatory requirements)
- Platform Assumptions (What Azure services we use)

**Layer 2 - Golden Paths** (The How-To):
- Web API: Complete Spring Boot implementation
- Frontend SPA: Complete React/TypeScript implementation
- Event-Driven: Kafka/EventHub patterns
- AI Agent: Microsoft Agent Framework patterns

**Skills** (The Commands):
- `/scaffold-web-api`: Generate from Web API golden path
- `/scaffold-frontend`: Generate from Frontend golden path
- `/apply-security-baseline`: Audit and fix security

---

## 💡 Usage Patterns

### Starting a New Microservice

```
# Step 1: Scaffold the backend API
@workspace /scaffold-web-api inventory-service

# Copilot asks questions, generates complete service

# Step 2: Scaffold the admin frontend
@workspace /scaffold-frontend inventory-admin

# Step 3: Review and customize
# All best practices already applied!

# Step 4: Deploy
git push origin main  # CI/CD automatically deploys
```

### Securing an Existing Application

```
# Step 1: Run security audit
@workspace /apply-security-baseline

# Step 2: Review findings
# Copilot shows all security issues

# Step 3: Apply fixes
# Copilot fixes critical and high priority issues

# Step 4: Verify
npm audit  # or mvn dependency-check:check
```

### Learning Best Practices

```
# Ask about patterns
Why do we use database-per-service?

# Ask about implementation
How should I implement circuit breakers?

# Get code examples
Show me how to add health checks to my API

# Copilot references ADRs and golden paths
```

---

## 🏗️ What Gets Generated

### When You Scaffold a Web API

```
my-service/
├── Complete source code
│   ├── Controllers with Azure AD auth
│   ├── Services with business logic
│   ├── Repositories with JPA
│   ├── DTOs with validation
│   └── Exception handling
├── Configuration
│   ├── Security config (Azure AD)
│   ├── Database config (connection pooling)
│   ├── Observability (OpenTelemetry)
│   └── Environment-specific settings
├── Database migrations
│   └── Flyway SQL scripts
├── Tests
│   ├── Unit tests
│   └── Integration tests (Testcontainers)
├── Infrastructure
│   ├── Dockerfile (multi-stage, secure)
│   ├── Kubernetes manifests
│   └── Helm charts (optional)
├── CI/CD
│   ├── GitHub Actions workflows
│   ├── Security scanning
│   └── Deployment automation
└── Documentation
    ├── README with setup instructions
    ├── API documentation
    └── Contributing guidelines
```

### When You Scaffold a Frontend

```
my-frontend/
├── Complete source code
│   ├── Authentication (MSAL)
│   ├── API client (axios with auth)
│   ├── Feature modules
│   ├── Components (accessible)
│   └── Form validation (Zod)
├── Configuration
│   ├── Vite config (optimized builds)
│   ├── TypeScript (strict mode)
│   ├── ESLint (security rules)
│   └── Tailwind CSS
├── Testing
│   ├── Unit tests (Vitest)
│   ├── Component tests (Testing Library)
│   └── E2E tests (Playwright)
├── Infrastructure
│   ├── Dockerfile
│   ├── nginx config (security headers)
│   └── Static Web Apps config
├── CI/CD
│   ├── GitHub Actions
│   ├── Linting and type checking
│   ├── Security scanning (npm audit)
│   └── Automatic deployment
└── Documentation
    └── README with setup instructions
```

---

## ✅ Quality Guarantees

Everything generated includes:

### Security ✅
- Azure AD authentication
- Secrets in Key Vault (never hardcoded)
- Input validation on all endpoints
- SQL injection prevention
- XSS prevention
- Security headers
- OWASP dependency scanning

### Reliability ✅
- Health checks (liveness/readiness)
- Circuit breakers
- Retry logic with exponential backoff
- Graceful degradation
- Error boundaries

### Observability ✅
- Structured JSON logging
- OpenTelemetry distributed tracing
- Metrics collection (Prometheus)
- Correlation IDs
- Performance monitoring

### Operational Excellence ✅
- Infrastructure as Code (Kubernetes)
- CI/CD pipelines
- Automated testing
- Code quality checks
- Security scanning
- Documentation

### Performance ✅
- Database connection pooling
- API caching strategies
- Code splitting (frontend)
- Lazy loading
- Bundle optimization

---

## 🚀 Next Steps After Scaffolding

### For Backend APIs

1. **Configure Azure Resources**:
   ```bash
   # Create Azure AD app registration
   az ad app create --display-name "my-service-api"
   
   # Create Key Vault
   az keyvault create \
     --name my-service-kv \
     --resource-group rg-my-service \
     --location eastus
   
   # Add secrets
   az keyvault secret set \
     --vault-name my-service-kv \
     --name db-password \
     --value "your-secure-password"
   ```

2. **Create Database**:
   ```bash
   # PostgreSQL
   az postgres flexible-server create \
     --name my-service-db \
     --resource-group rg-my-service \
     --location eastus
   ```

3. **Deploy**:
   ```bash
   # Push to trigger CI/CD
   git push origin main
   ```

### For Frontend Apps

1. **Configure Azure AD B2C**:
   ```bash
   # Create B2C tenant
   # Create user flows
   # Register application
   ```

2. **Update Environment Variables**:
   ```bash
   # .env.production
   VITE_AZURE_CLIENT_ID=your-client-id
   VITE_AZURE_TENANT_ID=your-tenant-id
   VITE_API_BASE_URL=https://api.yourorg.com
   ```

3. **Deploy**:
   ```bash
   # Push to trigger CI/CD
   git push origin main
   ```

---

## 📖 Reference Documentation

### Architecture Decision Records
- [ADR-001: Microservices Architecture](./../mcp-servers/layer1-context/architecture-patterns/adr/001-microservices-architecture.md)
- [ADR-002: API Gateway Pattern](./../mcp-servers/layer1-context/architecture-patterns/adr/002-api-gateway-pattern.md)
- [ADR-003: Database Per Service](./../mcp-servers/layer1-context/architecture-patterns/adr/003-database-per-service.md)

### Security
- [Security Baseline](./../mcp-servers/layer1-context/security-baseline/index.md)

### Golden Paths
- [Web API Golden Path](./../mcp-servers/layer2-goldenpaths/web-api/index.md)
- [Frontend SPA Golden Path](./../mcp-servers/layer2-goldenpaths/frontend-spa/index.md)

---

## 🤔 Common Questions

### Q: Will this work with my existing codebase?

**A**: Yes! The security baseline skill can audit and enhance existing code. For new features, Copilot will suggest patterns that match your scaffolded applications.

### Q: Can I customize the templates?

**A**: Absolutely! The golden paths are starting points. You can:
1. Use them as-is for standard services
2. Ask Copilot to modify specific aspects ("add Redis caching")
3. Customize the Layer 2 templates in your fork

### Q: What if I need something not in a golden path?

**A**: Just ask Copilot! For example:
- "Add Kafka integration to this service"
- "Implement GraphQL instead of REST"
- "Use MongoDB instead of PostgreSQL"

Copilot will apply the same security and architectural standards to your custom requirements.

### Q: How do I keep these templates updated?

**A**: 
1. Layer 1 (ADRs, Security): Review quarterly, update as policies change
2. Layer 2 (Golden Paths): Update when dependencies major-version
3. Skills: Update when golden paths change

### Q: Does this replace code review?

**A**: No! This establishes a high baseline. You still need:
- Code review for business logic
- Architecture review for new patterns
- Security review for sensitive features
- Performance testing under load

### Q: What about other languages/frameworks?

**A**: This reference implementation covers:
- Java/Spring Boot backend
- React/TypeScript frontend
- PostgreSQL/MongoDB

To add Python, Go, .NET, etc.:
1. Create new golden paths in Layer 2
2. Create new skills that reference them
3. Reuse Layer 1 (ADRs, Security) as-is

---

## 🎯 Success Metrics

Track your improvements:

### Before This Framework
- ⏰ 1-2 weeks to set up a new service
- 🐛 Security issues found in production
- 📚 Inconsistent code patterns across teams
- 🔥 Operational issues from missing observability
- 📉 Long onboarding time for new developers

### After This Framework
- ✅ 1-2 hours to scaffold a production-ready service
- ✅ Security baseline enforced from day one
- ✅ Consistent patterns across all services
- ✅ Observability built-in
- ✅ New developers productive immediately

---

## 🆘 Support

- **Questions**: #platform-engineering (Slack)
- **Issues**: File GitHub issue in this repo
- **Improvements**: Submit PR with updated golden paths
- **Security**: security@organization.com

---

## 🎓 Training Resources

- [Well-Architected Framework (Microsoft)](https://learn.microsoft.com/azure/architecture/framework/)
- [Spring Boot Best Practices](https://spring.io/guides)
- [React Best Practices](https://react.dev/learn)
- [Azure Security](https://learn.microsoft.com/azure/security/)

---

## 🔄 Feedback Loop

Help us improve!

**After using a skill**:
1. What worked well?
2. What was confusing?
3. What's missing?
4. What should change?

File feedback as GitHub issues or in #platform-engineering.

---

**Remember**: These tools make it **easy to do the right thing**. Security, reliability, and observability are built-in from the start. Focus on your business logic—we've got the infrastructure patterns covered! 🚀
