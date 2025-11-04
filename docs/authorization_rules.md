# Authorization Rules

> **Purpose**: Simple guide for ai agents to understand who can access what in this application.

---

## User Roles

- **[role_name]**: Brief description
- **[role_name]**: Brief description
- **[role_name]**: Brief description

**Example:**
- **admin**: Full system access
- **user**: Standard user with own data only
- **guest**: Temporary user, limited access

---

## Data Isolation

Simple statement of your isolation rules:

**Example - User Isolation:**
- Each user can only access their own data
- No user can see another user's data

**Example - Tenant Isolation:**
- Each organizations data is completely separate
- Users can only access data from inside the org they belong to
- No cross-org data access

## Important Rules

Any special rules or exceptions:

**Example:**
- Manager can make other staff members managers OR only Admins can change a user role. No one else.

Keep it simple and focused on business rules, not implementation details.
