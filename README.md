# Security Audit Agents for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-blue)](https://claude.ai/claude-code)
[![Next.js](https://img.shields.io/badge/Next.js-Optimized-black)](https://nextjs.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Ready-green)](https://supabase.com/)

A comprehensive suite of **23 specialized security audit agents** designed for Next.js + Supabase applications. These agents can be used in Claude Code to automatically analyze your codebase for security vulnerabilities and best practices.

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

### Recommended Workflow

```bash
# Step 1: Generate authorization rules documentation
@authorization-rules-writer

# Step 2: Review the generated rules
cat docs/authorization_rules.md

# Step 3: Run all security agents in parallel (fastest!)
@auth-security-auditor @api-auth-checker @input-validation-checker @output-sanitization-checker @query-injection-checker @rls-coverage-checker @supabase-advisor-checker @backend-authorization-checker @email-security-checker @client-secrets-checker @dependency-security-checker @triggerdotdev-security-checker @api-authorization-checker @file-upload-checker @logging-exposure-checker @rate-limiting-checker @browser-security-checker @webhook-security-checker @url-validation-checker @business-logic-checker @input-sanitization-checker

# Step 4: Run meta-analysis for strategic insights
@security-meta-analyzer

# Step 5: Review the comprehensive analysis
cat security-audit/meta-analysis-report.md
```

Each agent will analyze your codebase and write a detailed report to `security-audit/[agent-name]-report.md`.

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

Run all agents sequentially:
```bash
@auth-security-auditor
@api-auth-checker
@input-validation-checker
@output-sanitization-checker
@database-query-checker
@rls-coverage-checker
@supabase-advisor-checker
@backend-authorization-checker
@email-security-checker
@client-secrets-checker
@dependency-security-checker
@triggerdotdev-security-checker
@api-authorization-checker
@file-upload-checker
@logging-exposure-checker
@rate-limiting-checker
@browser-security-checker
@webhook-security-checker
@url-validation-checker
@business-logic-checker
```

All reports will be written to `security-audit/*.md`

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
