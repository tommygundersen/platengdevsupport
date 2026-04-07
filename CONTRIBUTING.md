# Contributing to Platform Engineering Developer Support

Thank you for your interest in improving our development standards and tooling! This guide explains how to contribute.

## ⚠️ Note: Reference Implementation Structure

**This repository uses a single-repo structure for demonstration purposes.** 

In a real organization, you would contribute to separate repositories:
- **Architecture standards** (`architecture-standards` repo) → ADRs, security baseline
- **Platform templates** (`platform-templates` repo) → Golden paths
- **Developer tools** (`developer-tools` repo) → Skills and CLI tools
- **Internal SDKs** (individual package repos) → Published to internal registries

The contribution workflows described here would apply to each respective repository. See the "Real-World Deployment" section in [README.md](README.md) for proper organizational structure.

## 🎯 What Can You Contribute?

### 1. Update Architecture Decision Records (ADRs)
- Propose new architectural patterns
- Update existing ADRs with lessons learned
- Add examples and anti-patterns

### 2. Enhance Golden Paths
- Update dependencies to latest stable versions
- Add new features to templates
- Improve security or performance
- Fix bugs in generated code

### 3. Create New Skills
- Add support for new languages/frameworks
- Create specialized scaffolding (e.g., batch jobs, AI agents)
- Enhance existing skills

### 4. Improve Documentation
- Clarify usage instructions
- Add troubleshooting guides
- Create video tutorials
- Translate to other languages

### 5. Report Issues
- Bug reports
- Security vulnerabilities
- Usability feedback

## 📋 Contribution Process

### For Architecture Changes (Layer 1)

Changes to ADRs, Security Baseline, or Compliance require **Architecture Review Board** approval.

1. **Create RFC (Request for Comments)**:
   ```
   docs/rfcs/YYYY-MM-DD-your-proposal.md
   ```

2. **RFC Format**:
   ```markdown
   # RFC: [Your Proposal Title]
   
   **Status**: Draft | Review | Accepted | Rejected
   **Author**: Your Name
   **Date**: 2026-04-07
   
   ## Problem Statement
   What problem are we solving?
   
   ## Proposed Solution
   How do we solve it?
   
   ## Alternatives Considered
   What else did we evaluate?
   
   ## Impact Analysis
   - Developer Experience
   - Security
   - Operations
   - Performance
   
   ## Migration Path
   How do existing services adopt this?
   ```

3. **Submit for Review**:
   - Create PR with RFC
   - Tag `@architecture-review-board`
   - Present in Architecture Review meeting

4. **After Approval**:
   - Update ADRs in `mcp-servers/layer1-context/`
   - Create migration guide if needed
   - Announce to teams

### For Golden Path Updates (Layer 2)

Updates to templates can be contributed directly but should be reviewed by Platform Engineering team.

1. **Test Your Changes**:
   ```bash
   # Generate service from your updated golden path
   @workspace /scaffold-web-api test-service
   
   # Verify it builds and runs
   cd test-service
   mvn clean install
   mvn spring-boot:run
   
   # Run security scans
   mvn dependency-check:check
   
   # Test deployment
   docker build -t test-service .
   ```

2. **Update Documentation**:
   - Update golden path README
   - Add changelog entry
   - Update version number

3. **Create PR**:
   ```
   Title: [Golden Path/Web API] Update Spring Boot to 3.2.5
   
   Changes:
   - Updated Spring Boot from 3.2.0 to 3.2.5
   - Updated dependencies to resolve CVE-2024-12345
   - Updated Dockerfile base image
   - Tested with example service (attached logs)
   ```

4. **Review Checklist**:
   - [ ] All dependencies updated
   - [ ] No new security vulnerabilities
   - [ ] Backward compatible (or migration guide provided)
   - [ ] Documentation updated
   - [ ] Example generation tested

### For New Skills

1. **Propose Skill**:
   ```
   # In GitHub issue
   Title: New Skill - Scaffold Batch Job
   
   Description:
   We need a skill to scaffold batch processing applications
   using Spring Batch with Azure Spring Cloud patterns.
   
   Use Cases:
   - Nightly data processing
   - Report generation
   - Data migration
   
   Acceptance Criteria:
   - Follows Layer 1 security baseline
   - Implements retry and error handling
   - Includes monitoring and alerting
   ```

2. **Create Skill**:
   ```
   skills/scaffold-batch-job/SKILL.md
   ```

3. **Follow Skill Template**:
   ```markdown
   ---
   applyTo:
     - '**/*.java'
   description: >
     Brief description of what the skill does
   tools:
     allow:
       - create_file
       - run_in_terminal
   ---
   
   # Your Skill Name
   
   ## Your Mission
   What this skill does when invoked
   
   ## What You Generate
   Detailed specification
   
   ## Parameters
   What questions to ask
   
   ## Mandatory Checklist
   Requirements before completion
   
   ## References
   Links to Layer 1/2 resources
   ```

