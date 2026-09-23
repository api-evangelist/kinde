---
name: kinde-migrate-user-with-password
description: Migrate a user from another identity provider into Kinde, carrying their existing password hash.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - createUser
  - SetUserPassword
  - getUserData
scopes:
  - create:users
  - update:users
  - read:users
mirrors: arazzo/kinde-migrate-user-with-password-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-migrate-user-with-password-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Migrate a user into Kinde with their existing password

The cutover flow. Done correctly the user never notices: they sign in to your product after the migration with exactly the credentials they had before it.

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

Carry across the email identity and set `provided_id` to the user's primary key in the SYSTEM YOU ARE LEAVING. During a migration this is the only thing that lets you reconcile the two populations afterwards, and you will want it when the cutover is half done.

### 2. Set the password — `SetUserPassword`

`PUT /api/v1/users/{user_id}/password`

Kinde accepts a pre-hashed password with its hashing parameters, so the plaintext never exists anywhere in the migration path. Supply the hash, the algorithm and the salt exactly as your previous provider stored them.

If the hash is not transferable, omit this step and move the user to a passwordless or reset-on-first-login flow instead — do not invent a password.

### 3. Verify — `getUserData`

`GET /api/v1/user`

Read the user back and confirm identities and profile landed.

## Migration-scale guidance

- The bulk write cap is **100 objects per request**; the page read cap is **500**.
- For thousands of users, Kinde explicitly directs you to CSV bulk import rather than this API path.
- Batch, and add delays between batches. Migrations are the single most common cause of 429 in Kinde's own documentation.
- Because there is no idempotency, a retried batch creates duplicate users. Reconcile on `provided_id` between batches rather than replaying.
- Run a final re-sync after cutover to catch users who changed their password during the window.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| Step 2 rejected | Unsupported hashing algorithm | Fall back to a reset-on-first-login flow; never store a plaintext password to work around this |
| Duplicate users appear | A retried create | Reconcile on `provided_id`; delete the duplicate, remembering deletion has no undo |
