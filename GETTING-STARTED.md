# Getting Started with Platform Engineering Developer Support

Welcome! This guide will get you up and running in 15 minutes.

## ⚠️ Before You Begin: Reference Implementation

**This repository is a reference implementation.**

In a real organization, these components would be in **separate repositories**:
- **Layer 1** (ADRs, Security) → Architecture team repo
- **Layer 2** (Golden Paths) → Platform engineering repo  
- **Skills** → Developer tools repo
- **SDKs** → Published packages

They're **colocated here to show how they work together**. For production deployment, see the "Real-World Deployment" section in the [main README](README.md).

## 🎯 What Is This?

This framework helps you build applications that **automatically follow** your organization's:
- Architecture patterns (microservices, API gateway, database-per-service)
- Security requirements (Azure AD, Key Vault, input validation)
- Compliance standards (logging, auditing, encryption)
- Operational practices (observability, health checks, CI/CD)

**How?** By making GitHub Copilot **context-aware** of your standards.

## ⚡ Quick Demo (5 minutes)

### Step 1: Create a REST API

In GitHub Copilot Chat, type:
```
@workspace /scaffold-web-api demo-service
```

Copilot asks:
- **Resource type?** → Type: `Product`
- **Database?** → Select: `PostgreSQL`
- **Azure subscription?** → Type: `my-subscription`

**Result**: A production-ready Spring Boot API with:
- ✅ Azure AD authentication
- ✅ PostgreSQL database
- ✅ Health checks
- ✅ Security headers
- ✅ OpenTelemetry tracing
- ✅ Docker + Kubernetes
- ✅ CI/CD pipeline

Total time: **2 minutes**

### Step 2: Create a Frontend

In GitHub Copilot Chat, type:
```
@workspace /scaffold-frontend demo-app
```

Copilot asks:
- **Resource type?** → Type: `Product`
- **Auth type?** → Select: `Azure AD B2C`

**Result**: A production-ready React SPA with:
- ✅ Azure AD B2C authentication
- ✅ TypeScript + Vite
- ✅ React Query
- ✅ Form validation
- ✅ Security best practices
- ✅ CI/CD pipeline

Total time: **2 minutes**

### Step 3: Review the Code

Open the generated files. Notice:
- No hardcoded secrets (all in Key Vault references)
- Complete error handling
- Input validation on all forms/endpoints
- Security headers configured
- Tests included
- Documentation included

**Everything you need is there.**

## 📦 Installation & Setup

### Prerequisites

1. **GitHub Copilot** subscription with Chat enabled
2. **VS Code** (latest version recommended) or **GitHub Codespaces**
3. **Git** installed

### Setup (One-Time, 5 minutes)

#### Step 1: Clone the Repository

```bash
# Clone this reference implementation
git clone https://github.com/your-org/platengdevsupport.git
cd platengdevsupport
```

#### Step 2: Open as Workspace in VS Code

```bash
# Open the root folder in VS Code
code .
```

**Critical**: You must open the **root `platengdevsupport` folder** as your workspace, not a subfolder. Skills are discovered from the workspace root.

#### Step 3: Wait for GitHub Copilot to Index (1-2 minutes)

Once VS Code opens:

1. **Watch for the notification** (bottom right): 
   - "Indexing workspace..." 
   - This means Copilot is scanning your workspace

2. **What Copilot is doing**:
   - ✅ Discovering skills in the `skills/` folder
   - ✅ Indexing Layer 1 context (ADRs, security baseline)
   - ✅ Indexing Layer 2 golden paths (templates)
   - ✅ Building a semantic understanding of your standards

3. **Wait for completion**:
   - The notification disappears when done (usually 1-2 minutes)
   - Larger workspaces may take longer

**Tip**: You can see indexing progress in the Output panel:
- View → Output → Select "GitHub Copilot" from dropdown

#### Step 4: Verify Skills Are Loaded

Open **GitHub Copilot Chat**:
- Windows/Linux: `Ctrl + Alt + I`
- Mac: `Cmd + Option + I`

Type this command:
```
@workspace list skills
```

**Expected result** - You should see:
```
Available skills:
• apply-security-baseline - Audit and apply organizational security baseline
• scaffold-frontend - Scaffold a production-ready React/TypeScript SPA
• scaffold-web-api - Scaffold a production-ready Java Spring Boot REST API
```

