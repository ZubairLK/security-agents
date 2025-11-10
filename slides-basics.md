---
theme: default
background: https://images.unsplash.com/photo-1550751827-4bd374c3f58b
class: text-center
highlighter: shiki
lineNumbers: true
drawings:
  persist: false
transition: slide-left
title: Security Fundamentals for Next.js + Supabase
mdc: true
---

# Security Fundamentals
## For Next.js + Supabase Applications

A guide for developers transitioning from Bubble to Next.js

---
layout: center
---

# The Security Mindset Shift

<v-clicks>

## In Bubble
- Security is mostly automatic
- Server/client handled for you
- Database rules built-in
- Less to think about

## In Next.js + Supabase
- Security is **your responsibility**
- Explicit server vs client decisions
- Multiple layers to configure
- More control = more responsibility

</v-clicks>

---
layout: two-cols
---

# Defense in Depth

Multiple layers of security working together

::right::

<v-clicks>

**Like a car's safety:**
- Seatbelts (first line)
- Airbags (second line)
- Crumple zones (third line)
- ABS brakes (prevention)

**In our apps:**
- Authentication (who are you?)
- Authorization (what can you do?)
- RLS (database protection)
- Input validation (check data)
- Output sanitization (safe display)
- Secrets management (protect keys)
- Server-side logic (trusted execution)

</v-clicks>

---
layout: center
---

# 1. Authentication
## Who are you?

---

# Authentication: Who Are You?

Verifying the identity of users

<v-clicks>

## Key Concepts

**Always use Supabase helpers**
```typescript
// ✅ Correct - Use getUser()
const { data: { user } } = await supabase.auth.getUser()

// ❌ Wrong - Don't use getSession()
const { data: { session } } = await supabase.auth.getSession()
```

**Why getUser() not getSession()?**
- `getUser()` verifies the token with Supabase server (secure)
- `getSession()` just reads local storage (can be manipulated)

</v-clicks>

---

# Authentication: Middleware Protection

Protect routes before users reach them

```typescript
// middleware.ts
import { createMiddlewareClient } from '@supabase/auth-helpers-nextjs'

export async function middleware(req: NextRequest) {
  const res = NextResponse.next()
  const supabase = createMiddlewareClient({ req, res })

  const { data: { user } } = await supabase.auth.getUser()

  // Redirect unauthenticated users
  if (!user && req.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', req.url))
  }

  return res
}
```

<v-clicks>

**Protects:**
- Dashboard routes
- Admin pages
- User-specific pages

</v-clicks>

---
layout: center
---

# 2. Authorization
## What can you do?

---

# Authorization: What Can You Do?

Authentication ≠ Authorization

<v-clicks>

## Authentication
"Are you logged in?" → Yes/No

## Authorization
"Can you do this action?" → Depends on your role/permissions

</v-clicks>

---

# Authorization: Always Check Both

```typescript
// ❌ Only checking authentication
export async function deleteProject(projectId: string) {
  const user = await getUser()
  if (!user) return { error: 'Not authenticated' }

  // Anyone logged in can delete any project! 🚨
  await supabase.from('projects').delete().eq('id', projectId)
}

// ✅ Check authentication AND authorization
export async function deleteProject(projectId: string) {
  const user = await getUser()
  if (!user) return { error: 'Not authenticated' }

  // Check ownership
  const project = await getProject(projectId)
  if (project.userId !== user.id) return { error: 'Not authorized' }

  // Check role permission
  if (!canDelete(user.role)) return { error: 'Insufficient permissions' }

  await supabase.from('projects').delete().eq('id', projectId)
}
```

---

# Authorization: The docs/authorization_rules.md File

**Always read this file before implementing authorization**

```markdown
# Authorization Rules

## User Roles
- **admin**: Full system access
- **user**: Access to own data only

## Access Rules
| Role | What They Can Access | What They Can Do |
|------|---------------------|------------------|
| **admin** | All data | Everything |
| **user** | Own data only | CRUD own data |

## Data Isolation
- Each user can only access their own data
- No cross-user access
```

This guides both developers and security agents!

---
layout: center
---

# 3. Row Level Security (RLS)
## Database-level protection

---

# RLS: Your Database Firewall

**Defense at the database level**

<v-clicks>

## Without RLS
```typescript
// If your app code has a bug, users can access any data
const { data } = await supabase.from('projects').select('*')
// Returns ALL projects (security breach!)
```

