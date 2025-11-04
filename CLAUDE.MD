# Security-First Development Guidelines

When coding in this project, follow these security principles from the start:

## Supabase Authentication

- **Always use Supabase helpers** for all auth-related operations
- When implementing auth features, read Supabase docs via the Supabase MCP tool or ask for docs to copy/paste
- Use `getUser()` not `getSession()` in all API routes and server actions
- Use middleware to protect routes

## Authorization

- **Always read `docs/authorization_rules.md`** before implementing any authorization logic
- This document defines who can access what and what actions each role can perform
- Check authentication first (who are you?), then authorization (what can you do?)
- Implement RLS policies on ALL Supabase tables and storage buckets
- Test both positive cases (authorized access works) and negative cases (unauthorized access blocked)

## Input & Output

- Validate ALL user inputs with Zod or similar validation library
- Never use `dangerouslySetInnerHTML` without DOMPurify
- Use parameterized queries, never string concatenation for SQL
- Validate and sanitize user-provided URLs

## Secrets & Environment

- **Never use service role key without explicit approval**
- Never use service role key client-side
- No API keys in code or `NEXT_PUBLIC_` env vars
- Keep all secrets server-side only

## Business Logic & Code Organization

### Server Actions vs API Routes
- **Always use Server Actions** for application logic
- Only create API routes for external integrations (e.g., Stripe webhooks) or multi-app scenarios (mobile + web)

### Workflow Organization
- Keep page-specific workflows in the same folder as the page
- Keep shared workflows in the `lib` folder

### General Rules
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

## Database Migrations

- **Never edit past migration files** - Always create new migrations for changes
- Migration file timestamps must use local date timestamp format
- Keep migration files immutable once applied

## Supabase Functions & Edge Functions

- **Avoid using Supabase Edge Functions** for regular application logic/workflows
- Use Next.js Server Actions instead for standard workflows
- Supabase Functions are OK for RLS policy helpers
- When needed, Supabase Functions should be defined in the **private schema**, not public
