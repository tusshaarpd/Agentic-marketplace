# AgentHub — Agentic Marketplace Platform

An enterprise-grade AI agent marketplace (think *"AWS Marketplace, but for AI Agents"*). It shows how a company can **publish, deploy, safeguard, govern, and monetize** the rising number of agents inside its organization — built as a static demo and deployed on Vercel.

> Live demo: https://agentic-marketplace-three.vercel.app/

---

## What this demo answers

The demo is built to showcase, on LinkedIn, the three things every enterprise asks once it has more than a handful of agents:

1. **How do we manage them at scale?** → `/governance` — lifecycle pipeline, approvals, canary rollouts, drift detection, policy-as-code.
2. **How do we keep them safe?** → `/security` — six-layer defense-in-depth, prompt-injection blocking, PII redaction, sandboxed execution, audit log, kill-switch.
3. **How do we make money from them?** → `/monetization` — five revenue models, AI-driven pricing experiments, multi-currency payouts, take-rate schedule.

---

## Three user roles

- **Developers** — publish AI agents, run experiments, earn credits.
- **Consumers (Enterprises)** — browse, deploy, and procure agents under one governed contract.
- **Admins / Platform team** — moderate quality, compliance, payouts, and security posture.

---

## Pages

### Marketing & Discovery
- **`/`** — Marketing landing page. Hero, three pillars, personas, defense-in-depth diagram, full demo grid.
- **`/marketplace`** — Searchable, filterable grid of all published agents.
- **`/marketplace/:id`** — Detailed agent profile (Overview · Metrics · API Docs · Reviews) with deploy CTA.

### Operations
- **`/dashboard`** — Platform KPIs, 14-day usage chart, top agents table.
- **`/deployments`** — Live terminal-style log viewer, resource gauges, Start / Stop / Restart controls.
- **`/analytics`** — Usage, revenue, errors with 7d / 14d / 30d range selector.
- **`/billing`** — Credit wallet, top-up modal, transaction history.

### Trust & Control (NEW)
- **`/security`** — Trust posture score, active guardrails, live threat feed, tamper-evident audit log, RBAC breakdown, data residency map, compliance badges (SOC 2 · ISO 27001 · GDPR · HIPAA · NIST AI RMF · EU AI Act).
- **`/governance`** — 7-stage agent lifecycle pipeline, approval queue with risk-class scoring, active rollouts with SLO gating, drift monitoring, policy-as-code (Rego).
- **`/monetization`** — Five revenue models, top-earning agents leaderboard, multi-armed-bandit pricing experiments, payouts ledger, volume-tiered take-rate schedule.

### Workspaces
- **`/my-agents`** — Developer-only. Manage and submit agents.
- **`/admin`** — Admin-only. Compliance review, platform health, payouts.

---

## Deploying to Vercel

The site is plain static HTML in `mockups/`. The `vercel.json` at the repo root configures clean URLs and rewrites so every page is reachable at `/page-name` (no `.html` suffix, no `/mockups` prefix).

**One-command deploy:**

```bash
vercel --prod
```

That's it — no build step, no framework, no backend.

### Routes configured by `vercel.json`

| URL | Serves |
|-----|--------|
| `/` | `mockups/index.html` |
| `/dashboard` | `mockups/dashboard.html` |
| `/marketplace` | `mockups/marketplace.html` |
| `/marketplace/:id` | `mockups/marketplace-detail.html` |
| `/deployments` | `mockups/deployments.html` |
| `/analytics` | `mockups/analytics.html` |
| `/billing` | `mockups/billing.html` |
| `/security` | `mockups/security.html` |
| `/governance` | `mockups/governance.html` |
| `/monetization` | `mockups/monetization.html` |
| `/my-agents` | `mockups/my-agents.html` |
| `/admin` | `mockups/admin.html` |

Security headers (`X-Content-Type-Options: nosniff`, `X-Frame-Options: SAMEORIGIN`, `Referrer-Policy: strict-origin-when-cross-origin`) are applied to every response.

---

## Tech notes

- 100% static HTML + inline CSS — no framework, no build step.
- All charts are inline SVG (no chart library needed).
- All data is mocked but realistic — what you'd see at day-100 of an enterprise agent fleet.
- Inter font from Google Fonts.
- Mobile-friendly landing page; operational pages are desktop-first.

---

## Local preview

Just open any HTML file in a browser, or run:

```bash
npx serve mockups
```

Then visit `http://localhost:3000`.