## With RLS
```sql
-- Policy: Users can only see their own projects
CREATE POLICY "Users see own projects"
ON projects FOR SELECT
USING (auth.uid() = user_id);
```

```typescript
// Same code, but RLS protects you
const { data } = await supabase.from('projects').select('*')
// Returns ONLY user's projects (safe!)
```

**RLS policies run on every database query, regardless of your app code**

</v-clicks>

---

# RLS: Enable on ALL Tables

```sql
-- Enable RLS on table
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Create policies for each operation
CREATE POLICY "Users can read own projects"
ON projects FOR SELECT
USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own projects"
ON projects FOR INSERT
WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own projects"
ON projects FOR UPDATE
USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own projects"
ON projects FOR DELETE
USING (auth.uid() = user_id);
```

**Every table needs RLS enabled + policies defined**

---

# RLS: Multi-tenant Example

```sql
-- For a franchise/organization model
CREATE POLICY "Users can read org data"
ON appointments FOR SELECT
USING (
  organization_id IN (
    SELECT organization_id
    FROM user_organizations
    WHERE user_id = auth.uid()
  )
);

-- Managers can only access assigned branches
CREATE POLICY "Managers access assigned branches"
ON appointments FOR ALL
USING (
  branch_id IN (
    SELECT branch_id
    FROM user_branch_assignments
    WHERE user_id = auth.uid()
    AND role = 'manager'
  )
);
```

---
layout: center
---

# 4. Input Validation
## Trust nothing from users

---

# Input Validation: Trust Nothing

**All user input is potentially malicious**

<v-clicks>

## The Problem
```typescript
// ❌ Accepting user input without validation
export async function createProject(formData: any) {
  // What if formData.name is 10MB of text?
  // What if formData.email is not an email?
  // What if formData.userId was manipulated?

  await supabase.from('projects').insert(formData)
}
```

## The Solution: Use Zod
```typescript
import { z } from 'zod'

const ProjectSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
  description: z.string().max(500).optional(),
})

export async function createProject(formData: unknown) {
  // Validate and parse
  const validated = ProjectSchema.parse(formData)

  await supabase.from('projects').insert(validated)
}
```

</v-clicks>

---

# Input Validation: Common Attacks

<v-clicks>

## SQL Injection
```typescript
// ❌ Never do this
const query = `SELECT * FROM users WHERE id = ${userId}`

// ✅ Always use parameterized queries (Supabase does this)
await supabase.from('users').select('*').eq('id', userId)
```

## Mass Assignment
```typescript
// ❌ User sends extra fields to gain admin access
const userData = { name: 'John', role: 'admin' } // role shouldn't be settable!

// ✅ Only accept expected fields
const UserSchema = z.object({
  name: z.string(),
  email: z.string().email(),
  // role is NOT in schema - can't be set by user
})
```

</v-clicks>

---

# Input Validation: Validate Everything

**Every API route and Server Action needs validation**

```typescript
// app/actions/projects.ts
'use server'

import { z } from 'zod'

const CreateProjectInput = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(1000).optional(),
})

export async function createProject(input: unknown) {
  // 1. Validate input
  const data = CreateProjectInput.parse(input)

  // 2. Authenticate
  const user = await getUser()
  if (!user) throw new Error('Not authenticated')

  // 3. Authorize (business logic)
  if (user.projectCount >= user.maxProjects) {
    throw new Error('Project limit reached')
  }

  // 4. Execute
  return await supabase.from('projects').insert({
    ...data,
    user_id: user.id, // Set server-side, not from input!
  })
}
```

---
layout: center
---

# 5. Input Sanitization
## Cleaning user data server-side

---

# Input Sanitization: Validation vs Sanitization

**Two different steps in input security**

<v-clicks>

## Input Validation (What we just covered)
- **Check if data is valid**
- Reject invalid data
- Use Zod schemas
- Example: "Is this an email? Is length < 100?"

## Input Sanitization (This section)
- **Clean/transform data before using it**
- Remove dangerous content
- Escape special characters
- Example: Strip HTML tags, remove scripts

**Both are needed for complete security!**

</v-clicks>

---

# Input Sanitization: When You Need It

<v-clicks>

## Common scenarios:

**1. Rich Text Content**
- Blog posts
- Comments
- User bios with formatting

**2. User-Provided URLs**
- Profile links
- External references

**3. Filenames**
- File uploads
- Document names

**4. Data Used in Multiple Contexts**
- Content that goes into emails
- Data displayed in different formats