#### Step 5: Test Context Awareness

In Copilot Chat, ask:
```
@workspace show me the microservices architecture pattern
```

**If it works**: Copilot will reference ADR-001 and show you the microservices pattern with DO/DON'T guidance.

**If it doesn't work**: Try these troubleshooting steps:

```bash
# Option 1: Refresh the workspace
@workspace refresh

# Option 2: Reload VS Code window
# Press F1 or Ctrl+Shift+P
# Type: "Developer: Reload Window"
# Press Enter

# Option 3: Check VS Code output
# View → Output → Select "GitHub Copilot"
# Look for any errors
```

#### Step 6: You're Ready! 🎉

Now you can use the skills:
```
@workspace /scaffold-web-api my-service
@workspace /scaffold-frontend my-app
@workspace /apply-security-baseline
```

---

### 🏢 For Your Organization (Production Setup)

**This mono-repo works for demo/learning.** For your organization, you have **three options**:

#### Option A: Multi-Root Workspace (Easiest)

Developers add multiple folders to their VS Code workspace:

```bash
# Directory structure
~/work/
├── my-actual-service/           # Their project
├── architecture-standards/      # Git clone of Layer 1
└── platform-templates/          # Git clone of Layer 2

# In VS Code:
# 1. Open my-actual-service
# 2. File → Add Folder to Workspace... → Select architecture-standards
# 3. File → Add Folder to Workspace... → Select platform-templates
# 4. File → Save Workspace As... → "my-company-workspace.code-workspace"
```

**Benefit**: Copilot indexes all folders. Developers get standards + their code.

#### Option B: Git Submodules (Automatic)

Add standards as submodules to service repos:

```bash
cd my-service

# Add as submodules (hidden from main codebase)
git submodule add https://github.com/your-org/architecture-standards .copilot/standards
git submodule add https://github.com/your-org/platform-templates .copilot/templates

# Update .gitignore to hide from commits
echo ".copilot/" >> .gitignore

# Clone with submodules
git clone --recursive https://github.com/your-org/my-service.git
```

**Benefit**: Automatic - developers get standards when they clone. Version-controlled.

#### Option C: VS Code Extension (Enterprise)

Package as a VS Code extension:

```json
// In your-company-copilot-extension/package.json
{
  "name": "your-company-copilot-standards",
  "contributes": {
    "copilot": {
      "contexts": [
        {
          "name": "company-standards",
          "path": "./standards"
        }
      ]
    }
  }
}
```

Publish to your internal extension marketplace. All developers install once.

**Benefit**: Enterprise-wide deployment. Automatic updates.

---

**Choose based on your organization size**:
- **< 50 developers**: Multi-root workspace (Option A)
- **50-200 developers**: Git submodules (Option B)  
- **200+ developers**: VS Code extension (Option C)

## 🎓 Your First Project (15 minutes)

Let's build a complete "Book Catalog" application.

### 1. Create Backend API (5 min)

```
@workspace /scaffold-web-api book-service
```

**Copilot asks**: Resource type?
**You answer**: `Book`

