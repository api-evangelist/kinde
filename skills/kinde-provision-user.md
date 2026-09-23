---
name: kinde-provision-user
description: Create a Kinde user and onboard them into an organization with roles, then verify the grant took effect.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - createUser
  - AddOrganizationUsers
  - GetOrganizationUserRoles
scopes:
  - create:users
  - create:organization_users
  - read:organization_users
mirrors: arazzo/kinde-provision-user-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-provision-user-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Provision a user into a Kinde organization

The canonical onboarding flow: create the person, put them in the right organization with the right roles, then read back to confirm.

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

### 1. Create the user — `createUser`

`POST /api/v1/user`

Supply `profile.given_name`, `profile.family_name` and an `identities` array containing an email identity. The response carries the Kinde user `id` (shape `kp:<32 hex>`) that every following step needs.

If you hold an id for this person in your own system, set `provided_id` now. It is the supported way to correlate a Kinde user back to your database later, and retrofitting it is expensive.

**Do not retry this call on a timeout.** Call `searchUsers` or `getUsers` filtered by email first; a duplicate user is not automatically merged.

### 2. Add the user to the organization — `AddOrganizationUsers`

`POST /api/v1/organizations/{org_code}/users`

Send the user id and the `roles` array of role KEYS (not role ids) in the same call. Assigning roles here saves a round trip against `CreateOrganizationUserRole`.

Roles resolve per organization. The same user can hold different roles in different organizations, so "does this user have the admin role" is never a global question.

### 3. Verify — `GetOrganizationUserRoles`

`GET /api/v1/organizations/{org_code}/users/{user_id}/roles`

Treat this read as mandatory. Step 2 accepting your request is not evidence that the role keys you sent actually exist in this environment.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| 403 on step 1 | Missing `create:users` | Add the scope to the M2M application and re-issue the token |
| 404 on step 2 | `org_code` belongs to another environment | Confirm which environment issued the token |
| Step 3 returns fewer roles than sent | A role key does not exist here | `GetRoles` to list valid keys, create the role, re-assign |
| 429 | Rate or concurrency limiter | Back off on `RateLimit-Reset`; do NOT re-send step 1 |