</v-clicks>

---

# Input Sanitization: Server-Side Only

**ALWAYS sanitize on the server, NEVER trust client-side sanitization**

```typescript
// ❌ Client-side sanitization (can be bypassed)
'use client'
function BlogPostForm() {
  const handleSubmit = (data) => {
    const clean = DOMPurify.sanitize(data.content) // User can modify this!
    await createPost(clean)
  }
}

// ✅ Server-side sanitization (trusted)
'use server'
import DOMPurify from 'isomorphic-dompurify'

export async function createPost(input: unknown) {
  // 1. Validate format
  const validated = BlogPostSchema.parse(input)

  // 2. Sanitize content (server-side!)
  const sanitized = {
    title: validated.title,
    content: DOMPurify.sanitize(validated.content, {
      ALLOWED_TAGS: ['p', 'strong', 'em', 'ul', 'ol', 'li', 'a'],
      ALLOWED_ATTR: ['href']
    })
  }

  // 3. Save to database
  await supabase.from('posts').insert(sanitized)
}
```

---

# Input Sanitization: Rich Text Example

**Two-step process: Validate then Sanitize**

```typescript
'use server'
import { z } from 'zod'
import DOMPurify from 'isomorphic-dompurify'

// 1. Validation schema
const BlogPostSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(1).max(10000), // HTML allowed
  author_id: z.string().uuid(),
})

export async function createBlogPost(input: unknown) {
  const user = await getUser()
  if (!user) throw new Error('Not authenticated')

  // 2. Validate structure
  const validated = BlogPostSchema.parse(input)

  // 3. Sanitize HTML content (remove dangerous tags/attributes)
  const sanitized = {
    ...validated,
    content: DOMPurify.sanitize(validated.content, {
      ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'u', 'h1', 'h2', 'h3',
                     'ul', 'ol', 'li', 'a', 'blockquote', 'code'],
      ALLOWED_ATTR: ['href', 'title'],
      ALLOW_DATA_ATTR: false,
    }),
    author_id: user.id, // Override from session, not input!
  }

  // 4. Save sanitized content
  return await supabase.from('blog_posts').insert(sanitized)
}
```

---

# Input Sanitization: URL Example

**User-provided URLs need validation AND sanitization**

```typescript
import { z } from 'zod'

const ProfileSchema = z.object({
  name: z.string().max(100),
  website: z.string().url().optional(), // Basic validation
  bio: z.string().max(500),
})

export async function updateProfile(input: unknown) {
  const user = await getUser()
  if (!user) throw new Error('Not authenticated')

  const validated = ProfileSchema.parse(input)

  // Sanitize URL - only allow http/https
  let sanitizedWebsite = null
  if (validated.website) {
    try {
      const url = new URL(validated.website)
      // Only allow http and https protocols
      if (url.protocol === 'http:' || url.protocol === 'https:') {
        sanitizedWebsite = url.toString()
      }
    } catch {
      throw new Error('Invalid URL')
    }
  }

  // Sanitize bio (plain text, no HTML)
  const sanitizedBio = validated.bio
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')

  await supabase.from('profiles').update({
    name: validated.name,
    website: sanitizedWebsite,
    bio: sanitizedBio,
  }).eq('user_id', user.id)
}
```

---

# Input Sanitization: Defense in Depth

**Sanitize on input AND output for maximum security**

```typescript
// Step 1: Sanitize when SAVING (Input Sanitization)
'use server'
export async function createComment(input: unknown) {
  const validated = CommentSchema.parse(input)

  const sanitized = {
    ...validated,
    content: DOMPurify.sanitize(validated.content, {
      ALLOWED_TAGS: ['p', 'br', 'strong', 'em'],
    })
  }

  await supabase.from('comments').insert(sanitized)
}

// Step 2: Sanitize when DISPLAYING (Output Sanitization)
function CommentDisplay({ comment }) {
  return (
    <div
      dangerouslySetInnerHTML={{
        __html: DOMPurify.sanitize(comment.content) // Sanitize again!
      }}
    />
  )
}
```

<v-clicks>

**Why both?**
- Input sanitization = Protects your database
- Output sanitization = Protects your users
- If one fails, the other catches it

</v-clicks>

---

# Input Sanitization: Common Mistakes

<v-clicks>

## ❌ Mistake 1: Only client-side sanitization
```typescript
// Client can bypass this
const clean = DOMPurify.sanitize(userInput)
await saveToDatabase(clean)
```

