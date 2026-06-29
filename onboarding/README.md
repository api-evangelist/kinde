# Programmatic API Onboarding — Kinde

A single-file, zero-dependency Node.js (18+) CLI that reproduces SoundCloud's
`sc-api-auth.mjs` pattern for Kinde: register an application / obtain credentials
programmatically instead of clicking through a dashboard, so agents and developers
can onboard at the command line.

- Script: [`kinde-api-auth.mjs`](kinde-api-auth.mjs)
- Run `node kinde-api-auth.mjs --help` for usage and the required environment variables.
- Story / rationale: https://apievangelist.com/2026/08/07/kinde-plumbing-skips-front-door/

Part of the API Evangelist "Programmatic API Onboarding for the Agentic Moment" series.
