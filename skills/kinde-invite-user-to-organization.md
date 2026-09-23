---
name: kinde-invite-user-to-organization
description: Invite a person to a Kinde organization with a pre-selected role and confirm the invitation.
api: Kinde Management API
base_url: https://{subdomain}.kinde.com
operations:
  - GetRoles
  - createOrganizationInvite
  - getOrganizationInvite
scopes:
  - read:roles
  - create:organization_users
  - read:organization_users
mirrors: arazzo/kinde-invite-user-to-organization-workflow.yml
generated: '2026-09-12'
method: generated
source: >-
  Generated from arazzo/kinde-invite-user-to-organization-workflow.yml and the operations it names, each verified present in this
  repo's refined OpenAPIs by operationId lookup. Cross-cutting rules drawn from
  conventions/kinde-conventions.yml, rate-limits/kinde-rate-limits.yml and
  errors/kinde-problem-types.yml.
---

# Invite a user to a Kinde organization

The self-service alternative to provisioning. Kinde sends the email and the person completes their own sign-up, which means they choose their own credentials and no password ever passes through your system.

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

### 1. Resolve the role — `GetRoles`

`GET /api/v1/roles`

Read the environment's roles and pick the one the invitee should receive. Match on `key`, never on display `name` — names are editable in the dashboard, keys are the contract.

### 2. Send the invitation — `createOrganizationInvite`

`POST /api/v1/organization/invites`

Include the email, the `org_code`, and the role to grant on acceptance.

Invitations are one of the places where a careless retry is visible to a human: re-sending sends a second email. Check with `getOrganizationInvite` before re-issuing.

### 3. Confirm — `getOrganizationInvite`

`GET /api/v1/organization/invites/{invite_id}`

Read back the invitation state. An unaccepted invitation is not an error — it means the human has not acted yet.

## Note

Invited users can complete sign-up even in an organization where self-sign-up is disabled. That is the point of the invitation path, and it is also why the role you choose in step 1 matters: it is granted without further review.

## Failure modes

| Symptom | Cause | Action |
|---|---|---|
| 403 on step 2 | Missing `create:organization_users` | Grant the scope |
| Invitation never arrives | Spam filtering, Google Workspace pre-delivery scanning (~4 min), Microsoft Defender quarantine | Check `getOrganizationInvite` state before resending; a resend duplicates the email |