## ✅ Fix: Always server-side
```typescript
'use server'
export async function save(input: unknown) {
  const clean = DOMPurify.sanitize(input) // Server-side
  await saveToDatabase(clean)
}
```

## ❌ Mistake 2: Sanitizing but not validating
```typescript
// Still vulnerable - what if content is 10GB?
const clean = DOMPurify.sanitize(input.content)
```

## ✅ Fix: Validate first, then sanitize
```typescript
const validated = z.string().max(10000).parse(input.content)
const clean = DOMPurify.sanitize(validated)
```

</v-clicks>

---
layout: center
---

# 6. Output Sanitization
## Safe display of user data

---

# Output Sanitization: Preventing XSS

**Cross-Site Scripting (XSS) attacks**

<v-clicks>

## The Problem
```tsx
// User submits this as their name:
const userName = '<img src=x onerror="alert(document.cookie)">'

// ❌ This executes the malicious script
<div dangerouslySetInnerHTML={{ __html: userName }} />
```

## What Happens
1. User submits malicious HTML/JavaScript
2. App displays it without sanitization
3. Script executes in other users' browsers
4. Attacker steals cookies, sessions, data

</v-clicks>

---

# Output Sanitization: React's Default Protection

**React automatically escapes HTML**

```tsx
// ✅ React escapes this automatically - safe!
<div>{userName}</div>
// Renders: &lt;img src=x onerror="alert(document.cookie)"&gt;

// ✅ Also safe in attributes
<input value={userName} />

// ❌ DANGEROUS - bypasses React's protection
<div dangerouslySetInnerHTML={{ __html: userName }} />
```

<v-clicks>

**Rule: Never use `dangerouslySetInnerHTML` without sanitization**

</v-clicks>

---

# Output Sanitization: When You Need HTML

**Use DOMPurify to sanitize**

```tsx
import DOMPurify from 'isomorphic-dompurify'

// ❌ NEVER do this
<div dangerouslySetInnerHTML={{ __html: userContent }} />

// ✅ Sanitize first
<div
  dangerouslySetInnerHTML={{
    __html: DOMPurify.sanitize(userContent)
  }}
/>
```

<v-clicks>

**DOMPurify removes:**
- `<script>` tags
- Event handlers (onclick, onerror, etc.)
- JavaScript URLs (javascript:...)
- Dangerous attributes

**DOMPurify keeps:**
- Safe HTML (p, div, strong, em, etc.)
- Safe attributes (class, id, etc.)

</v-clicks>

---

# Output Sanitization: Other Contexts

<v-clicks>

## URLs
```tsx
// ❌ User can inject javascript: URLs
<a href={userProvidedUrl}>Click me</a>

// ✅ Validate URLs
const SafeUrlSchema = z.string().url().refine(
  (url) => url.startsWith('http://') || url.startsWith('https://')
)
```

## Email Display
```tsx
// ❌ Can leak data in error messages
<p>Error: {error.message}</p>

// ✅ Sanitize error messages
<p>Error: {sanitizeErrorMessage(error)}</p>
```

## Logs
```typescript
// ❌ Logging sensitive data
console.log('User login:', user.email, user.password)

// ✅ Never log sensitive data
console.log('User login:', user.id)
```

</v-clicks>

---
layout: center
---

# 7. Secrets Management
## Protecting API keys and sensitive data

---

# Secrets Management: The Bubble → Next.js Shift

<v-clicks>

## In Bubble
- API keys stored in Bubble's secure backend
- Automatically handled
- Can't accidentally expose them

## In Next.js
- **You** decide what's client-side vs server-side
- Easy to accidentally expose secrets
- Must be explicit about protection

</v-clicks>

---

# Secrets Management: Client vs Server

**Understanding `NEXT_PUBLIC_` prefix**

```bash
# .env.local

# ✅ Server-side only (safe for secrets)
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJ...
STRIPE_SECRET_KEY=sk_live_...
DATABASE_URL=postgresql://...

# ⚠️ Client-side (publicly visible in browser)
NEXT_PUBLIC_SUPABASE_URL=https://xyz.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJ...
```

<v-clicks>

**Rule: Only use `NEXT_PUBLIC_` for non-sensitive values**

- `NEXT_PUBLIC_` vars are bundled into client JavaScript
- Anyone can see them in browser DevTools
- Never use for API keys, secrets, or credentials

</v-clicks>

---

