# Platform Engineering Developer Support

A comprehensive framework for guiding GitHub Copilot to follow organizational best practices, architectural patterns, and compliance requirements.

## 🚀 Quick Start for Developers

### Step 1: Set Up Your Workspace (5 minutes)

**For this reference implementation**, you need to have this repository as your VS Code workspace:

```bash
# Clone this repository
git clone https://github.com/your-org/platengdevsupport.git
cd platengdevsupport

# Open in VS Code
code .
```

**Important**: Open the **root `platengdevsupport` folder** as your workspace. Skills and MCPs are discovered based on the workspace root.

### Step 2: Wait for Copilot Indexing (1-2 minutes)

Once you open the workspace in VS Code:

1. GitHub Copilot will automatically start indexing the workspace
2. Look for the notification: **"Indexing workspace..."** (bottom right)
3. Wait for: **"Workspace indexed"** or the notification disappears
4. This usually takes 1-2 minutes for this repository

**What's happening?** Copilot is:
- 🔍 Discovering the `skills/` folder and loading skill definitions
- 📚 Indexing all files in `mcp-servers/layer1-context/` (ADRs, security baseline)
- 📋 Indexing all files in `mcp-servers/layer2-goldenpaths/` (templates)
- 🧠 Building context about your organizational standards

**Visual: How Copilot Discovers Your Standards**
```
Your VS Code Workspace (platengdevsupport/)
│
├─ skills/                           ← Copilot discovers these automatically
│  ├─ scaffold-web-api/SKILL.md      → Registers as /scaffold-web-api command
│  ├─ scaffold-frontend/SKILL.md     → Registers as /scaffold-frontend command
│  └─ apply-security-baseline/SKILL.md → Registers as /apply-security-baseline command
│
├─ mcp-servers/layer1-context/       ← Copilot indexes for context
│  ├─ architecture-patterns/adr/     → Referenced when you ask architecture questions
│  └─ security-baseline/             → Applied when generating code
│
└─ mcp-servers/layer2-goldenpaths/   ← Copilot uses as templates
   ├─ web-api/                       → Used by /scaffold-web-api skill
   └─ frontend-spa/                  → Used by /scaffold-frontend skill
```

**Tip**: You can view indexing progress:
- View → Output → Select "GitHub Copilot" from dropdown

### Step 3: Verify Skills Are Available (30 seconds)

Open **GitHub Copilot Chat** (Ctrl+Alt+I or Cmd+Alt+I) and type:

```
@workspace list skills
```

You should see:
- ✅ `apply-security-baseline`
- ✅ `scaffold-frontend`
- ✅ `scaffold-web-api`

If you don't see them, try:
1. Refresh the workspace: `@workspace refresh`
2. Reload VS Code: Ctrl+Shift+P → "Developer: Reload Window"

### Step 4: Use the Skills! (2 minutes)

Now you can use simple commands in Copilot Chat:

```bash
# Scaffold a production-ready API in 2 minutes
@workspace /scaffold-web-api my-service

# Scaffold a production-ready frontend in 2 minutes
@workspace /scaffold-frontend my-app

# Audit and fix security in existing code
@workspace /apply-security-baseline
```

**That's it!** Copilot now knows about your organizational standards and will generate code accordingly.

---

### 🏢 For Production/Enterprise Use

**This mono-repo is for demonstration only.** In your organization:

**Option 1: Multi-Repo Setup (Recommended)**
```bash
# Developers would have multiple workspaces
~/work/
├── my-service/                    # Their actual project
├── architecture-standards/        # Clone of Layer 1 standards (read-only)
└── platform-templates/            # Clone of Layer 2 templates (read-only)

# In VS Code: Add all folders to a multi-root workspace
# File → Add Folder to Workspace (for each folder)
# Copilot indexes across all workspace folders
```

**Option 2: Git Submodules**
```bash
# In your service repository
cd my-service
git submodule add https://github.com/your-org/architecture-standards .copilot/standards
git submodule add https://github.com/your-org/platform-templates .copilot/templates

# Copilot will discover the skills in submodules
```

**Option 3: Published Extension (Enterprise)**
Package skills as a VS Code extension that automatically adds context to all workspaces.

