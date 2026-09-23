---
name: kinde-end-user-self-serve-portal
description: Read a signed-in user profile, roles and billing entitlements, then hand them a self-serve portal link.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - getUserProfileV2
  - GetUserRoles
  - GetEntitlements
  - GetPortalLink
scopes:
  - openid
  - profile
  - email
mirrors: arazzo/kinde-end-user-self-serve-portal-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-end-user-self-serve-portal-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Show a signed-in user their account and hand off to the portal

This is the ONE flow in this skill pack that runs on the **Account API**, not the Management API — it acts as the signed-in user, not as an administrator.

## Conventions for this flow

- **Base URL** `https://{subdomain}.kinde.com`, paths under `/account_api/v1/` and `/oauth2/`.
- **Auth** is the END USER'S access token, obtained from their sign-in session (`getToken` in any Kinde SDK). Do NOT use an M2M token here; the Account API resolves everything relative to the token's subject.
- The Account API exposes 10 operations and operates only on the authenticated user. There is no way to read another user through it — which is exactly the property that makes it safe to call from a user-facing surface.
- Errors and pagination follow the same conventions as the Management API. There is still no idempotency, but this flow is read-only up to the final step.

## Steps

### 1. Read the profile — `getUserProfileV2`

`GET /oauth2/v2/user_profile`

Returns the authenticated user's profile claims. This is the OIDC userinfo endpoint advertised in Kinde's discovery document.

### 2. Read their roles — `GetUserRoles`

Roles resolve per organization. If your product is multi-tenant, you must know WHICH organization the user is currently acting in before this answer means anything.

### 3. Read entitlements — `GetEntitlements`

`GET /account_api/v1/entitlements`

Returns the feature access the user's current plan grants. Use this — not a cached copy of their plan name — to gate features. Kinde's own guidance is to keep cache durations short for entitlement-sensitive decisions, and to subscribe to billing webhooks so access stays in step with plan changes.

### 4. Generate the portal link — `GetPortalLink`

`GET /account_api/v1/portal_link`

Returns a URL. Redirect the user to it.

Optional parameters:
- `return_url` — where to send the user when they leave the portal.
- `subnav` — which portal section to open.

React and Next.js apps can skip the manual fetch and use the `PortalLink` component, which generates the link and redirects in one step.

## What the user can do once they are there

Profile and authentication-method management, and — if you enabled plan management and the user holds a billing role — viewing and **cancelling** their subscription. Cancellation syncs to Stripe, and whether they are billed for unpaid usage or credited for unused days depends on the billing policies YOU configured. Kinde states no default, so know your own policy before you expose this.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| 401 on any step | An M2M token was used instead of a user token | Use the signed-in user's access token |
| Entitlements look stale | Cached plan state | Re-read entitlements; subscribe to billing webhooks |
| Roles look wrong | Reading roles without an organization context | Resolve the user's active organization first |