# Secrets Management: Service Role Key

**The most dangerous key**

<v-clicks>

```typescript
// ❌ NEVER use service role key client-side
const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.SUPABASE_SERVICE_ROLE_KEY // 🚨 Exposed to browser!
)

// ✅ Use anon key client-side
const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY // ✅ Safe
)

// ✅ Use service role key ONLY server-side
// (Server Actions, API Routes, never in components)
const supabaseAdmin = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.SUPABASE_SERVICE_ROLE_KEY // ✅ Server-side only
)
```

**Service role key bypasses all RLS policies - total database access!**

</v-clicks>

---

# Secrets Management: Agency Rule

**Never use service role key without approval**

<v-clicks>

## Why this rule?
- Service role key = full database access
- Bypasses all RLS policies
- One mistake = security breach
- Client data at risk

## When is it needed?
- Admin operations (with approval)
- Background jobs
- Data migrations
- System maintenance

## Always ask yourself:
1. Can I use the anon key instead?
2. Can RLS policies handle this?
3. Do I really need to bypass security?

</v-clicks>

---
layout: center
---

# 8. Server-side vs Client-side
## The Next.js mindset shift

---

# Server vs Client: The Biggest Change

**This is the #1 concept Bubble developers must understand**

<v-clicks>

## In Bubble
```text
[User Action] → [Bubble Backend] → [Database]
```
Everything goes through backend automatically

## In Next.js
```text
[User Action] → [Client Component] → ???
                    ↓
                [Server Action] → [Database]
```
**You** decide what runs where

</v-clicks>

---

# Server vs Client: What Runs Where

<v-clicks>

## Client-side (Browser)
- React components (by default)
- User interactions
- UI state
- Form validation (for UX)
- **NO secrets allowed**
- **NO trusted operations**

## Server-side (Your Server)
- Server Components
- Server Actions (`'use server'`)
- API Routes
- Database queries
- **Secrets safe here**
- **Trusted operations only here**

</v-clicks>

---

# Server vs Client: Server Actions

**Use Server Actions, not API routes (for app logic)**

```typescript
// app/actions/projects.ts
'use server' // This marks it as server-only

import { createServerClient } from '@/lib/supabase-server'

export async function createProject(formData: FormData) {
  // This code runs on the server
  const supabase = createServerClient()

  // Safe to use secrets here
  const user = await supabase.auth.getUser()

  // Safe to do sensitive operations
  await supabase.from('projects').insert({
    name: formData.get('name'),
    user_id: user.data.user?.id,
  })
}
```

```tsx
// app/components/CreateProjectForm.tsx
'use client'

import { createProject } from '@/app/actions/projects'

export function CreateProjectForm() {
  return (
    <form action={createProject}>
      <input name="name" />
      <button>Create</button>
    </form>
  )
}
```

---

# Server vs Client: Common Mistakes

<v-clicks>

## ❌ Mistake 1: Trusting client-side data
```typescript
// Client component
function DeleteButton({ projectId, userId }) {
  // User can change userId in DevTools!
  await fetch('/api/delete', {
    body: JSON.stringify({ projectId, userId })
  })
}
```

## ✅ Fix: Get user on server
```typescript
// Server Action
'use server'
export async function deleteProject(projectId: string) {
  const user = await getUser() // Get from session, not from client
  // Verify ownership server-side
}
```

</v-clicks>

---

# Server vs Client: Common Mistakes (cont.)

<v-clicks>

## ❌ Mistake 2: Doing calculations client-side
```typescript
// Client component
function Checkout({ items }) {
  const total = items.reduce((sum, item) => sum + item.price, 0)
  await createOrder({ items, total }) // User can manipulate total!
}
```

## ✅ Fix: Calculate on server
```typescript
// Server Action
'use server'
export async function createOrder(items: Item[]) {
  // Calculate total server-side (trusted)
  const total = items.reduce((sum, item) => sum + item.price, 0)
  await saveOrder({ items, total })
}
```

</v-clicks>

---

# Server vs Client: The Rule

<v-clicks>

## If it involves:
- Money
- Permissions
- Sensitive data
- Business logic
- Database writes
- Secrets/API keys

## Then it must be:
**SERVER-SIDE ONLY**

Use:
- Server Actions (`'use server'`)
- API Routes (for webhooks)
- Server Components

</v-clicks>

---
layout: center
---

# Defense in Depth
## Why we need ALL the layers

---

# Defense in Depth: Working Together

