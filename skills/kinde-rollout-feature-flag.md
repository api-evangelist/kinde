---
name: kinde-rollout-feature-flag
description: Create a Kinde feature flag and override its value for one organization, then verify the override.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - CreateFeatureFlag
  - UpdateOrganizationFeatureFlagOverride
  - GetOrganizationFeatureFlags
scopes:
  - create:feature_flags
  - update:organizations
  - read:feature_flags
mirrors: arazzo/kinde-rollout-feature-flag-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-rollout-feature-flag-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Roll a feature flag out to one organization

Kinde flags are defined once per environment and then overridden per organization or per user. This is the staged-rollout pattern: default off everywhere, on for one customer.

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

### 1. Create the flag — `CreateFeatureFlag`

`POST /api/v1/feature_flags`

Send `name`, `key`, `type` and the DEFAULT value. Set the default to the safe state — usually off. Every organization inherits it until explicitly overridden, so a default of "on" ships the feature to everyone the moment the flag exists.

The `key` appears in tokens and in SDK lookups. It is part of your application's contract; choose it once.

### 2. Override for the organization — `UpdateOrganizationFeatureFlagOverride`

`PATCH /api/v1/organizations/{org_code}/feature_flags/{feature_flag_key}`

Set the value for this one organization. Everyone else stays on the default.

### 3. Verify — `GetOrganizationFeatureFlags`

`GET /api/v1/organizations/{org_code}/feature_flags`

Read back the organization's resolved flags and confirm both the override AND that you have not disturbed any other flag.

## Reversing a rollout

`DeleteOrganizationFeatureFlagOverride` removes the override and the organization falls back to the environment default. `DeleteEnvironementFeatureFlagOverride` (note the spelling in the contract — it is misspelled in the operationId) does the same at environment level. Both are immediate and unbounded in time, which makes a flag rollout one of the genuinely safe, reversible things an agent can do in Kinde.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| Flag not visible to the app | Token was issued before the change | Flags resolve into tokens; refresh the user's token or call `refreshUserClaims` |
| 404 on step 2 | Flag key mismatch | Keys are case-sensitive; read them back with `getFeatureFlags` |
| 403 on step 2 | Missing `update:organizations` | The override is an organization write, not a flag write |
