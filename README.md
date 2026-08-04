# Kinde (kinde)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Kinde is a developer-first authentication and customer identity platform that bundles authentication (passwords, passwordless, social, enterprise SSO), authorization (roles, permissions, scopes), B2B organizations, billing, and feature flags into a single integrated product. Founded in Australia, Kinde positions itself as "the fully integrated developer platform — secure and monetize your product from day one" and is used by over 70,000 developers. The platform exposes a Management API for tenant administration and an Account API for end-user self-service flows, both backed by published OpenAPI specs and a large open-source SDK ecosystem on GitHub (TypeScript, React, Next.js, Python, Go, Java, .NET, PHP, Ruby, Elixir, Flutter, iOS, Android, Expo, React Native, SvelteKit, Nuxt, Remix, TanStack Start) plus a Go-based CLI, a Terraform provider, and a Model Context Protocol (MCP) server for AI agents.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/kinde/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/kinde/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- Authentication
- Authorization
- Customer Identity
- Identity Management
- OAuth
- OpenID Connect
- Single Sign-On
- Multi-Factor Authentication
- Role-Based Access Control
- Feature Flags
- Billing
- B2B
- SaaS
- Developer Platform

## Timestamps

- **Created:** 2026-05-22
- **Modified:** 2026-05-22

## APIs

### Kinde Management API

The Kinde Management API exposes administrative endpoints for managing a Kinde business: users, organizations, applications, APIs, scopes, roles, permissions, connections, directories, environments, environment variables, feature flags, properties, property categories, webhooks, subscribers, billing entitlements/agreements/meter usage, API keys, connected apps, and identities. The spec covers 97 paths and 169 operations across 27 tag groups. Authentication uses an M2M (machine-to-machine) OAuth client to obtain a bearer token, and the base URL is templated as ``https://{subdomain}.kinde.com``.