**Each layer catches what others might miss**

```typescript
// Layer 1: Middleware (Authentication)
export async function middleware(req) {
  const user = await getUser()
  if (!user) return redirect('/login') // ✅ Blocks unauthenticated
}

// Layer 2: Server Action (Authorization + Validation)
'use server'
export async function deleteProject(id: string) {
  const user = await getUser() // ✅ Double-check auth

  const project = await getProject(id)
  if (project.userId !== user.id) throw new Error() // ✅ Check ownership

  // ✅ Business logic check
  if (project.hasActiveSubscription) throw new Error()

  await supabase.from('projects').delete().eq('id', id)
}

// Layer 3: RLS Policy (Database)
CREATE POLICY "delete_own_projects" ON projects FOR DELETE
USING (auth.uid() = user_id); // ✅ Final protection at DB level
```

**If one layer fails, others protect you**

---

# Defense in Depth: Real Example

**What if you forget to check authorization?**

```typescript
// ❌ Forgot to check ownership!
'use server'
export async function deleteProject(id: string) {
  const user = await getUser()
  if (!user) throw new Error('Not authenticated')

  // Bug: Missing ownership check!
  await supabase.from('projects').delete().eq('id', id)
}
```

<v-clicks>

**RLS saves you:**
```sql
CREATE POLICY "delete_own_projects" ON projects FOR DELETE
USING (auth.uid() = user_id);
```

Even with the bug, users can only delete their own projects because RLS blocks unauthorized deletes at the database level!

**This is why we need multiple layers**

</v-clicks>

---
layout: center
---

# Security Agents
## Automated verification

---

# Security Agents: Your Safety Net

**20 specialized agents that check your code**

<v-clicks>

## What they do:
- Read your codebase
- Check for security issues
- Verify best practices
- Read `docs/authorization_rules.md`
- Write detailed reports

## How to use:
```bash
@auth-security-auditor
@rls-coverage-checker
@input-validation-checker
```

## Output:
- `security-audit/auth-security-report.md`
- Lists of issues found
- Specific file:line references
- Recommendations for fixes

</v-clicks>

---

# Security Agents: Coverage

| Category | Agents |
|----------|--------|
| **Authentication** | auth-security-auditor, api-auth-checker |
| **Authorization** | api-authorization-checker, rls-coverage-checker, backend-authorization-checker |
| **Input Security** | input-validation-checker, database-query-checker, url-validation-checker |
| **Output Security** | output-sanitization-checker, logging-exposure-checker |
| **Secrets** | client-secrets-checker |
| **Server/Client** | business-logic-checker, triggerdotdev-security-checker |
| **Web Security** | browser-security-checker, rate-limiting-checker, webhook-security-checker |
| **File/Email** | file-upload-checker, email-security-checker |
| **Dependencies** | dependency-security-checker |

**Run them before every release!**

---
layout: center
---

# Quick Reference

---

# Security Checklist

Before deploying, ask yourself:

<v-clicks>

1. **Authentication**: Did I use `getUser()` not `getSession()`?
2. **Authorization**: Did I check both authentication AND ownership?
3. **RLS**: Do all tables have RLS policies?
4. **Input Validation**: Did I validate all user inputs with Zod?
5. **Output Sanitization**: Did I avoid `dangerouslySetInnerHTML` or use DOMPurify?
6. **Secrets**: Are all secrets server-side only?
7. **Server/Client**: Is sensitive logic server-side only?

**Run security agents to verify:**
```bash
@auth-security-auditor
@api-authorization-checker
@input-validation-checker
```

</v-clicks>

---
layout: center
class: text-center
---

# Summary

<v-clicks>

**Authentication** → Who are you? (getUser())

**Authorization** → What can you do? (check ownership + role)

**RLS** → Database firewall (always enabled)

**Input Validation** → Trust nothing (use Zod)

**Input Sanitization** → Clean data server-side (DOMPurify for HTML)

**Output Sanitization** → Safe display (avoid dangerouslySetInnerHTML)

**Secrets** → Server-side only (never NEXT_PUBLIC_ for secrets)

**Server vs Client** → Sensitive = Server (biggest mindset shift)

</v-clicks>

---
layout: center
class: text-center
---

# Questions?

Read the full documentation:
- `CLAUDE.MD` - Development guidelines
- `docs/authorization_rules.md` - Authorization rules
- `.claude/agents/README.md` - Security agents guide

Run security agents with: `@[agent-name]`