4. **Test Skill**:
   ```
   # Test the skill command
   @workspace /scaffold-batch-job test-job
   
   # Verify generated code
   # Test security baseline compliance
   # Test CI/CD pipeline
   ```

## 🔐 Security Contributions

**IMPORTANT**: Security issues should be reported privately.

### Reporting Security Vulnerabilities

**DO NOT** create public GitHub issues for security vulnerabilities.

Instead:
1. Email: security@organization.com
2. Include:
   - Description of vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

3. We will:
   - Acknowledge within 24 hours
   - Assess severity
   - Develop fix
   - Coordinate disclosure

### Updating Security Baseline

Changes to security requirements:
1. Must be approved by Security Team
2. Require impact analysis
3. Need migration guide for existing services
4. May require security training update

## 📝 Style Guidelines

### For ADRs (Architecture Decision Records)

```markdown
# ADR-XXX: Descriptive Title

**Status**: Accepted | Deprecated | Superseded
**Date**: YYYY-MM-DD
**Deciders**: Architecture Review Board
**Well-Architected Pillars**: List relevant pillars

## Context
Why are we making this decision?

## Decision
What did we decide?

### DO ✅
- Specific actionable guidance
- Use bullet points

### DON'T ❌
- Clear anti-patterns
- Explain why not

## Consequences

### Positive
- ✅ Benefits

### Negative
- ❌ Trade-offs

## Compliance
What's required for compliance?

## Alternatives Considered
What else did we evaluate?

## Related
- Links to other ADRs
- External references
```

### For Golden Paths

- **Code must work out-of-the-box**
- Use latest stable versions (not bleeding edge)
- Include comprehensive comments
- Follow language/framework conventions
- Include example usage
- Add troubleshooting section

### For Skills

- **Clear, conversational tone**
- Actionable instructions
- Include examples
- Reference Layer 1/2 docs
- Specify mandatory requirements
- Include anti-patterns to avoid

## 🧪 Testing Requirements

### Unit Tests
All golden path code should include unit tests demonstrating:
- Happy path
- Error cases
- Edge cases
- Security scenarios

### Integration Tests
Golden paths should include integration tests for:
- Database connectivity
- Authentication flow
- API endpoints
- External service integration

### Security Tests
- OWASP Dependency Check passes
- No secrets in code
- Input validation present
- Security headers configured

## 📊 Review Process

### Automated Checks
All PRs trigger:
- ✅ Linting (code style)
- ✅ Type checking
- ✅ Unit tests
- ✅ Security scanning
- ✅ Build verification

### Manual Review
PRs are reviewed for:
- Architecture alignment
- Security best practices
- Code quality
- Documentation completeness
- Backward compatibility

### Approval Requirements

| Change Type | Approvals Needed | Timeline |
|-------------|-----------------|----------|
| Typo/Doc fixes | 1 Platform Engineer | 1-2 days |
| Dependency updates | 1 Platform Engineer | 2-3 days |
| Golden Path changes | 2 Platform Engineers | 3-5 days |
| New Skills | 1 Platform Engineer + 1 Security | 5-7 days |
| ADR changes | Architecture Review Board | 2-4 weeks |
| Security Baseline | Security Team + ARB | 4-6 weeks |

## 🎓 Recognition

Contributors are recognized:
- In CHANGELOG.md
- In monthly Platform Engineering newsletter
- In annual DevCon presentations
- With "Platform Champion" badge

Top contributors may be invited to join:
- Architecture Review Board
- Security Champions program
- Platform Engineering team

## 📞 Getting Help

- **Questions**: #platform-engineering (Slack)
- **Pair Programming**: Book time with Platform Engineering team
- **Office Hours**: Tuesdays 2-4 PM (virtual)
- **Documentation**: This repo's `/docs` folder

## 🚀 Quick Reference: Contribution Workflows

### Update Dependencies in Golden Path
```bash
# 1. Update pom.xml or package.json
# 2. Test generation
@workspace /scaffold-web-api test-service

# 3. Verify build
cd test-service && mvn clean install

# 4. Check security
mvn dependency-check:check

# 5. Create PR
```

### Create New ADR
```bash
# 1. Create RFC
touch docs/rfcs/2026-04-07-new-pattern.md

# 2. Write proposal
# 3. Submit for review
# 4. Present to ARB
# 5. After approval, create ADR
touch mcp-servers/layer1-context/architecture-patterns/adr/004-new-pattern.md
```

### Report Bug
```bash
# 1. Create GitHub issue with template
# 2. Tag with appropriate labels
# 3. Include reproduction steps
# 4. Attach logs/screenshots
```

## 🙏 Thank You!

Every contribution makes our development experience better. Whether you're fixing a typo or proposing a new architectural pattern, your input is valued!

---

**Questions?** Reach out in #platform-engineering or open a discussion in this repo.
