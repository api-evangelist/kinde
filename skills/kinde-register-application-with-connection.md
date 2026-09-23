---
name: kinde-register-application-with-connection
description: Register a Kinde application, set its callback URLs, and attach an authentication connection.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - createApplication
  - addRedirectCallbackURLs
  - CreateConnection
  - EnableConnection
scopes:
  - create:applications
  - update:applications
  - create:connections
mirrors: arazzo/kinde-register-application-with-connection-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-register-application-with-connection-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Register an application and give it a way to sign people in

An application in Kinde is an OAuth client. On its own it cannot authenticate anyone — it needs callback URLs and at least one enabled connection.

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

### 1. Create the application — `createApplication`

`POST /api/v1/applications`

Choose `type` deliberately, because it decides the security model and cannot be casually changed later:

- **Front-end / SPA** — public client, PKCE, no client secret.
- **Back-end web** — confidential client, holds a client secret.
- **Machine to machine (M2M)** — client credentials, no user, the type used for Management API access.

The response carries the client id and, for confidential types, the client secret. The secret is shown once.

### 2. Set callback URLs — `addRedirectCallbackURLs`

`POST /api/v1/applications/{app_id}/auth_redirect_urls`

Register every redirect URI the app will use. Kinde requires HTTPS; only a localhost host may use plain HTTP. A missing or mismatched redirect URI is the cause behind platform error codes 1656 and 1829.

Set logout URLs too, via `addLogoutRedirectURLs`.

### 3. Create the connection — `CreateConnection`

`POST /api/v1/connections`

A connection is an authentication method: a social provider, a custom OAuth 2.0 provider, or a SAML / WS-Fed enterprise connection.

For social connections, always supply YOUR OWN client id and secret from the provider. If you let Kinde proxy its own credentials, your traffic shares Kinde's quota with every other customer and you will be rate limited. For Apple specifically, starting on Kinde's app permanently binds users to the wrong app and they cannot be transferred later.

### 4. Enable it on the application — `EnableConnection`

`POST /api/v1/applications/{app_id}/connections/{connection_id}`

Creating a connection does not attach it. This step is what makes the sign-in button appear.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| "State not found" at sign-in | Domain or env-var mismatch, often a trailing space | Compare the configured domain against the environment exactly |
| Error code 1656 | Redirect URI not https, or missing nonce on implicit flow | Fix the redirect URI registered in step 2 |
| Error code 4179 | Expired secret at the social provider | Rotate the provider secret and update the connection |
| Users rate limited at sign-in | Kinde proxy credentials in use | Supply your own provider app keys (step 3) |
