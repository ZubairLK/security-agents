# Security Audit Agents

A comprehensive suite of **23 specialized security audit agents** for Next.js + Supabase applications. Each agent focuses on a specific security domain and writes detailed findings to `security-audit/` folder.

## Philosophy: Defense in Depth (Seatbelts + Airbags)

Multiple layers of security checks at various levels:
- **Authentication** - Verify who you are
- **Authorization** - Verify what you can do
- **Input Validation** - Verify data is expected format
- **Output Sanitization** - Verify data is safe to display
- **Business Logic** - Verify operations are secure

## Quick Start - Recommended Workflow

```bash
# Step 1: Generate authorization rules documentation (prerequisite for many agents)
@authorization-rules-writer

# Step 2: Review the generated rules
cat docs/authorization_rules.md

# Step 3: Run all security agents in parallel (much faster!)
# You can run multiple agents in a single command for parallel execution
@auth-security-auditor @api-auth-checker @input-validation-checker @output-sanitization-checker @query-injection-checker @rls-coverage-checker @supabase-advisor-checker @backend-authorization-checker @email-security-checker @client-secrets-checker @dependency-security-checker @triggerdotdev-security-checker @api-authorization-checker @file-upload-checker @logging-exposure-checker @rate-limiting-checker @browser-security-checker @webhook-security-checker @url-validation-checker @business-logic-checker @input-sanitization-checker

# Step 4: Run meta-analysis to identify patterns and prioritize fixes
@security-meta-analyzer

# Step 5: Review the meta-analysis report for strategic insights
cat security-audit/meta-analysis-report.md
```

Each agent will analyze your codebase and write a report to `security-audit/[agent-name]-report.md`.

---

## Security Coverage Matrix

### 📋 Prerequisites & Analysis

| Agent | Covers | Report File |
|-------|--------|-------------|
| **authorization-rules-writer** | Extracts business authorization rules from codebase, documents WHO can do WHAT | `docs/authorization_rules.md` |
| **security-meta-analyzer** | Analyzes all security reports to identify patterns, systemic issues, and prioritize remediation | `meta-analysis-report.md` |

**Purpose:**
- ✅ Create authorization baseline for other agents to verify against
- ✅ Identify cross-cutting concerns and architectural issues
- ✅ Generate strategic remediation roadmap
- ✅ Calculate composite risk scores for prioritization

---

### 🔐 Authentication & Session Management

| Agent | Covers | Report File |
|-------|--------|-------------|
| **auth-security-auditor** | Supabase Auth helpers usage, session security, middleware protection | `auth-security-report.md` |
| **api-auth-checker** | API routes & server actions use `getUser()` not `getSession()`, authentication presence | `api-auth-report.md` |

**Checklist Coverage:**
- ✅ Authentication is secure (Supabase Auth helpers used everywhere)
- ✅ Middleware & session security
- ✅ Middleware blocks unauthenticated routes
- ✅ All API endpoints & server actions check authentication

---

### 🛡️ Authorization & Access Control

| Agent | Covers | Report File |
|-------|--------|-------------|
| **api-authorization-checker** | API/server action authorization against business rules | `api-authorization-report.md` |
| **rls-coverage-checker** | Database table & storage bucket RLS policies, privilege escalation, cross-tenant access | `rls-coverage-report.md` |
| **backend-authorization-checker** | Database views, functions, triggers, SECURITY DEFINER issues | `backend-authorization-report.md` |
| **supabase-advisor-checker** | Supabase built-in security advisories contextualized with business rules | `supabase-advisor-report.md` |

**Checklist Coverage:**
- ✅ Authorization is secure (right role can only CRUD right things)
- ✅ RLS policies tested (positive and negative cases)
- ✅ Privilege escalation not possible via simple updates
- ✅ All API endpoints & server actions check authorization

---

### 🔒 Input Security

| Agent | Covers | Report File |
|-------|--------|-------------|
| **input-validation-checker** | Zod/validation library usage, all user inputs validated | `input-validation-report.md` |
| **input-sanitization-checker** | Server-side sanitization of HTML, URLs, filenames before storage | `input-sanitization-report.md` |
| **query-injection-checker** | SQL/NoSQL injection prevention, parameterized queries | `query-injection-report.md` |
| **url-validation-checker** | User-provided URLs validated, open redirect & SSRF prevention | `url-validation-report.md` |