- **Human URL:** [https://docs.kinde.com/kinde-apis/management/](https://docs.kinde.com/kinde-apis/management/)
- **Base URL:** `https://{subdomain}.kinde.com/api/v1`

#### Tags

- Authentication
- Authorization
- Customer Identity
- User Management
- Organizations
- Roles
- Permissions
- Feature Flags
- Webhooks
- Billing
- API Keys
- Connections

#### Properties

- [Documentation](https://docs.kinde.com/kinde-apis/management/)
- [Getting Started](https://docs.kinde.com/developer-tools/kinde-api/connect-to-kinde-api/)
- [Authentication](https://docs.kinde.com/developer-tools/kinde-api/access-token-for-api/)
- [OpenAPI](openapi/kinde-management-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Open Collection](collections/kinde-management-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Open A P I Canonical](https://api-spec.kinde.com/kinde-management-api-spec.yaml)
- [Spectral Rules](rules/kinde-rules.yml)
- [JSON Schema](json-schema/kinde-user-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-organization-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-application-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-role-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-permission-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-feature-flag-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/kinde-webhook-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Structure](json-structure/kinde-user-structure.json)
- [JSON Structure](json-structure/kinde-organization-structure.json)
- [Examples](examples/kinde-create-user-example.json)
- [Examples](examples/kinde-create-organization-example.json)
- [Examples](examples/kinde-create-application-example.json)
- [Examples](examples/kinde-create-role-example.json)
- [Examples](examples/kinde-create-feature-flag-example.json)
- [Examples](examples/kinde-create-webhook-example.json)

### Kinde Account API

The Kinde Account API (also documented as the Frontend API) provides endpoints for the currently signed-in user to inspect their own identity, sessions, billing entitlements, feature-flag values, organization memberships, roles, permissions, properties, and self-serve portal URLs. The spec covers 10 paths across 7 tag groups (Billing, Feature Flags, OAuth, Permissions, Self-serve Portal, Properties, Roles). It is intended to be called from the user's own browser/app using their authenticated session token rather than a backend M2M token.

- **Human URL:** [https://docs.kinde.com/kinde-apis/frontend/](https://docs.kinde.com/kinde-apis/frontend/)
- **Base URL:** `https://{subdomain}.kinde.com/account_api/v1`

#### Tags

- Account Management
- Self-Service
- Billing
- Feature Flags
- Permissions
- Roles
- Properties

#### Properties

- [Documentation](https://docs.kinde.com/kinde-apis/frontend/)
- [OpenAPI](openapi/kinde-frontend-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Open Collection](collections/kinde-frontend-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Open A P I Canonical](https://api-spec.kinde.com/kinde-frontend-api-spec.yaml)

### Kinde MCP Server

The Kinde MCP (Model Context Protocol) server acts as a bridge between AI assistants and a Kinde account. It exposes a subset of the Kinde Management API as MCP tools (query organizations, check user existence by email, retrieve user roles, list users in an organization, manage users and permissions), authenticated via an environment-level API key whose scopes constrain which tools the AI client can call.

- **Human URL:** [https://docs.kinde.com/mcp-server/about-mcp-server/](https://docs.kinde.com/mcp-server/about-mcp-server/)

#### Tags

- MCP
- AI Agents
- Model Context Protocol
- Identity

#### Properties

- [Documentation](https://docs.kinde.com/mcp-server/about-mcp-server/)

## Common Properties

- [Arazzo Workflows](arazzo/) — [Arazzo Specification](https://spec.openapis.org/arazzo/latest.html)
- [Portal](https://docs.kinde.com)
- [Sign In](https://app.kinde.com/admin)
- [Sign Up](https://app.kinde.com/register)
- [Pricing](https://kinde.com/pricing/)
- [Blog](https://kinde.com/blog/)
- [Status Page](https://status.kinde.com)
- [Status Page R S S](https://status.kinde.com/history.rss)
- [Status Page Atom](https://status.kinde.com/history.atom)
- [Changelog](https://updates.kinde.com)
- [Roadmap](https://updates.kinde.com/board)
- [Git Hub](https://github.com/kinde-oss)
- [Terms of Service](https://docs.kinde.com/trust-center/agreements/terms-of-service/)
- [Trust Center](https://docs.kinde.com/trust-center/)
- [Support](mailto:support@kinde.com)
- [Contact Sales](https://kinde.com/contact-us/)
- [Plans](plans/kinde-plans-pricing.yml)
- [Rate Limits](rate-limits/kinde-rate-limits.yml)
- [Fin Ops](finops/kinde-finops.yml)
- [Vocabulary](vocabulary/kinde-vocabulary.yml)
- [JSON-LD](json-ld/kinde-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [SDK](https://github.com/kinde-oss/kinde-typescript-sdk)
- [SDK](https://github.com/kinde-oss/kinde-auth-nextjs)
- [SDK](https://github.com/kinde-oss/kinde-auth-react)
- [SDK](https://github.com/kinde-oss/kinde-auth-pkce-js)
- [SDK](https://github.com/kinde-oss/kinde-nodejs-sdk)
- [SDK](https://github.com/kinde-oss/kinde-node-express)
- [SDK](https://github.com/kinde-oss/kinde-node-express-api)
- [SDK](https://github.com/kinde-oss/kinde-remix-sdk)
- [SDK](https://github.com/kinde-oss/kinde-auth-remix-sdk)
- [SDK](https://github.com/kinde-oss/kinde-sveltekit-sdk)
- [SDK](https://github.com/kinde-oss/nuxt-kinde)
- [SDK](https://github.com/kinde-oss/kinde-tsr)
- [SDK](https://github.com/kinde-oss/kinde-python-sdk)
- [SDK](https://github.com/kinde-oss/kinde-go)
- [SDK](https://github.com/kinde-oss/kinde-java-sdk)
- [SDK](https://github.com/kinde-oss/kinde-dotnet-sdk)
- [SDK](https://github.com/kinde-oss/kinde-php-sdk)
- [SDK](https://github.com/kinde-oss/kinde-ruby-sdk)
- [SDK](https://github.com/kinde-oss/kinde-elixir-sdk)
- [SDK](https://github.com/kinde-oss/kinde-auth-wordpress)
- [SDK](https://github.com/kinde-oss/kinde-flutter-sdk)
- [SDK](https://github.com/kinde-oss/kinde-sdk-ios)
- [SDK](https://github.com/kinde-oss/kinde-sdk-android)
- [SDK](https://github.com/kinde-oss/expo)
- [SDK](https://github.com/kinde-oss/kinde-react-native-sdk-0-7x)
- [SDK](https://github.com/kinde-oss/management-api-js)
- [C L I](https://github.com/kinde-oss/kinde-cli)
- [C L I](https://github.com/kinde-oss/homebrew-kinde-cli)
- [C L I](https://github.com/kinde-oss/scoop-kinde-cli)
- [Tools](https://github.com/kinde-oss/jwt-validator)
- [Tools](https://github.com/kinde-oss/jwt-decoder)
- [Tools](https://github.com/kinde-oss/js-utils)
- [Tools](https://github.com/kinde-oss/webhook)
- [Tools](https://github.com/kinde-oss/kinde-translations)
- [Tools](https://github.com/kinde-oss/infrastructure)
- [Tools](https://github.com/kinde-oss/workflows-runtime)
- [Tools](https://github.com/kinde-oss/terraform-provider-kinde)
- [Tools](https://github.com/kinde-oss/kinde-convex-sync)
- [Tools](https://github.com/kinde-oss/kinde-convex-billing)
- [Documentation](https://github.com/kinde-oss/documentation)
- [L L Ms Txt](https://docs.kinde.com/llms.txt)

## Maintainers

**FN:** Kin Lane
**Email:** info@apievangelist.com
