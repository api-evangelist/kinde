---
name: kinde-create-role-with-permission
description: Define a Kinde permission, create a role, and bind the permission to the role.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - CreatePermission
  - GetPermissions
  - CreateRole
  - UpdateRolePermissions
scopes:
  - create:permissions
  - read:permissions
  - create:roles
  - update:roles
mirrors: arazzo/kinde-create-role-with-permission-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-create-role-with-permission-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Define a Kinde role and the permission it carries

Roles and permissions are defined ONCE at the environment level and then bound to users per organization. This skill builds the environment-level definitions.

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

### 1. Create the permission — `CreatePermission`

`POST /api/v1/permissions`

Send `name`, `description` and a stable `key`. The key is what appears in the user's token at runtime, so treat it as a public API of your own product: changing it later breaks every authorization check your application performs.

### 2. Resolve the permission id — `GetPermissions`

`GET /api/v1/permissions`

`CreatePermission` does not reliably return the new id, so read the list and match on your key.

### 3. Create the role — `CreateRole`

`POST /api/v1/roles`

Send `name`, `description` and `key`. Set `is_default_role` only if every new organization member should receive it automatically — that is a broad grant and easy to forget.

### 4. Bind permission to role — `UpdateRolePermissions`

`PATCH /api/v1/roles/{role_id}/permissions`

This is the step that makes the role mean something. A role with no permissions is silently useless.

## Note for agents working over MCP

`CreateRole` and `CreatePermission` are both reachable through the Kinde MCP server. `UpdateRolePermissions` is **not** — the MCP server is granted no `update:` scopes at all. An agent confined to MCP can create a role and a permission but cannot connect them, and must hand step 4 to a REST caller.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| 409 on step 1 or 3 | The key already exists | Treat as success-if-exists and continue to the read |
| 403 on step 4 | Missing `update:roles` | Grant it; it is distinct from `create:roles` |