**Checklist Coverage:**
- ✅ Input validation (SQL/NoSQL, XSS, data type validation)
- ✅ Input sanitization (rich text, URLs, filenames cleaned server-side)
- ✅ URL validation & sanitization

---

### 🎨 Output Security

| Agent | Covers | Report File |
|-------|--------|-------------|
| **output-sanitization-checker** | XSS prevention, `dangerouslySetInnerHTML` usage, DOMPurify | `output-sanitization-report.md` |
| **logging-exposure-checker** | Sensitive data in console logs, error messages, debug statements | `logging-exposure-report.md` |

**Checklist Coverage:**
- ✅ HTML rendering validation (never use dangerouslySetInnerHTML without DOMPurify)
- ✅ Console logs/stack traces/error logs don't expose sensitive info

---

### 💼 Business Logic & Server-Side Security

| Agent | Covers | Report File |
|-------|--------|-------------|
| **business-logic-checker** | Sensitive calculations server-side only, no client-side trust | `business-logic-report.md` |
| **triggerdotdev-security-checker** | TriggerDev jobs triggered server-side only, authorization in jobs | `triggerdotdev-security-report.md` |

**Checklist Coverage:**
- ✅ Business logic security (sensitive calculations/workflows are server-side)
- ✅ TriggerDev jobs secured (server-side only, validate inputs & authorization)

---

### 🔑 Secrets & Environment Variables

| Agent | Covers | Report File |
|-------|--------|-------------|
| **client-secrets-checker** | Service role key exposure, API keys in client code, `NEXT_PUBLIC_` misuse | `client-secrets-report.md` |

**Checklist Coverage:**
- ✅ No use of service role key client-side
- ✅ No API keys or secrets in code/public env vars
- ✅ Keys only used in server-side code

---

### 🌐 Web Security

| Agent | Covers | Report File |
|-------|--------|-------------|
| **browser-security-checker** | Security headers (X-Frame, CSP, etc.), CSRF protection, SameSite cookies | `browser-security-report.md` |
| **rate-limiting-checker** | Rate limits on APIs, server actions, email sending | `rate-limiting-report.md` |
| **webhook-security-checker** | Stripe/webhook signature validation | `webhook-security-report.md` |

**Checklist Coverage:**
- ✅ Security headers (X-Frame, X-Content-Type, prevent iframe, etc.)
- ✅ CSRF protection
- ✅ All API endpoints & server actions have rate limits
- ✅ Stripe webhook validation

---

### 📁 File & Email Security

| Agent | Covers | Report File |
|-------|--------|-------------|
| **file-upload-checker** | File upload validation, signed URLs, storage bucket isolation, MIME type checks | `file-upload-report.md` |
| **email-security-checker** | Application emails server-side only, templated, sanitized inputs, rate limited | `email-security-report.md` |

**Checklist Coverage:**
- ✅ File storage protection (signed URLs, isolated buckets, upload MIME type protection)
- ✅ Email safety (server-side only, templated, sanitized, rate limited)

---

### 📦 Dependencies & Supply Chain

| Agent | Covers | Report File |
|-------|--------|-------------|
| **dependency-security-checker** | npm audit, dependency vulnerabilities, CODEOWNERS file | `dependency-security-report.md` |

**Checklist Coverage:**
- ✅ Dependency management (npm audit)
- ✅ CODEOWNERS file exists (enables branch protection)

---

## Agent Characteristics

### 🎯 Lean & Principle-Based
All agents are designed to be:
- **Non-prescriptive** - State security principles, don't dictate implementations
- **Context-aware** - Discover what exists, then apply principles
- **Documentation-driven** - Fetch current Supabase docs rather than hardcode patterns

### 🤖 Autonomous
Agents automatically:
- Search codebase for relevant patterns
- Read business rules from `docs/authorization_rules.md`
- Query Supabase documentation (via MCP tools)
- Write comprehensive reports to `security-audit/`

### 🔍 Focused
Each agent has a **single responsibility**:
- One security concern per agent
- No overlap between agents
- Clear, actionable findings

---

## Complete Agent List

