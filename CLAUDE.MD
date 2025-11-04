# Security-First Development Guidelines

When coding in this project, follow these security principles from the start:

## Authentication & Authorization

- Use `getUser()` not `getSession()` in all API routes and server actions
- Check authentication AND authorization on every API endpoint
- Implement RLS policies on ALL Supabase tables and storage buckets
- Use middleware to protect routes

## Input & Output

- Validate ALL user inputs with Zod or similar validation library
- Never use `dangerouslySetInnerHTML` without DOMPurify
- Use parameterized queries, never string concatenation for SQL
- Validate and sanitize user-provided URLs

## Secrets & Environment

- Never use service role key client-side
- No API keys in code or `NEXT_PUBLIC_` env vars
- Keep all secrets server-side only

## Business Logic

- Sensitive calculations and workflows must be server-side
- Never trust client-side data for important operations
- TriggerDev jobs must validate authorization

## Web Security

- Implement rate limiting on all APIs and server actions
- Use security headers (CSP, X-Frame-Options, etc.)
- Validate webhook signatures (Stripe, etc.)
- Use SameSite cookies and CSRF protection

## File & Email

- Validate file uploads (MIME type, size)
- Use signed URLs for file access
- Send emails server-side only with templating
- Rate limit email sending

## Logging & Monitoring

- Never log sensitive data (passwords, tokens, PII)
- Sanitize error messages shown to users
- Keep detailed logs server-side only

## Dependencies

- Run `npm audit` regularly
- Keep dependencies updated
- Add CODEOWNERS file for branch protection

## Quick Reference

Before committing code, ask:
1. Did I authenticate AND authorize?
2. Did I validate all inputs?
3. Did I sanitize all outputs?
4. Is business logic server-side only?
5. Are secrets protected?

Run security agents regularly with `@[agent-name]` to catch issues early.