See [Real-World Deployment](#-real-world-deployment) section below for details.

---

**New here?** Continue with the **[Getting Started Guide](GETTING-STARTED.md)** for a detailed walkthrough.

## 📋 Important: Reference Implementation

**This repository is a reference implementation.**

In a real organization, these components would typically live in **separate repositories** with different ownership and lifecycles:
- **Layer 1 (ADRs, Security Baseline)** → Architecture team repository, quarterly reviews
- **Layer 2 (Golden Paths)** → Platform engineering repository, monthly updates
- **Skills** → Developer tooling repository, continuous iteration
- **Generated applications** → Individual service repositories owned by product teams

They are **colocated here to demonstrate how the pieces work together**. This single-repo structure is for learning and proof-of-concept purposes only.

## 🎯 Philosophy

**Copilot is steerable, not enforceable.** This framework uses:
- **Context injection** (MCPs, repos, docs) → influence suggestions
- **Golden paths & SDKs** → make the right thing easiest
- **Policy & CI enforcement** → catch deviations
- **Feedback loops** → continuous improvement

## 📁 Structure

```
platengdevsupport/                    # ⚠️ Single repo for demo only
├── mcp-servers/                      # → In production: "architecture-standards" repo
│   ├── layer1-context/               #    Owned by: Architecture team
│   │   ├── architecture-patterns/    #    Update cycle: Quarterly
│   │   ├── security-baseline/
│   │   ├── compliance-regulations/
│   │   └── platform-assumptions/
│   └── layer2-goldenpaths/           # → In production: "platform-templates" repo
│       ├── web-api/                  #    Owned by: Platform engineering
│       ├── frontend-spa/             #    Update cycle: Monthly
│       ├── event-driven/
│       └── ai-agent/
├── skills/                           # → In production: "developer-tools" repo
│   ├── scaffold-web-api/             #    Owned by: Developer experience team
│   ├── scaffold-frontend/            #    Update cycle: Continuous
│   ├── apply-security-baseline/
│   └── generate-compliance-report/
├── templates/                        # Reference implementations (demo)
│   ├── react-typescript-frontend/
│   ├── java-maven-backend/
│   ├── mongodb-repository/
│   └── postgresql-repository/
├── sdks/                             # → In production: Individual npm/Maven packages
│   ├── @org-http-sdk/                #    Owned by: Platform engineering
│   ├── @org-telemetry/               #    Published to: Internal registries
│   └── @org-security/
└── docs/                             # Documentation
    ├── architecture-decision-records/
    ├── well-architected/
    └── usage-guide.md
```

**Note**: The arrows (→) indicate where these would live in a real organizational structure with proper separation of concerns, ownership, and release cycles.

## 🚀 Quick Start for Developers

### Simple Chat Commands

Ask GitHub Copilot in chat:

```
@workspace /scaffold-web-api my-service
```

```
@workspace /scaffold-frontend my-app
```

```
@workspace /apply-security-baseline
```

```
@workspace /generate-compliance-report
```

### What Gets Applied Automatically

When you use these commands, Copilot will:
- ✅ Use approved architectural patterns
- ✅ Include security baselines by default
- ✅ Apply observability standards
- ✅ Follow naming conventions
- ✅ Include proper error handling
- ✅ Add required compliance headers

## 📚 How It Works - The Three Layers

```
┌─────────────────────────────────────────────────────────────┐
│  DEVELOPERS (You!)                                          │
│  Use simple chat commands                                   │
└────────────┬────────────────────────────────────────────────┘
             │
             │ @workspace /scaffold-web-api
             │ @workspace /scaffold-frontend
             │ @workspace /apply-security-baseline
             │
             v
┌─────────────────────────────────────────────────────────────┐
│  SKILLS (Chat Commands)                                     │
│  - /scaffold-web-api: Generate Spring Boot API             │
│  - /scaffold-frontend: Generate React SPA                  │
│  - /apply-security-baseline: Audit & fix security          │
└────────────┬────────────────────────────────────────────────┘
             │
             │ References Golden Paths for templates
             │
             v
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: Golden Paths (The "How-To")                      │
│  - web-api/: Complete Spring Boot implementation           │
│  - frontend-spa/: Complete React/TypeScript implementation │
│  - event-driven/: Kafka/EventHub patterns                  │
│  Production-ready code, tests, CI/CD, documentation        │
└────────────┬────────────────────────────────────────────────┘
             │
             │ Follows rules from Layer 1
             │
             v
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: Authoritative Context (The "Rules")              │
│  - architecture-patterns/: ADRs (microservices, API        │
│    gateway, database-per-service)                          │
│  - security-baseline/: Authentication, secrets, validation,│
│    encryption, headers                                     │
│  - compliance-regulations/: Logging, auditing, data        │
│    residency                                               │
│  - platform-assumptions/: Azure services, capabilities     │
└─────────────────────────────────────────────────────────────┘
```

### Layer 1: Authoritative Context (Read-Only)

These MCPs provide **canonical truth** about:
- Architecture Decision Records (ADRs)
- Security requirements
- Compliance mandates
- Performance constraints
- Platform capabilities

**Purpose**: Make Copilot aware of the rules
**Format**: Declarative, citation-friendly
**Examples**: 
- [ADR-001: Microservices Architecture](mcp-servers/layer1-context/architecture-patterns/adr/001-microservices-architecture.md)
- [Security Baseline](mcp-servers/layer1-context/security-baseline/index.md)

### Layer 2: Golden Paths (Prescriptive)

These MCPs provide **executable patterns**:
- Reference implementations
- Working code examples
- Dependency choices
- Configuration templates
- Non-negotiables

**Purpose**: Make Copilot build it correctly
**Format**: Code, templates, step-by-step
**Examples**:
- [Web API Golden Path](mcp-servers/layer2-goldenpaths/web-api/index.md) - Spring Boot REST API
- [Frontend SPA Golden Path](mcp-servers/layer2-goldenpaths/frontend-spa/index.md) - React/TypeScript

### Skills: Easy-to-Use Commands

Developer-friendly commands that generate production-ready code:
- [`/scaffold-web-api`](skills/scaffold-web-api/SKILL.md) - Generate Java Spring Boot API
- [`/scaffold-frontend`](skills/scaffold-frontend/SKILL.md) - Generate React TypeScript SPA
- [`/apply-security-baseline`](skills/apply-security-baseline/SKILL.md) - Audit and fix security issues

## 🏗️ Well-Architected Framework Alignment

Based on Microsoft's Well-Architected Framework:

| Pillar | Implementation |
|--------|----------------|
| **Reliability** | Circuit breakers, retry policies, health checks, graceful degradation |
| **Security** | Azure AD, RBAC, Key Vault, input validation, security headers, Zero Trust |
| **Cost Optimization** | Right-sizing, auto-scaling, resource optimization, monitoring |
| **Operational Excellence** | Observability, IaC, CI/CD, testing, structured logging |
| **Performance Efficiency** | Caching, async patterns, database optimization, code splitting |

## 📦 What You Get

### When You Scaffold a Web API

```
my-service/
├── ✅ Complete Spring Boot application (Java 21)
├── ✅ Azure AD OAuth2 authentication
├── ✅ PostgreSQL/MongoDB with migrations
├── ✅ OpenTelemetry observability (tracing + metrics)
├── ✅ Health checks (liveness/readiness)
├── ✅ Global exception handling
├── ✅ Input validation on all endpoints
├── ✅ Circuit breakers (Resilience4j)
├── ✅ Security headers configured
├── ✅ Structured JSON logging
├── ✅ Docker multi-stage build (non-root user)
├── ✅ Kubernetes manifests (deployment, service, ingress, HPA)
├── ✅ CI/CD pipeline (GitHub Actions)
├── ✅ OWASP dependency scanning
├── ✅ Unit and integration tests
└── ✅ Complete documentation

Time saved: 4-8 hours of setup
```

### When You Scaffold a Frontend

```
my-app/
├── ✅ Complete React 18 + TypeScript application
├── ✅ Azure AD B2C authentication (MSAL)
├── ✅ Vite build tool (fast development)
├── ✅ React Query (server state management)
├── ✅ React Router (navigation)
├── ✅ Tailwind CSS (styling)
├── ✅ Form validation (React Hook Form + Zod)
├── ✅ Secure API client (axios with auto-token-refresh)
├── ✅ Error boundaries
├── ✅ Loading states
├── ✅ Security headers (nginx config)
├── ✅ ESLint with security rules
├── ✅ OpenTelemetry browser tracing
├── ✅ CI/CD for Azure Static Web Apps
├── ✅ Unit tests (Vitest) + E2E tests (Playwright)
└── ✅ Complete documentation

Time saved: 6-10 hours of setup
```

### When You Apply Security Baseline

```
✅ Audits your application against security requirements
✅ Identifies vulnerabilities (SQL injection, XSS, secrets, etc.)
✅ Prioritizes by severity (P0/P1/P2/P3)
✅ Fixes critical issues automatically
✅ Moves secrets to Azure Key Vault
✅ Adds Azure AD authentication
✅ Implements input validation
✅ Adds security headers
✅ Updates vulnerable dependencies
✅ Generates security audit report

Time saved: 2-3 days of security review
```

## 📖 Documentation

### For Developers

- **[Getting Started Guide](GETTING-STARTED.md)** - 15-minute setup guide
- **[Usage Guide](docs/usage-guide.md)** - How developers interact with this system
- **[Quick Reference](docs/quick-reference.md)** - Command cheat sheet
- **[Example: Order Service](templates/example-order-service/README.md)** - Complete working example

### For Architects

- **[Architecture Decision Records](mcp-servers/layer1-context/architecture-patterns/adr/)** - Why we made these choices
  - [ADR-001: Microservices Architecture](mcp-servers/layer1-context/architecture-patterns/adr/001-microservices-architecture.md)
  - [ADR-002: API Gateway Pattern](mcp-servers/layer1-context/architecture-patterns/adr/002-api-gateway-pattern.md)
  - [ADR-003: Database Per Service](mcp-servers/layer1-context/architecture-patterns/adr/003-database-per-service.md)
- **[Security Baseline](mcp-servers/layer1-context/security-baseline/index.md)** - Security requirements
- **[Well-Architected Alignment](docs/well-architected/)** - Detailed pillar implementation

### For Contributors

- **[Contributing Guide](CONTRIBUTING.md)** - How to update patterns and MCPs
- **[Golden Paths](mcp-servers/layer2-goldenpaths/)** - Implementation templates
  - [Web API](mcp-servers/layer2-goldenpaths/web-api/index.md)
  - [Frontend SPA](mcp-servers/layer2-goldenpaths/frontend-spa/index.md)
- **[Skills](skills/)** - Developer-facing commands
  - [scaffold-web-api](skills/scaffold-web-api/SKILL.md)
  - [scaffold-frontend](skills/scaffold-frontend/SKILL.md)
  - [apply-security-baseline](skills/apply-security-baseline/SKILL.md)

## 🔧 Customization

To adapt this for your organization:

1. **Update Layer 1 MCPs** with your actual policies
2. **Customize Layer 2 templates** with your SDKs
3. **Create organization-specific skills**
4. **Update reference implementations** with your patterns
5. **Configure CI/CD** to validate compliance

## 🏢 Real-World Deployment

For production use, **split this reference implementation** into separate repositories:

### Recommended Repository Structure

```
your-org/
├── architecture-standards/           # Layer 1: Authoritative Context
│   ├── adrs/                         # Architecture Decision Records
│   ├── security-baseline/            # Security requirements
│   ├── compliance/                   # Compliance standards
│   └── README.md
│   # Owned by: Architecture Review Board
│   # Access: Read-only for most developers
│   # Update frequency: Quarterly
│   # Version: Semantic versioning (v1.x.x)
│
├── platform-templates/               # Layer 2: Golden Paths
│   ├── java-spring-boot-template/
│   ├── react-typescript-template/
│   ├── python-fastapi-template/
│   └── README.md
│   # Owned by: Platform Engineering team
│   # Access: Read for developers, write for platform team
│   # Update frequency: Monthly
│   # Version: Semantic versioning (v2.x.x)
│
├── developer-tools/                  # Skills and CLI tools
│   ├── copilot-skills/
│   │   ├── scaffold-web-api/
│   │   ├── scaffold-frontend/
│   │   └── apply-security/
│   ├── cli/                          # Optional: CLI wrapper
│   └── README.md
│   # Owned by: Developer Experience team
│   # Access: Open for contributions
│   # Update frequency: Continuous
│   # Version: Semantic versioning (v3.x.x)
│
└── [internal-sdks-org]/              # In-house SDK packages
    ├── http-client/                  # Published to npm/@org/http-client
    ├── telemetry/                    # Published to npm/@org/telemetry
    ├── security/                     # Published to npm/@org/security
    └── auth/                         # Published to Maven/your-org
    # Owned by: Platform Engineering + Service teams
    # Access: Published to internal registries
    # Version: Independent semantic versioning
```

### Integration Points

Each repository references the others:

```yaml
# platform-templates/java-template/metadata.yml
references:
  architecture_standards: v1.2.0  # ADRs version
  internal_sdks:
    - @org/http-client@^2.0.0
    - @org/telemetry@^1.5.0

# developer-tools/skills/scaffold-web-api/config.yml
templates:
  source: platform-templates
  version: v2.3.0
standards:
  source: architecture-standards
  version: v1.2.0
```

### Benefits of Separation

✅ **Clear ownership**: Each team owns their domain  
✅ **Independent versioning**: Updates don't affect everything  
✅ **Proper access control**: Restrict write access appropriately  
✅ **Release management**: Control when changes propagate  
✅ **Scalability**: Teams work independently  
✅ **Auditability**: Track changes per domain

### Migration Path

1. **Phase 1**: Use this reference repo for POC (2-4 weeks)
2. **Phase 2**: Split into separate repos (1-2 weeks)
3. **Phase 3**: Set up proper CI/CD and versioning (1-2 weeks)
4. **Phase 4**: Onboard teams gradually (4-8 weeks)

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed migration guidance.

## ❓ Frequently Asked Questions

### How does Copilot "see" my skills and standards?

When you open this repository as your VS Code workspace, GitHub Copilot automatically:
1. **Scans the `skills/` folder** for any `SKILL.md` files and registers them as commands
2. **Indexes all files** in the workspace to build semantic understanding
3. **Makes skills available** as `/command-name` in Copilot Chat
4. **References indexed content** when answering questions or generating code

**Key point**: The workspace root must contain the `skills/` folder for discovery.

### Do I need to manually configure anything?

**For this demo: No.** Just open the folder in VS Code and wait for indexing to complete.

**For production**: You'll set up multi-root workspaces, submodules, or a VS Code extension so developers automatically get access. See the "For Production/Enterprise Use" section above.

### Can developers customize the skills?

**In this mono-repo**: Yes, edit the `SKILL.md` files directly.

**In production**: Skills would be in a separate `developer-tools` repository. Developers can:
- Fork and propose changes via PR
- Add company-specific skills
- Override with local workspace skills

### What if I don't want to use all skills?

Copilot loads all skills it finds in the workspace. To disable a skill:
- **Temporarily**: Just don't invoke it
- **Permanently**: Remove or rename the skill folder (e.g., `skills/_disabled_scaffold-web-api/`)

### How often do I need to update this?

**For the demo**: It's static - no updates needed for learning.

**In production**:
- **Layer 1** (ADRs, security): Quarterly or when policies change
- **Layer 2** (templates): Monthly or when dependencies update
- **Skills**: Continuous improvement

Each would have its own repository and versioning in production.

### Will this slow down Copilot?

**Initial indexing**: Takes 1-2 minutes for this repository size.

**After that**: No noticeable impact. Copilot only re-indexes when files change.

**At scale**: With large mono-repos (>10,000 files), consider splitting into focused workspaces.

### Can I use this with other non-Microsoft tech stacks?

**Absolutely!** The framework is technology-agnostic. This implementation includes:
- Java/Spring Boot
- React/TypeScript
- PostgreSQL/MongoDB

To add Python, .NET, Go, etc.:
1. Create new golden paths in Layer 2 (e.g., `python-fastapi/`)
2. Create new skills (e.g., `scaffold-python-api/`)
3. Reuse Layer 1 ADRs and security baseline (mostly tech-agnostic)

### How do I test changes to skills before deploying?

1. Edit the `SKILL.md` file locally
2. Save the file
3. In Copilot Chat: `@workspace refresh`
4. Test the skill with your changes
5. Iterate until satisfied
6. Commit and push (or create PR in production)

### Can I integrate this with Azure DevOps instead of GitHub?

**Yes!** The skills use Copilot's natural language understanding, not git provider-specific features. You'd just:
- Host repositories in Azure DevOps
- Adjust CI/CD pipelines to Azure Pipelines
- Keep using VS Code + Copilot locally

The skills, ADRs, and templates work the same way.

## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on updating architectural patterns and MCPs.

---

## 📌 Final Reminder: Production Deployment

**This single-repository structure is for learning and demonstration only.**

When adopting this framework in your organization:

1. ✅ **Split into separate repositories** with proper ownership
2. ✅ **Implement version control** for each component
3. ✅ **Set up proper access controls** (not everyone needs write access to ADRs)
4. ✅ **Establish release processes** for Layer 1, Layer 2, and Skills independently
5. ✅ **Publish SDKs** to internal package registries (npm, Maven, NuGet)
6. ✅ **Use semantic versioning** for each component
7. ✅ **Create integration points** so components reference specific versions

See the **"Real-World Deployment"** section above for detailed guidance on proper repository structure.

---

**Built with ❤️ for Platform Engineering teams everywhere**
