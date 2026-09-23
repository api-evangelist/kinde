---
name: kinde-create-organization-with-users
description: Create a Kinde organization and seed it with its initial members in one pass.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - createOrganization
  - AddOrganizationUsers
  - GetOrganizationUsers
scopes:
  - create:organizations
  - create:organization_users
  - read:organization_users
mirrors: arazzo/kinde-create-organization-with-users-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-create-organization-with-users-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Create a Kinde organization and seed its members

The B2B tenant-creation flow. An organization is Kinde's customer boundary — branding, auth policy, feature-flag overrides and billing all attach to it.

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

### 1. Create the organization — `createOrganization`

`POST /api/v1/organization`

The response returns the `org_code` (shape `org_<hex>`). **Capture it.** It is the key for every nested operation in Kinde's model.

Set `external_id` if you have your own tenant identifier — same reasoning as `provided_id` on users.

### 2. Add the initial members — `AddOrganizationUsers`

`POST /api/v1/organizations/{org_code}/users`

Accepts an array of users, each with an id and optional `roles` (role keys) and `permissions`. The bulk cap is **100 objects per request** — chunk larger seed lists and pause between chunks. Bulk writes are the documented top cause of 429s.

Every user you add must already exist. To create and add in one motion, run the `kinde-provision-user` skill per person instead.

### 3. Verify — `GetOrganizationUsers`

`GET /api/v1/organizations/{org_code}/users`

Page with `page_size` and `next_token`, then reconcile against what you sent. A partially-applied bulk write is exactly what this step exists to catch.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| 403 on step 1 | Missing `create:organizations` | Grant the scope; note the Free tier caps monthly active organizations at 5 |
| Batch rejected on step 2 | More than 100 objects | Chunk to 100 |
| Users missing in step 3 | Partial application, or ids from another environment | Re-add only the missing ids; never replay the whole batch |
