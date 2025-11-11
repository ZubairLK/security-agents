# Security Audit Agents for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-blue)](https://claude.ai/claude-code)
[![Next.js](https://img.shields.io/badge/Next.js-Optimized-black)](https://nextjs.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Ready-green)](https://supabase.com/)

A comprehensive suite of **23 specialized security audit agents** designed for Next.js + Supabase applications. These agents can be used in Claude Code to automatically analyze your codebase for security vulnerabilities and best practices.

> **📦 Optimized for Next.js + Supabase, but adaptable to any stack!** While these agents are specifically tailored for Next.js and Supabase, the security principles and patterns they check for are universal. See [Adapting to Your Stack](#adapting-to-your-stack) for guidance on customizing for other frameworks.

## What's Included

- **23 Security Agents** - Each focused on a specific security domain
- **Claude Code Settings** - Pre-configured permissions and MCP server integrations
- **Defense in Depth** - Multiple layers of security coverage
- **Meta-Analysis** - Strategic insights across all security reports
- **Authorization Rules Writer** - Automatic business rules documentation

## Installation

1. Copy the `.claude` directory to your project root:
   ```bash
   cp -r .claude /path/to/your/project/
   ```

2. Launch Claude Code in your project directory

3. The agents will be available with `@` prefix (e.g., `@auth-security-auditor`)

## Quick Start

### Optimal 3-Step Workflow

```bash
# Step 1: Generate authorization rules (prerequisite for many agents)
@authorization-rules-writer

# Step 2: Run all security agents in parallel (fastest!)
@auth-security-auditor @api-auth-checker @api-authorization-checker @input-validation-checker @input-sanitization-checker @output-sanitization-checker @query-injection-checker @rls-coverage-checker @supabase-advisor-checker @backend-authorization-checker @email-security-checker @client-secrets-checker @dependency-security-checker @triggerdotdev-security-checker @file-upload-checker @logging-exposure-checker @rate-limiting-checker @browser-security-checker @webhook-security-checker @url-validation-checker @business-logic-checker

# Step 3: Run meta-analysis for strategic insights
@security-meta-analyzer
```

**What you get:**
- `docs/authorization_rules.md` - Your application's business authorization rules
- `security-audit/*-report.md` - 21 detailed security reports
- `security-audit/meta-analysis-report.md` - Strategic analysis with prioritized roadmap

## Security Coverage

### 🔐 Authentication & Session Management
- **auth-security-auditor** - Supabase Auth helpers, session security, middleware
- **api-auth-checker** - API routes & server actions authentication

### 🛡️ Authorization & Access Control
- **api-authorization-checker** - Business rule authorization
- **rls-coverage-checker** - Database RLS policies
- **backend-authorization-checker** - Database functions, views, triggers
- **supabase-advisor-checker** - Supabase security advisories

### 🔒 Input Security
- **input-validation-checker** - Zod/validation library usage
- **database-query-checker** - SQL/NoSQL injection prevention
- **url-validation-checker** - URL validation, open redirect & SSRF prevention

### 🎨 Output Security
- **output-sanitization-checker** - XSS prevention
- **logging-exposure-checker** - Sensitive data in logs

### 💼 Business Logic & Server-Side
- **business-logic-checker** - Server-side business logic
- **triggerdotdev-security-checker** - TriggerDev job security

### 🔑 Secrets & Environment
- **client-secrets-checker** - Secret exposure prevention

### 🌐 Web Security
- **browser-security-checker** - Security headers, CSRF
- **rate-limiting-checker** - Rate limiting on endpoints
- **webhook-security-checker** - Webhook signature validation

### 📁 File & Email
- **file-upload-checker** - File upload security
- **email-security-checker** - Email security

### 📦 Dependencies
- **dependency-security-checker** - npm audit, vulnerabilities

## Agent Features

### 🎯 Lean & Principle-Based
- Non-prescriptive - State principles, don't dictate implementations
- Context-aware - Discover existing code, then apply principles
- Documentation-driven - Fetch current docs rather than hardcode patterns

### 🤖 Autonomous
- Automatically search codebase
- Read business rules from `docs/authorization_rules.md`
- Query Supabase documentation via MCP tools
- Write comprehensive reports

### 🔍 Focused
- Single responsibility per agent
- No overlap between agents
- Clear, actionable findings

## Report Format

Each agent generates a markdown report with:
- **Summary** - High-level findings count
- **Critical Issues** (🔴) - Immediate vulnerabilities
- **Warnings** (⚠️) - Security weaknesses
- **Properly Handled** (✅) - What's working
- **Recommendations** - Specific fixes with file:line references

## Configuration

The `.claude/settings.local.json` includes:
- Pre-approved bash commands for security operations
- MCP server configurations (Supabase, Context7, Playwright)
- Permission settings optimized for security auditing

## Business Rules Integration

Many agents read `docs/authorization_rules.md` to understand your application-specific security requirements. Document:
- Who can access what resources
- Role-based permissions
- Multi-tenant isolation rules
- Sensitive operations requirements

## Best Practices

1. **Run agents regularly** - After features or before releases
2. **Fix critical issues first** - 🔴 Critical > ⚠️ High > 🟢 Low
3. **Update business rules** - Keep authorization rules current
4. **Review all reports** - Comprehensive security requires all layers
5. **Track fixes** - Use reports to track remediation progress

## Running a Complete Audit

### Optimal 3-Step Workflow

```bash
# Step 1: Generate authorization rules (prerequisite for other agents)
@authorization-rules-writer

# Step 2: Run all security agents in parallel (fastest approach!)
@auth-security-auditor @api-auth-checker @api-authorization-checker @input-validation-checker @input-sanitization-checker @output-sanitization-checker @query-injection-checker @rls-coverage-checker @supabase-advisor-checker @backend-authorization-checker @email-security-checker @client-secrets-checker @dependency-security-checker @triggerdotdev-security-checker @file-upload-checker @logging-exposure-checker @rate-limiting-checker @browser-security-checker @webhook-security-checker @url-validation-checker @business-logic-checker

# Step 3: Run meta-analysis for strategic insights
@security-meta-analyzer
```

All reports will be written to `security-audit/*.md`

The meta-analysis report (`security-audit/meta-analysis-report.md`) will provide:
- Cross-cutting concerns across all reports
- Systemic patterns and root causes
- Prioritized remediation roadmap
- Vulnerability heat map
- Quick wins and strategic recommendations

## Technical Details

Built for:
- **Claude Code** - Anthropic's official CLI
- **Next.js** applications
- **Supabase** backend
- **TypeScript/JavaScript** codebases

Uses:
- MCP (Model Context Protocol) for Supabase documentation
- Task agents for autonomous code analysis
- Write tool for generating reports

## Full Agent Details

For detailed information about each agent, see `.claude/agents/README.md`

---

## Adapting to Your Stack

While optimized for **Next.js + Supabase**, these agents can be customized for your tech stack.

### Universal Agents (Work Anywhere)

These agents work across all frameworks without modification:
- input-validation-checker
- output-sanitization-checker
- client-secrets-checker
- dependency-security-checker
- logging-exposure-checker
- rate-limiting-checker
- webhook-security-checker
- business-logic-checker

### Framework-Specific Agents

These need customization for your stack:
- **auth-security-auditor** - Update for your auth library patterns
- **rls-coverage-checker** - Adapt for your database access control
- **api-auth-checker** - Change API route patterns
- **api-authorization-checker** - Update authorization checking patterns
- **backend-authorization-checker** - Modify for your database system
- **triggerdotdev-security-checker** - Replace with your background job system

### How to Customize

1. Copy agent file from `.claude/agents/[agent-name].md`
2. Update search patterns for your framework
3. Replace documentation references (if using MCP tools)
4. Keep security principles unchanged (OWASP guidelines are universal)

See individual agent files for their search patterns and modify accordingly for your stack.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions welcome! Please ensure:
- Agents follow the lean, principle-based approach
- Single responsibility per agent
- Include Write tool for report generation
- Update documentation

## Support

- **Issues**: [GitHub Issues](https://github.com/ZubairLK/security-agents/issues)
- **Discussions**: [GitHub Discussions](https://github.com/ZubairLK/security-agents/discussions)
- **Documentation**: See `.claude/agents/README.md` for detailed agent docs

## Credits

Created by [Zubair LK](https://github.com/ZubairLK) for comprehensive security auditing of modern web applications.

Built with ❤️ for the Next.js + Supabase community.