| # | Agent Name | Color | Model | Primary Focus |
|---|------------|-------|-------|---------------|
| 1 | authorization-rules-writer | Purple | Opus | Extract & document business authorization rules |
| 2 | auth-security-auditor | Blue | Sonnet | Supabase Auth implementation |
| 3 | api-auth-checker | Green | Sonnet | API authentication (getUser vs getSession) |
| 4 | input-validation-checker | Yellow | Sonnet | Input validation presence |
| 5 | input-sanitization-checker | Orange | Sonnet | Server-side HTML/URL/filename sanitization |
| 6 | output-sanitization-checker | Purple | Sonnet | XSS prevention & output escaping |
| 7 | query-injection-checker | Orange | Sonnet | SQL/NoSQL injection prevention |
| 8 | rls-coverage-checker | Red | Opus | RLS policies on tables & storage |
| 9 | supabase-advisor-checker | Cyan | - | Supabase built-in advisories |
| 10 | backend-authorization-checker | Red | - | Database functions, views, triggers |
| 11 | email-security-checker | - | - | Application email security |
| 12 | client-secrets-checker | - | - | Secrets exposure client-side |
| 13 | dependency-security-checker | - | - | npm audit & CODEOWNERS |
| 14 | triggerdotdev-security-checker | - | - | TriggerDev job security |
| 15 | api-authorization-checker | - | - | API authorization vs business rules |
| 16 | file-upload-checker | Cyan | - | File upload security |
| 17 | logging-exposure-checker | Orange | - | Sensitive data in logs |
| 18 | rate-limiting-checker | Pink | - | Rate limiting on endpoints |
| 19 | browser-security-checker | Purple | - | Security headers & CSRF |
| 20 | webhook-security-checker | Teal | - | Webhook signature validation |
| 21 | url-validation-checker | Indigo | - | URL validation (open redirect, SSRF) |
| 22 | business-logic-checker | Amber | - | Server-side business logic |
| 23 | security-meta-analyzer | Gold | Opus | Cross-report analysis & strategic insights |

---

## Infrastructure & Process (Not Covered by Agents)

The following are **operational/infrastructure concerns** that cannot be detected in code:

### GitHub Repository Settings
- ⚙️ Dependabot configured
- ⚙️ PR approval required for production merges
- ⚙️ Branch protection enabled

### Deployment & Infrastructure
- ⚙️ HTTPS everywhere (enforced by hosting platform)
- ⚙️ Monitoring (PostHog configured)

### Access Control & Procedures
- ⚙️ Secrets rotation policy
- ⚙️ Supabase production access read-only
- ⚙️ Production secrets access limited

**Note:** While agents check for `CODEOWNERS` file existence, the actual GitHub branch protection rules must be configured manually in GitHub settings.

---

## Running a Complete Security Audit

### Sequential Approach
```bash
# Run all agents one by one
@auth-security-auditor
@api-auth-checker
@input-validation-checker
@output-sanitization-checker
@query-injection-checker
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

### Review Reports
All reports will be in `security-audit/` folder:
```bash
ls -la security-audit/
# Review each *-report.md file
```

---

## Agent Output Format

Each agent writes a markdown report with:
- **Summary** - High-level findings count
- **Critical Issues** (🔴) - Immediate security vulnerabilities
- **Warnings** (⚠️) - Security weaknesses
- **Properly Handled** (✅) - What's working correctly
- **Recommendations** - Specific fixes with file:line references

---

## Business Rules Integration

Many agents read `docs/authorization_rules.md` to understand your application-specific security requirements. This file should document:
- Who can access what resources
- Role-based permissions
- Multi-tenant isolation rules
- Sensitive operations and their requirements

Agents that use business rules:
- `rls-coverage-checker`
- `supabase-advisor-checker`
- `backend-authorization-checker`
- `api-authorization-checker`
- `triggerdotdev-security-checker`

---

## Best Practices

1. **Run agents regularly** - After significant features or before releases
2. **Fix critical issues first** - 🔴 Critical > ⚠️ High > 🟢 Low
3. **Update business rules** - Keep `docs/authorization_rules.md` current
4. **Review all reports** - Don't cherry-pick, comprehensive security requires all layers
5. **Track fixes** - Use security reports to track remediation progress

---

## Contributing

When adding new security agents:
1. Follow the **lean, principle-based** approach
2. Add **Write** tool to save reports
3. Include instruction to write to `security-audit/[agent-name]-report.md`
4. Update this README with the new agent
5. Ensure single responsibility - one security concern per agent

---

## Support

For issues or questions about security agents:
- Check agent-specific `.md` files in `.claude/agents/`
- Review Supabase documentation for auth/RLS best practices
- Consult `docs/authorization_rules.md` for business-specific requirements
