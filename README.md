# Kinde (kinde)

Kinde is a developer-first authentication and customer identity platform that bundles authentication (passwords, passwordless, social, enterprise SSO), authorization (roles, permissions, scopes), B2B organizations, billing, and feature flags into a single integrated product. Founded in Australia, Kinde positions itself as "the fully integrated developer platform — secure and monetize your product from day one" and is used by over 70,000 developers. The platform exposes a Management API for tenant administration and an Account API for end-user self-service flows, both backed by published OpenAPI specs and a large open-source SDK ecosystem on GitHub (TypeScript, React, Next.js, Python, Go, Java, .NET, PHP, Ruby, Elixir, Flutter, iOS, Android, Expo, React Native, SvelteKit, Nuxt, Remix, TanStack Start) plus a Go-based CLI, a Terraform provider, and a Model Context Protocol (MCP) server for AI agents.

**URL:** [https://kinde.com](https://kinde.com)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

 - Authentication, Authorization, Customer Identity, Identity Management, OAuth, OpenID Connect, Single Sign-On, Multi-Factor Authentication, Role-Based Access Control, Feature Flags, Billing, B2B, SaaS, Developer Platform

## Timestamps

- **Created:** 2026-05-22
- **Modified:** 2026-05-22

## APIs

### Kinde Management API

The Kinde Management API exposes administrative endpoints for managing a Kinde business: users, organizations, applications, APIs, scopes, roles, permissions, connections, directories, environments, environment variables, feature flags, properties, property categories, webhooks, subscribers, billing entitlements/agreements/meter usage, API keys, connected apps, and identities. The spec covers **97 paths and 169 operations across 27 tag groups**. Authentication uses an M2M (machine-to-machine) OAuth client to obtain a bearer token, and the base URL is templated as `https://{subdomain}.kinde.com`.

**Human URL:** [https://docs.kinde.com/kinde-apis/management/](https://docs.kinde.com/kinde-apis/management/)
**Base URL:** `https://{subdomain}.kinde.com/api/v1`

#### Tags

 - Authentication, Authorization, Customer Identity, User Management, Organizations, Roles, Permissions, Feature Flags, Webhooks, Billing, API Keys, Connections

#### Properties

- [Documentation](https://docs.kinde.com/kinde-apis/management/)
- [GettingStarted](https://docs.kinde.com/developer-tools/kinde-api/connect-to-kinde-api/)
- [Authentication](https://docs.kinde.com/developer-tools/kinde-api/access-token-for-api/)
- [OpenAPI](openapi/kinde-management-api-openapi.yml)
- [Canonical OpenAPI source](https://api-spec.kinde.com/kinde-management-api-spec.yaml)
- [Spectral Rules](rules/kinde-rules.yml)

#### Naftiko Capabilities

- [capabilities/kinde-users.yaml](capabilities/kinde-users.yaml)
- [capabilities/kinde-organizations.yaml](capabilities/kinde-organizations.yaml)
- [capabilities/kinde-applications.yaml](capabilities/kinde-applications.yaml)
- [capabilities/kinde-roles-permissions.yaml](capabilities/kinde-roles-permissions.yaml)
- [capabilities/kinde-feature-flags.yaml](capabilities/kinde-feature-flags.yaml)
- [capabilities/kinde-webhooks.yaml](capabilities/kinde-webhooks.yaml)
- [capabilities/kinde-billing.yaml](capabilities/kinde-billing.yaml)
- [capabilities/kinde-api-keys.yaml](capabilities/kinde-api-keys.yaml)
- [capabilities/kinde-connections.yaml](capabilities/kinde-connections.yaml)
- [capabilities/kinde-properties.yaml](capabilities/kinde-properties.yaml)

### Kinde Account API

The Kinde Account API (also documented as the Frontend API) provides endpoints for the currently signed-in user to inspect their own identity, sessions, billing entitlements, feature-flag values, organization memberships, roles, permissions, properties, and self-serve portal URLs. The spec covers **10 paths across 7 tag groups** (Billing, Feature Flags, OAuth, Permissions, Self-serve Portal, Properties, Roles). It is intended to be called from the user's own browser/app using their authenticated session token rather than a backend M2M token.

**Human URL:** [https://docs.kinde.com/kinde-apis/frontend/](https://docs.kinde.com/kinde-apis/frontend/)
**Base URL:** `https://{subdomain}.kinde.com/account_api/v1`

#### Tags

 - Account Management, Self-Service, Billing, Feature Flags, Permissions, Roles, Properties

#### Properties

- [Documentation](https://docs.kinde.com/kinde-apis/frontend/)
- [OpenAPI](openapi/kinde-frontend-api-openapi.yml)
- [Canonical OpenAPI source](https://api-spec.kinde.com/kinde-frontend-api-spec.yaml)

#### Naftiko Capabilities

- [capabilities/kinde-account.yaml](capabilities/kinde-account.yaml)

### Kinde MCP Server

The Kinde MCP (Model Context Protocol) server acts as a bridge between AI assistants and a Kinde account. It exposes a subset of the Kinde Management API as MCP tools (query organizations, check user existence by email, retrieve user roles, list users in an organization, manage users and permissions), authenticated via an environment-level API key whose scopes constrain which tools the AI client can call.

**Human URL:** [https://docs.kinde.com/mcp-server/about-mcp-server/](https://docs.kinde.com/mcp-server/about-mcp-server/)

#### Tags

 - MCP, AI Agents, Model Context Protocol, Identity

## Artifacts

### OpenAPI Specs

- [openapi/kinde-management-api-openapi.yml](openapi/kinde-management-api-openapi.yml) — Kinde Management API, 97 paths / 169 operations
- [openapi/kinde-frontend-api-openapi.yml](openapi/kinde-frontend-api-openapi.yml) — Kinde Account API, 10 paths

### JSON Schemas

- [json-schema/kinde-user-schema.json](json-schema/kinde-user-schema.json)
- [json-schema/kinde-organization-schema.json](json-schema/kinde-organization-schema.json)
- [json-schema/kinde-application-schema.json](json-schema/kinde-application-schema.json)
- [json-schema/kinde-role-schema.json](json-schema/kinde-role-schema.json)
- [json-schema/kinde-permission-schema.json](json-schema/kinde-permission-schema.json)
- [json-schema/kinde-feature-flag-schema.json](json-schema/kinde-feature-flag-schema.json)
- [json-schema/kinde-webhook-schema.json](json-schema/kinde-webhook-schema.json)

### JSON Structures

- [json-structure/kinde-user-structure.json](json-structure/kinde-user-structure.json)
- [json-structure/kinde-organization-structure.json](json-structure/kinde-organization-structure.json)

### JSON-LD Context

- [json-ld/kinde-context.jsonld](json-ld/kinde-context.jsonld)

### Examples

- [examples/kinde-create-user-example.json](examples/kinde-create-user-example.json)
- [examples/kinde-create-organization-example.json](examples/kinde-create-organization-example.json)
- [examples/kinde-create-application-example.json](examples/kinde-create-application-example.json)
- [examples/kinde-create-role-example.json](examples/kinde-create-role-example.json)
- [examples/kinde-create-feature-flag-example.json](examples/kinde-create-feature-flag-example.json)
- [examples/kinde-create-webhook-example.json](examples/kinde-create-webhook-example.json)

### Vocabulary

- [vocabulary/kinde-vocabulary.yml](vocabulary/kinde-vocabulary.yml)

### Plans, Rate Limits, and FinOps

- [plans/kinde-plans-pricing.yml](plans/kinde-plans-pricing.yml) — API Commons Plans 0.1
- [rate-limits/kinde-rate-limits.yml](rate-limits/kinde-rate-limits.yml) — API Commons Rate Limits 0.1
- [finops/kinde-finops.yml](finops/kinde-finops.yml) — FOCUS-aligned FinOps profile

### Spectral Rules

- [rules/kinde-rules.yml](rules/kinde-rules.yml)

## SDKs and Tooling (from `github.com/kinde-oss`)

**Frontend & SPA:** kinde-auth-pkce-js, kinde-auth-react, kinde-auth-nextjs, kinde-typescript-sdk, nuxt-kinde, kinde-sveltekit-sdk, kinde-tsr (TanStack Start), kinde-remix-sdk, kinde-auth-remix-sdk
**Backend / Node:** kinde-nodejs-sdk, kinde-node-express, kinde-node-express-api, kinde-node, kinde-node-auth-utils
**Other languages:** kinde-python-sdk, kinde-go, kinde-java-sdk, kinde-dotnet-sdk, kinde-php-sdk, kinde-ruby-sdk, kinde-elixir-sdk, kinde-auth-wordpress
**Native / Mobile:** kinde-sdk-ios, kinde-sdk-android, kinde-flutter-sdk, expo, kinde-react-native-sdk-0-7x (and 0-6x / 0-5x variants)
**CLI / IaC:** kinde-cli (Go), homebrew-kinde-cli, scoop-kinde-cli, terraform-provider-kinde
**Tools / Utilities:** management-api-js, jwt-validator, jwt-decoder, js-utils, webhook (event-decoder), kinde-translations, infrastructure (workflow typings), workflows-runtime
**Integrations:** kinde-convex-sync, kinde-convex-billing

## Common Properties

- [Portal](https://docs.kinde.com)
- [Sign In](https://app.kinde.com/admin)
- [Sign Up](https://app.kinde.com/register)
- [Pricing](https://kinde.com/pricing/)
- [Blog](https://kinde.com/blog/)
- [Status Page](https://status.kinde.com) — [RSS](https://status.kinde.com/history.rss) / [Atom](https://status.kinde.com/history.atom)
- [Changelog](https://updates.kinde.com)
- [Roadmap](https://updates.kinde.com/board)
- [GitHub](https://github.com/kinde-oss) — 58 public repos
- [Terms of Service](https://docs.kinde.com/trust-center/agreements/terms-of-service/)
- [Trust Center](https://docs.kinde.com/trust-center/)
- [Support](mailto:support@kinde.com)

## Notable Findings

- **Single integrated platform.** Kinde explicitly bundles auth + billing + feature flags + B2B organizations as one product, rather than asking developers to stitch together auth + Stripe + LaunchDarkly.
- **Two clear API surfaces.** A large Management API (97 paths, 27 tags) for backend administration and a small Account API (10 paths) for the signed-in user's own session/entitlements/portal-link.
- **Modern AI integration.** A native MCP server exposes a curated subset of the Management API as scoped tools for AI agents.
- **Wide SDK coverage.** 58 public repos in `kinde-oss`, including generated SDKs for nearly every major web and mobile stack plus a Go CLI and Terraform provider.
- **Transparent self-serve pricing.** Four published tiers ($0 / $25 / $75 / $250) with explicit Free-tier MAU/MAO/feature-flag ceilings.

## Notable Absences

- The `https://kinde.com/api` and `https://kinde.com/changelog` direct paths return 404 — Kinde uses `docs.kinde.com/kinde-apis/...` and `updates.kinde.com` instead.
- The blog and the changelog page do not appear to advertise an RSS feed (only the status page does).
- Per-endpoint rate limits are not published in a structured form; Kinde describes "attack protection" / DoS mitigation qualitatively rather than as a documented limit table.
- No public AsyncAPI spec for the webhook event surface, even though webhook events are a first-class part of the product.
