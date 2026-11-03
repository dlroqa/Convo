# Architecture decisions

Decisions that govern the planned work on this fork. The Phase 0 assessment is the source for the
background; this file records what has been decided so later pull requests can be checked against it.

Status values: **Accepted** (binding for new work), **Proposed** (not yet approved).

## Ground rules

| ID | Decision | Status |
|---|---|---|
| AD-01 | The existing GUI (appearance, layout, navigation, behaviour) and all local inference functions (image, chat, speech, voice) must keep working. New features are added as new settings sections or panels, never by redesigning existing screens. | Accepted |
| AD-02 | All building, testing, rendering and packaging runs on GitHub-hosted Actions runners. | Accepted |
| AD-03 | Work lands in small sequential pull requests, each approved before the next one starts. Nothing is merged, tagged, released or deployed without explicit approval. | Accepted |

## Data ownership

| ID | Decision | Status |
|---|---|---|
| AD-10 | Cloudflare (Workers API with D1 and R2) is the authoritative store for future cloud-owned profiles. | Accepted |
| AD-11 | Local SQLite is authoritative only for local-only profiles. For profiles connected to a cloud account it is a controlled offline cache that synchronises with Cloudflare and never overrides it. | Accepted |
| AD-12 | A local-only profile always exists and needs no account, network or Cloudflare service. | Accepted |

## Desktop application

| ID | Decision | Status |
|---|---|---|
| AD-20 | The native desktop application must run fully offline once the local engines and models have been provisioned. | Accepted |
| AD-21 | Communication between the desktop shell and the local server sidecar must enforce authentication (a per-launch secret), origin restrictions (only the application's own origin), and process ownership (the shell only supervises and stops processes it started). | Accepted |
| AD-22 | Desktop shell: Tauri 2 running the existing Node.js server as a sidecar. | Proposed |

## Tools and integrations

| ID | Decision | Status |
|---|---|---|
| AD-30 | The Tool & Integration Manager must be backed by a real versioned tool registry, permission checks enforced at the moment each tool call runs, and an agent tool-calling loop on the server. A settings screen alone does not meet this. | Accepted |
| AD-31 | Disabling an integration must stop any further execution of it, including calls already queued and processes it started. | Accepted |
| AD-32 | Installing, updating or removing a third-party tool must never modify the core harness automatically. Tools live in their own directories and registry entries, and are loaded through adapters. | Accepted |

## Known issues to address first

The Phase 0 assessment found these in the current code. PR1 is planned to fix them.

- The local server sends `Access-Control-Allow-Origin: *` on every response and has no authentication
  or Origin/Host check, so any website open in the browser can call its API
  (`scripts/server/serve.cjs`, `json()` and the OPTIONS handler).
- The static file handler joins the raw request path onto `app/dist` without checking that the result
  stays inside it (`scripts/server/serve.cjs`, "Static frontend files").
- Model downloads accept any http(s) host, including private network addresses.
- The launchers force-kill whatever process is listening on the backend ports.

## Pull request sequence

| PR | Scope | Status |
|---|---|---|
| PR0 | Baseline GitHub Actions CI and this document. No product changes. | In review |
| PR1 | Local server security hardening with tests. | Not started |
| Later | GUI baselines, provider layer, Bedrock, desktop shell, SQLite, tool registry, MCP, Cloudflare interfaces. | Not started |
