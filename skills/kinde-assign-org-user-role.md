---
name: kinde-assign-org-user-role
description: Find an existing Kinde user and grant them a role inside a specific organization.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - searchUsers
  - CreateOrganizationUserRole
  - GetOrganizationUserRoles
scopes:
  - read:users
  - create:organization_user_roles
  - read:organization_users
mirrors: arazzo/kinde-assign-org-user-role-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-assign-org-user-role-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Grant an existing user a role in an organization

Use this when the person already exists and only their authorization inside one organization needs to change.

## Conventions that apply to every step

- **Base URL** `https://{subdomain}.kinde.com` — `{subdomain}` is the caller's own Kinde business subdomain. There is no shared host, and an id from one Kinde environment does not exist in another.
- **Auth** OAuth 2.0 client credentials against `https://{subdomain}.kinde.com/oauth2/token` with `audience=https://{subdomain}.kinde.com/api`. Present the JWT as `Authorization: Bearer <token>`. Request only the scopes this skill lists by passing them in the token request's `scope` parameter.
- **403 means scope, not identity.** 168 of 169 operations declare 403 and only one declares 401, so a missing scope is far likelier than a bad token.
- **NO IDEMPOTENCY.** Kinde publishes no `Idempotency-Key` header or replay protection of any kind. Never blind-retry a POST. If a create times out, read back by the natural key before retrying, or you will create a duplicate.
- **429 handling.** Read the `RateLimit-Reset` header (seconds) and back off exponentially with jitter. `RateLimit-Limit` and `RateLimit-Remaining` are NOT returned, so you cannot pace proactively. Keep `page_size` small and avoid expansions on list calls; both consume concurrency slots.
- **Pagination** is `page_size` (max 500) plus an opaque `next_token`. Stop when `next_token` is absent.
- **Errors** are `{"errors":[{"code","message"}]}` — not RFC 9457 problem+json.
- **Reversibility.** Prefer suspension (`updateUser` with `is_suspended`) over `deleteUser`. No delete in this API has an undo, a restore, or a published retention window.

## Steps

### 1. Find the user — `searchUsers`

`GET /api/v1/search/users`

Search by email or name. Match exactly before proceeding; granting a role to the wrong user is a silent authorization defect that nothing downstream will flag.

If you stored a `provided_id` at creation time, prefer resolving through that rather than by email — email is mutable and is not a stable key.

### 2. Assign the role — `CreateOrganizationUserRole`

`POST /api/v1/organizations/{org_code}/users/{user_id}/roles`

The role is scoped to this organization only. The same user keeps whatever roles they hold elsewhere.

### 3. Verify — `GetOrganizationUserRoles`

`GET /api/v1/organizations/{org_code}/users/{user_id}/roles`

Confirm the full resulting role set, not just that the call returned 2xx.

## Reversing this

`DeleteOrganizationUserRole` removes the assignment with no time limit. This is one of the few fully reversible operations in the API — the user record and their memberships are untouched.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| Step 1 returns several matches | Duplicate users, often from an unguarded retry | Disambiguate on `provided_id` or `created_on`; do not guess |
| 404 on step 2 | The user is not a member of this organization | Add them with `AddOrganizationUsers` first |
| 403 on step 2 | Missing `create:organization_user_roles` | This scope is separate from `create:roles` |
