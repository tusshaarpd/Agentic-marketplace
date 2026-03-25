# AgentHub — Agentic Marketplace Platform

An enterprise-grade AI agent marketplace (think "AWS Marketplace, but for AI Agents") built with Next.js 14, TypeScript, and Tailwind CSS — deployed on Vercel with fully mocked data and no backend.

## Overview

Three user roles interact with the platform:
- **Developers** — publish AI agents and earn credits
- **Consumers** — browse, deploy, and pay-per-use for agents
- **Admins** — moderate quality, compliance, and payouts

---

## Pages

### `/dashboard`
Platform-level KPI overview. Displays four metric cards (Total Requests, Total Revenue, Active Agents, Platform Error Rate), a 14-day usage line chart, and a top 5 agents table ranked by rating.

### `/marketplace`
Searchable, filterable grid of all published AI agents. Filter by category (NLP, Document Analysis, Code Review, etc.) or text search in real-time. Each agent card shows rating, price in credits, latency, and compliance tier.

### `/marketplace/[id]`
Detailed agent profile page. Includes tabbed sections for Overview, Metrics (latency, throughput, error rate, hallucination rate), API Docs (mock curl command), and Reviews. Also shows a sidebar pricing card and "Deploy Agent" CTA.

### `/deployments`
Live deployment console. Left panel shows a terminal-style log viewer (black bg, green monospace text) with auto-scrolling simulated logs streamed every 2 seconds. Right panel has a deployment selector, status indicator, Start/Stop/Restart controls, and CPU/Memory resource gauges.

### `/analytics`
Usage and revenue analytics with a date range selector (7d / 14d / 30d). Includes KPI cards, a requests/errors line chart, and a daily revenue bar chart. Developers also see per-agent performance; Admins see a compliance incidents summary.

### `/billing`
Credit wallet showing current balance with an "Add Credits" modal (500 / 1000 / 5000 / 10000 credit packages). Below is a filterable transaction history table showing purchases, usage charges, payouts, and refunds.

### `/my-agents`
Developer-only page (RBAC protected). Lists the current developer's agents with status, version, rating, and pricing. Includes a "Submit New Agent" modal form and per-agent "Push Update" actions that trigger toast notifications.

### `/admin`
Admin-only page (RBAC protected). Three tabs:
- **Compliance Review** — approve or reject agents based on static validation, dynamic testing, AI evaluation, and safety guardrails scores
- **Platform Health** — GMV, active deployments, uptime KPIs, and usage charts
- **Payouts** — process pending developer payout requests

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS v3 |
| Charts | Recharts |
| Icons | Lucide React |
| Deployment | Vercel |
| Data | 100% mock (no backend) |

---

## Getting Started

```bash
npm install
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) — the root redirects to `/dashboard`.

To test different roles, use the role switcher in the user dropdown (TopBar). Default user is `Arjun Mehta` (DEVELOPER).

---

## Static Mockups

HTML mockups for all pages are available in the `mockups/` folder. Open `mockups/index.html` in a browser for a quick preview without running the Next.js app.