**Copilot asks**: Database?
**You answer**: `MongoDB` (let's try MongoDB this time)

**Copilot asks**: Azure subscription?
**You answer**: `dev-subscription`

**Copilot generates** ~30 files with complete implementation.

### 2. Verify Backend (2 min)

```bash
cd book-service

# Check structure
ls -la

# Build (Copilot added all dependencies)
mvn clean install

# Run locally
mvn spring-boot:run

# In another terminal, test
curl http://localhost:8080/actuator/health
# Should return: {"status":"UP"}
```

### 3. Create Frontend (5 min)

```
@workspace /scaffold-frontend book-catalog
```

**Copilot asks**: Resource type?
**You answer**: `Book`

**Copilot asks**: Auth?
**You answer**: `Azure AD B2C`

**Copilot generates** ~40 files with complete React app.

### 4. Verify Frontend (2 min)

```bash
cd book-catalog

# Install
npm install

# Run
npm run dev

# Open http://localhost:5173
# You'll see login page (Azure AD B2C)
```

### 5. Customize (1 min)

Let's add a "published year" field:

```
@workspace add a "publishedYear" field to Book entity and forms
```

Copilot updates:
- Database migration
- Java entity
- DTOs
- TypeScript types
- Form validation
- UI components

**All done!**

## 🔐 Security Check (5 minutes)

Let's verify security compliance:

```
@workspace /apply-security-baseline
```

Copilot:
1. Scans your code
2. Checks for vulnerabilities
3. Reports findings
4. Offers to fix issues

**Result**: Security audit report showing all checks passed ✅

## 📚 Learning the Patterns

### Learn by Example

```
@workspace show me an example of circuit breaker pattern
```

Copilot shows example from Golden Path with explanation.

### Learn by Doing

```
@workspace add Redis caching to my service
```

Copilot implements it following organizational patterns.

### Learn by Asking

```
@workspace why do we use API Gateway?
```

Copilot references ADR-002 with detailed explanation.

## 🎯 Common Use Cases

### Use Case 1: New Feature Development

**Scenario**: Add "Order History" feature

```
# 1. Add new controller
@workspace create OrderHistoryController following our REST API standards

# 2. Add database table
@workspace create Flyway migration for order_history table

# 3. Add frontend page
@workspace create OrderHistory component with list and detail views
```

### Use Case 2: Refactoring for Security

**Scenario**: Legacy code has hardcoded passwords

```
@workspace /apply-security-baseline

# Copilot finds issues:
# - Hardcoded database password
# - Missing input validation
# - No security headers

@workspace fix all P0 and P1 security issues

# Copilot:
# - Moves secrets to Key Vault
# - Adds validation
# - Adds security headers
```

### Use Case 3: Adding Observability

**Scenario**: Need better monitoring

```
@workspace add OpenTelemetry tracing to this service

# Copilot:
# - Adds dependencies
# - Configures OTLP exporter
# - Adds custom spans
# - Updates documentation
```

## 💡 Pro Tips

### Tip 1: Be Specific

❌ Bad: "add security"
✅ Good: "add Azure AD authentication with RBAC to this API"

### Tip 2: Reference Patterns

❌ Bad: "how do I cache?"
✅ Good: "show me the approved caching pattern from our golden path"

### Tip 3: iterate

```
# Start simple
@workspace /scaffold-web-api my-service

# Add features incrementally
@workspace add Kafka event publishing
@workspace add Redis caching
@workspace add rate limiting
```

### Tip 4: Learn Continuously

```
# Ask "why"
@workspace why do we use eventual consistency?

# Ask "how"
@workspace how do I implement saga pattern?

# Ask "when"
@workspace when should I use MongoDB vs PostgreSQL?
```

## 🚨 Troubleshooting

### Issue: Skills not showing in Copilot

**Symptoms**: 
- `@workspace /scaffold-web-api` says "command not found"
- `@workspace list skills` shows empty or doesn't list your skills

**Solutions**:

1. **Verify workspace root is correct**:
   ```bash
   # You should be in the platengdevsupport folder
   pwd
   # Should show: .../platengdevsupport
   
   # Check skills folder exists
   ls skills/
   # Should show: scaffold-web-api/, scaffold-frontend/, apply-security-baseline/
   ```

2. **Refresh workspace indexing**:
   ```
   # In Copilot Chat
   @workspace refresh
   ```
   Wait 1-2 minutes for reindexing.

3. **Reload VS Code**:
   - Press `F1` or `Ctrl+Shift+P` (Cmd+Shift+P on Mac)
   - Type: `Developer: Reload Window`
   - Press Enter
   - Wait for Copilot to reindex

4. **Check Copilot indexing status**:
   - View → Output
   - Select "GitHub Copilot" from dropdown
   - Look for "Indexed workspace" messages

5. **Verify skill files have correct format**:
   ```bash
   # Each SKILL.md should have YAML frontmatter with name field
   head -n 5 skills/scaffold-web-api/SKILL.md
   
   # Should show:
   # ---
   # name: scaffold-web-api
   # applyTo:
   # ...
   ```

### Issue: Copilot doesn't reference ADRs or security baseline

**Symptoms**: 
- Copilot gives generic answers instead of referencing your standards
- Doesn't mention ADR-001, Security Baseline, etc.

**Solutions**:

1. **Use @workspace prefix**:
   ```
   # ❌ Wrong
   show me microservices pattern
   
   # ✅ Correct
   @workspace show me microservices pattern
   ```

2. **Be explicit about context**:
   ```
   @workspace according to our ADRs, what's the approved database pattern?
   ```

3. **Check files are indexed**:
   ```
   # In Copilot Chat
   @workspace search for "ADR-001"
   ```
   If it finds ADR-001, indexing is working.

### Issue: Generated code has errors

**Solution**: Check prerequisites
```bash
# Java version
java -version  # Should be Java 21

# Maven version
mvn -version   # Should be Maven 3.9+

# Node version
node -version  # Should be Node 20+
```

### Issue: Authentication not working

**Solution**: Configure Azure AD
```bash
# Follow setup guide
@workspace show me Azure AD configuration guide
```

### Issue: Workspace opened incorrectly

**Symptoms**:
- Copilot can only see one subfolder
- Skills aren't discovered

**Solution**: Open the correct folder
```bash
# ❌ Wrong - opening a subfolder
code skills/

# ❌ Wrong - opening a nested project
code templates/example-order-service/

# ✅ Correct - open the root
code platengdevsupport/
```

Or in VS Code:
- File → Open Folder...
- Select the **platengdevsupport** root folder
- Not any subfolder

### Issue: Multi-root workspace not working

**Solution**: Properly configure workspace file
```bash
# Create a workspace file
# File → Save Workspace As... → my-workspace.code-workspace

# The .code-workspace file should include all folders:
{
  "folders": [
    { "path": "my-service" },
    { "path": "../architecture-standards" },
    { "path": "../platform-templates" }
  ]
}
```

### Still having issues?

1. **Check GitHub Copilot is active**:
   - Look for Copilot icon in status bar (bottom right)
   - Should show "Copilot" not "Copilot: Inactive"

2. **Verify Copilot Chat is enabled**:
   - You need GitHub Copilot Chat extension installed
   - Check: Extensions → Search "GitHub Copilot Chat"

3. **Check VS Code version**:
   - Help → About
   - Should be version 1.85 or later (for best Copilot support)

4. **View detailed logs**:
   ```
   # In VS Code
   Help → Toggle Developer Tools → Console tab
   # Look for Copilot-related errors
   ```

If issues persist, check [GitHub Copilot documentation](https://docs.github.com/copilot) or ask in #platform-engineering (Slack).

## 📖 Next Steps

### Explore Documentation

1. **[Usage Guide](docs/usage-guide.md)** - Comprehensive usage instructions
2. **[Quick Reference](docs/quick-reference.md)** - Command cheat sheet
3. **[ADRs](mcp-servers/layer1-context/architecture-patterns/adr/)** - Architecture decisions
4. **[Security Baseline](mcp-servers/layer1-context/security-baseline/)** - Security requirements
5. **[Golden Paths](mcp-servers/layer2-goldenpaths/)** - Implementation templates

### Try Advanced Features

```
# Create event-driven service
@workspace create a service that listens to Kafka events

# Add AI features
@workspace integrate Azure OpenAI to add natural language search

# Set up monitoring
@workspace add Application Insights monitoring
```

### Join the Community

- **Slack**: #platform-engineering
- **Office Hours**: Tuesdays 2-4 PM
- **Monthly Demo**: First Friday of each month

### Contribute

Found a better pattern? Want to add a language?

See [CONTRIBUTING.md](CONTRIBUTING.md)

## 🎉 You're Ready!

You now know how to:
- ✅ Scaffold production-ready applications in minutes
- ✅ Apply security baselines automatically
- ✅ Follow architectural patterns consistently
- ✅ Learn best practices from Copilot
- ✅ Customize for your needs

**Start building!** 🚀

---

## Quick Command Reference

```bash
# Scaffold services
@workspace /scaffold-web-api <name>
@workspace /scaffold-frontend <name>

# Security
@workspace /apply-security-baseline

# Learn
@workspace show me [pattern name]
@workspace how do I [task]
@workspace why do we [decision]

# Customize
@workspace add [feature] to this service
@workspace implement [pattern] in this code
```

---

**Questions?** Ask in #platform-engineering or open an issue!

**Ready for more?** Check out the [full documentation](docs/).
