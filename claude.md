# CLAUDE.md — Agentic Marketplace Platform
## Claude Code Instruction Document

> **Purpose:** This is the single source of truth for Claude Code to build, structure, and deploy the Agentic Marketplace Platform on Vercel. Read every section before writing any code. Follow this document precisely.

---

## 0. What You Are Building (Plain English)

Think of this like "AWS Marketplace, but for AI Agents." Three types of people use this platform:

- **Developers** publish AI agents and earn credits (money equivalent).
- **Consumers** browse, deploy, and pay-per-use for those AI agents.
- **Admins** moderate quality, compliance, and payouts.

Since this is a **Vercel-only frontend deployment**, there is no real backend. All data comes from mock files. All containers, logs, and metrics are simulated using `setInterval`, mock state, and realistic-looking data. The UI must look and feel like Stripe or AWS Console — enterprise grade.

---

## 1. Deployment Target

| Property | Value |
|---|---|
| Platform | **Vercel** |
| Framework | **Next.js 14 (App Router)** |
| Styling | **Tailwind CSS v3** |
| Language | **TypeScript (strict mode)** |
| Charts | **Recharts** |
| No backend | All data is mock/static |
| No Docker runtime | Simulated via UI state |
| Node version | 18+ |

### Vercel-Specific Rules

- Use only `next/image`, `next/link`, `next/navigation` — never `react-router-dom`.
- No `fs`, `path`, or Node.js server modules in client components.
- All dynamic routes use the App Router convention: `app/[param]/page.tsx`.
- Environment variables follow the `NEXT_PUBLIC_` prefix for client access.
- The `next.config.js` must set `output: 'standalone'` for Vercel Edge compatibility.
- No WebSockets — use `setInterval` to simulate real-time log streaming.

---

## 2. Tech Stack (Exact Packages)

Install these packages. Do not deviate:

```bash
npm install next@14 react@18 react-dom@18
npm install typescript @types/react @types/node
npm install tailwindcss postcss autoprefixer
npm install recharts @types/recharts
npm install lucide-react
npm install clsx tailwind-merge
npm install @radix-ui/react-tabs @radix-ui/react-dropdown-menu @radix-ui/react-dialog @radix-ui/react-toast
npm install date-fns
```

Do NOT install: `express`, `mongoose`, `prisma`, `socket.io`, `axios` (use native `fetch`), `styled-components`, `emotion`.

---

## 3. Folder Structure (Mandatory)

Create exactly this structure. No deviations:

```
agentic-marketplace/
├── app/
│   ├── layout.tsx                  # Root layout with sidebar + topbar
│   ├── page.tsx                    # Redirects to /dashboard
│   ├── dashboard/
│   │   └── page.tsx                # Dashboard KPI page
│   ├── marketplace/
│   │   ├── page.tsx                # Agent listing grid
│   │   └── [id]/
│   │       └── page.tsx            # Agent detail page
│   ├── deployments/
│   │   └── page.tsx                # Deployment console with live logs
│   ├── analytics/
│   │   └── page.tsx                # Analytics charts page
│   ├── billing/
│   │   └── page.tsx                # Credit wallet + billing history
│   ├── my-agents/
│   │   └── page.tsx                # Developer's agent management
│   └── admin/
│       └── page.tsx                # Admin compliance dashboard
│
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx             # Collapsible left nav
│   │   ├── TopBar.tsx              # Search + user dropdown
│   │   ├── AppShell.tsx            # Combines Sidebar + TopBar + main
│   │   └── ProtectedRoute.tsx      # RBAC guard component
│   ├── ui/
│   │   ├── Badge.tsx               # Status badges (Verified, Pending, Failed)
│   │   ├── Card.tsx                # Generic card wrapper
│   │   ├── KpiCard.tsx             # Number + label + trend card
│   │   ├── MetricCard.tsx          # Agent metric display card
│   │   ├── StatusIndicator.tsx     # Running/Stopped/Failed dot+label
│   │   ├── Skeleton.tsx            # Loading skeleton blocks
│   │   ├── Toast.tsx               # Toast notification system
│   │   ├── Modal.tsx               # Generic modal dialog
│   │   ├── Tabs.tsx                # Tab component wrapper
│   │   └── Table.tsx               # Sortable, paginated table
│   ├── marketplace/
│   │   ├── AgentCard.tsx           # Card shown in marketplace grid
│   │   ├── AgentFilters.tsx        # Search + filter bar
│   │   └── AgentMetricsChart.tsx   # Recharts time-series for agent
│   ├── deployments/
│   │   ├── LogPanel.tsx            # Terminal-style live log viewer
│   │   ├── DeploymentControls.tsx  # Start/Stop/Restart buttons
│   │   └── ResourceGauge.tsx       # CPU/Memory usage display
│   ├── analytics/
│   │   ├── UsageLineChart.tsx      # Recharts line chart
│   │   └── RevenueBarChart.tsx     # Recharts bar chart
│   └── billing/
│       ├── CreditWallet.tsx        # Balance + top-up UI
│       └── TransactionTable.tsx    # Billing history table
│
├── lib/
│   ├── mockData.ts                 # All mock data (agents, metrics, users)
│   ├── auth.ts                     # RBAC roles, users, hasAccess()
│   ├── utils.ts                    # cn(), formatCredits(), formatMs()
│   └── constants.ts                # ROLES, STATUS, METRIC_THRESHOLDS
│
├── hooks/
│   ├── useAuth.ts                  # Returns current mock user + role
│   ├── useLiveLogs.ts              # setInterval log streaming hook
│   └── useMetrics.ts               # Returns agent metrics with refresh
│
├── types/
│   └── index.ts                    # All TypeScript interfaces
│
├── public/
│   └── logo.svg                    # Platform logo
│
├── tailwind.config.ts
├── next.config.js
├── tsconfig.json
└── CLAUDE.md                       # This file
```

---

## 4. TypeScript Types (Define First, Use Everywhere)

Create `types/index.ts` with these exact interfaces:

```typescript
// ---- Roles ----
export type Role = 'ADMIN' | 'DEVELOPER' | 'CONSUMER';

export interface User {
  id: string;
  name: string;
  email: string;
  role: Role;
  orgId: string;
  avatarInitials: string;
  creditBalance: number;
}

// ---- Agents ----
export type AgentStatus = 'PUBLISHED' | 'PENDING' | 'REJECTED' | 'DRAFT';
export type PricingModel = 'PER_REQUEST' | 'PER_MINUTE' | 'SUBSCRIPTION' | 'FREEMIUM';
export type ComplianceTier = 'VERIFIED' | 'PROVISIONAL' | 'UNVERIFIED';

export interface AgentMetrics {
  hallucinationRate: number;       // percentage, lower is better
  promptCompletionRate: number;    // percentage, higher is better
  piiDetectionAccuracy: number;    // percentage, higher is better
  latencyMs: number;               // milliseconds, lower is better
  throughputRps: number;           // requests per second
  errorRate: number;               // percentage, lower is better
  uptimePercent: number;           // percentage, higher is better
}

export interface Agent {
  id: string;
  name: string;
  description: string;
  category: string;
  developerId: string;
  developerName: string;
  orgId: string;
  status: AgentStatus;
  complianceTier: ComplianceTier;
  version: string;
  containerImage: string;
  pricingModel: PricingModel;
  pricePerUnit: number;            // credits
  rating: number;                  // 0-5
  reviewCount: number;
  metrics: AgentMetrics;
  tags: string[];
  createdAt: string;               // ISO string
  updatedAt: string;
}

// ---- Deployments ----
export type DeploymentStatus = 'RUNNING' | 'STOPPED' | 'FAILED' | 'STARTING' | 'RESTARTING';

export interface Deployment {
  id: string;
  agentId: string;
  agentName: string;
  userId: string;
  orgId: string;
  status: DeploymentStatus;
  cpuUsage: number;                // percentage
  memoryUsageMb: number;
  requestCount: number;
  queueDepth: number;
  startedAt: string;
  logs: LogEntry[];
}

export interface LogEntry {
  timestamp: string;
  level: 'INFO' | 'WARN' | 'ERROR';
  message: string;
}

// ---- Billing ----
export type TransactionType = 'CREDIT_PURCHASE' | 'AGENT_USAGE' | 'SUBSCRIPTION' | 'PAYOUT' | 'REFUND';

export interface Transaction {
  id: string;
  userId: string;
  agentId?: string;
  agentName?: string;
  type: TransactionType;
  credits: number;                 // positive = earned, negative = spent
  description: string;
  createdAt: string;
}

// ---- Analytics ----
export interface UsageDataPoint {
  date: string;                    // 'MMM DD' format
  requests: number;
  revenue: number;
  errors: number;
}

// ---- Compliance ----
export type ComplianceCheckStatus = 'PASS' | 'FAIL' | 'WARNING';

export interface ComplianceReport {
  agentId: string;
  agentName: string;
  staticValidation: ComplianceCheckStatus;
  dynamicTesting: ComplianceCheckStatus;
  aiEvaluation: ComplianceCheckStatus;
  safetyGuardrails: ComplianceCheckStatus;
  overallScore: number;            // 0-100
  reviewedAt: string;
  reviewedBy: string;
}
```

---

## 5. Mock Data (`lib/mockData.ts`)

This file provides all data. It must be realistic — no "Agent 1", "Test User" names. Use professional, realistic names.

### What to include:

**`MOCK_AGENTS: Agent[]`** — at least 12 agents across categories like: NLP Processing, Document Analysis, Code Review, Customer Support, Data Extraction, Fraud Detection, Translation, Summarization.

For each agent, provide realistic metrics. Example ranges:
- `hallucinationRate`: 1.2% to 8.5%
- `promptCompletionRate`: 92% to 99.8%
- `latencyMs`: 180 to 2400
- `uptimePercent`: 98.1% to 99.97%

**`MOCK_USERS: User[]`** — one user per role:
```typescript
{ id: 'u1', name: 'Arjun Mehta', role: 'DEVELOPER', orgId: 'org-1', creditBalance: 4200 }
{ id: 'u2', name: 'Priya Sharma', role: 'CONSUMER', orgId: 'org-2', creditBalance: 1800 }
{ id: 'u3', name: 'Rahul Singh', role: 'ADMIN', orgId: 'org-admin', creditBalance: 99999 }
```

**`MOCK_DEPLOYMENTS: Deployment[]`** — 3-4 active deployments with varying statuses.

**`MOCK_TRANSACTIONS: Transaction[]`** — 15-20 realistic transactions mixing purchases, usage charges, and payouts.

**`MOCK_USAGE_TREND: UsageDataPoint[]`** — 30 days of daily data for charts.

**`MOCK_COMPLIANCE_REPORTS: ComplianceReport[]`** — one per agent in PENDING or REJECTED status.

---

## 6. RBAC System (`lib/auth.ts`)

```typescript
import { Role, User } from '@/types';
import { MOCK_USERS } from './mockData';

// Simulate a logged-in user. Change this to test different roles.
export const CURRENT_USER: User = MOCK_USERS[0]; // Default: DEVELOPER

// Define what each role can access
const ROLE_PERMISSIONS: Record<Role, string[]> = {
  ADMIN: ['dashboard', 'marketplace', 'my-agents', 'deployments', 'analytics', 'billing', 'admin'],
  DEVELOPER: ['dashboard', 'marketplace', 'my-agents', 'deployments', 'analytics', 'billing'],
  CONSUMER: ['dashboard', 'marketplace', 'deployments', 'analytics', 'billing'],
};

export function hasAccess(role: Role, page: string): boolean {
  return ROLE_PERMISSIONS[role]?.includes(page) ?? false;
}

export function getNavItems(role: Role) {
  return NAV_ITEMS.filter(item => hasAccess(role, item.key));
}
```

`ProtectedRoute.tsx` wraps any page component. If `hasAccess()` returns false, render a centered "Access Denied" card with the user's role displayed, not a redirect.

---

## 7. Page-by-Page Specifications

### 7.1 Root Layout (`app/layout.tsx`)

- Renders `<AppShell>` which contains `<Sidebar>` + `<TopBar>` + `<main>{children}</main>`.
- Body uses a light gray background: `bg-gray-50`.
- Font: `Inter` from Google Fonts via `next/font/google`.
- No authentication redirects — just render based on `CURRENT_USER`.

### 7.2 Dashboard (`app/dashboard/page.tsx`)

Render four `<KpiCard>` components in a responsive grid (2 cols on mobile, 4 cols on desktop):

| KPI | Value source | Icon |
|---|---|---|
| Total Requests Today | sum from `MOCK_USAGE_TREND` last 1 day | `Activity` |
| Total Revenue (Credits) | sum from `MOCK_TRANSACTIONS` | `Coins` |
| Active Agents | count of `PUBLISHED` agents | `Bot` |
| Platform Error Rate | avg error rate across agents | `AlertTriangle` |

Below the KPIs, render a `<UsageLineChart>` with the last 14 days of data.

Below that, render a table of the top 5 agents by rating.

### 7.3 Marketplace (`app/marketplace/page.tsx`)

- Top: `<AgentFilters>` with a text search input and category dropdown (All, NLP, Document, Code, Support, Data).
- Below: responsive grid of `<AgentCard>` components — 3 columns on desktop, 2 on tablet, 1 on mobile.
- Filter agents in real-time using `useState` — no page reload.
- Each `<AgentCard>` shows: name, category badge, developer name, star rating, price per request in credits, key metric (latency), compliance badge, and a "View Details" button linking to `/marketplace/[id]`.

### 7.4 Agent Detail Page (`app/marketplace/[id]/page.tsx`)

Use `params.id` to find the agent from `MOCK_AGENTS`. If not found, render a 404-style card.

Structure:
1. **Header**: Agent name (h1), `<Badge>` for compliance tier, `<Badge>` for status, version number, "Deploy Agent" button (opens a modal confirming credit deduction).
2. **Tabs** (Overview | Metrics | API Docs | Reviews):
   - **Overview tab**: Description, category, developer, tags, pricing breakdown.
   - **Metrics tab**: 4 `<MetricCard>` components (Latency, Throughput, Error Rate, Hallucination Rate) + a `<AgentMetricsChart>` showing simulated 7-day trend.
   - **API Docs tab**: Static code block showing a mock `curl` command using the agent's ID.
   - **Reviews tab**: 3-4 hardcoded mock reviews with names, ratings, and dates.
3. **Sidebar** (right column on desktop): Pricing card with CTA, Agent info (version, last updated, uptime).

### 7.5 Deployment Console (`app/deployments/page.tsx`)

This is the most critical page. Build it with care.

**Left panel** (2/3 width): `<LogPanel>` component
- Black background, `font-mono`, green text (`text-green-400`).
- Displays last 20 log entries from deployment's `logs` array.
- Each log line: `[HH:MM:SS] [LEVEL] message`
- Auto-scrolls to bottom on new log.
- Uses `useLiveLogs` hook to append a new simulated log every 2 seconds when status is `RUNNING`.

**Right panel** (1/3 width):
- Deployment selector dropdown (pick from `MOCK_DEPLOYMENTS`).
- `<StatusIndicator>` showing current status with colored dot.
- `<DeploymentControls>` with Start, Stop, Restart buttons. Clicking these changes the `status` state locally.
- `<ResourceGauge>` showing CPU % and Memory MB as progress bars.
- Stats: Request Count, Queue Depth.

**`useLiveLogs` hook logic:**
```typescript
// When status === 'RUNNING', use setInterval every 2000ms to pick a random
// log message from a predefined pool and append it to the logs array.
// Clear the interval when status !== 'RUNNING' or component unmounts.
```

Predefined log message pool (at least 20 entries):
```
"Processing inference request #4521"
"PII scan completed — no violations detected"
"Token limit check: 2048/4096"
"Cache hit ratio: 78.3%"
"Guardrail check passed for output"
"Response latency: 342ms"
"Hallucination score: 0.04 (within threshold)"
"Request queued — current depth: 3"
// ... etc
```

### 7.6 Analytics (`app/analytics/page.tsx`)

- Date range selector (Last 7d / 14d / 30d) — filter `MOCK_USAGE_TREND` accordingly.
- 4 KPI cards: Total Requests, Total Revenue, Avg Success Rate, Avg Latency.
- `<UsageLineChart>`: Line chart with requests and errors over time.
- `<RevenueBarChart>`: Bar chart of daily revenue.
- If role is `DEVELOPER`, also show a table of "My Agents" performance.
- If role is `ADMIN`, also show a compliance incidents summary.

### 7.7 Billing (`app/billing/page.tsx`)

- Top: `<CreditWallet>` showing current balance, a "Add Credits" button (opens a modal with 4 mock packages: 500, 1000, 5000, 10000 credits).
- Below: `<TransactionTable>` showing `MOCK_TRANSACTIONS` for the current user filtered by `userId`.
- Each row: Date, Type badge, Agent (if applicable), Credits (green if positive, red if negative), Description.
- "Export CSV" button — console.log only, not functional download.

### 7.8 My Agents (`app/my-agents/page.tsx`)

- Only accessible to `DEVELOPER` and `ADMIN` roles (wrap with `<ProtectedRoute>`).
- Table of agents where `developerId === CURRENT_USER.id`.
- Columns: Name, Status badge, Version, Rating, Pricing, Latency, Actions (Edit | View Metrics | Deprecate).
- "Submit New Agent" button — opens a modal with a placeholder form (name, description, category, pricing model, price). On submit, show a toast: "Agent submitted for compliance review."
- Patch management row: For each agent, show "v{version}" with a "Push Update" button that shows a toast: "Update queued for review."

### 7.9 Admin Console (`app/admin/page.tsx`)

- Only accessible to `ADMIN` role.
- Tabs: Compliance Review | Platform Health | Payouts.
- **Compliance tab**: Table of `MOCK_COMPLIANCE_REPORTS` with agent name, each check status, overall score, and Approve/Reject buttons (update state locally + show toast).
- **Platform Health tab**: 4 KPI cards (Total Agents, Active Deployments, GMV this month, Avg Platform Uptime) + a usage chart.
- **Payouts tab**: Table of pending developer payouts (mock data), with "Process Payout" button per row.

---

## 8. Component Specifications

### `<KpiCard>`

```typescript
interface KpiCardProps {
  title: string;
  value: string | number;
  trend?: number;        // positive = green up arrow, negative = red down arrow
  icon: LucideIcon;
  iconColor?: string;    // Tailwind color class e.g. 'text-blue-500'
}
```

Style: white background, rounded-xl, shadow-sm, p-6. Large bold value, small muted title, icon top-right.

### `<AgentCard>`

```typescript
interface AgentCardProps {
  agent: Agent;
}
```

Style: white card, hover:shadow-md transition, p-5. Top: category badge (colored by category). Middle: name (font-semibold), description (2-line truncate, text-sm text-gray-500). Bottom: star rating, price, compliance tier badge, "View Details" button.

### `<Badge>`

```typescript
interface BadgeProps {
  variant: 'success' | 'warning' | 'error' | 'info' | 'neutral';
  children: React.ReactNode;
}
```

Style mapping:
- `success` → `bg-green-100 text-green-800`
- `warning` → `bg-yellow-100 text-yellow-800`
- `error` → `bg-red-100 text-red-800`
- `info` → `bg-blue-100 text-blue-800`
- `neutral` → `bg-gray-100 text-gray-700`

### `<StatusIndicator>`

Shows a colored dot + text:
- `RUNNING` → green dot + "Running"
- `STOPPED` → gray dot + "Stopped"
- `FAILED` → red dot + "Failed"
- `STARTING` → yellow pulsing dot + "Starting..."
- `RESTARTING` → yellow pulsing dot + "Restarting..."

Pulsing animation: use `animate-pulse` on the dot div.

### `<Sidebar>`

- Fixed left, full height, `w-64` when expanded, `w-16` when collapsed.
- Toggle button at the bottom.
- Each nav item: icon (Lucide) + label, `hover:bg-gray-100` on hover, `bg-blue-50 text-blue-700 font-medium` when active (use `usePathname()`).
- At the bottom of the sidebar: current user's name, role badge, avatar initials circle.

### `<TopBar>`

- Fixed top, full width, height 16, white background, border-bottom.
- Left: Platform logo + name "AgentHub".
- Center: Search input (placeholder: "Search agents, deployments...").
- Right: Notification bell icon (with a mock badge count of 3), user avatar circle with dropdown (Profile, Switch Role [for testing], Sign Out [console.log]).

---

## 9. Utility Functions (`lib/utils.ts`)

```typescript
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

// Merge Tailwind classes safely
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Format credits with commas and ⓒ symbol
export function formatCredits(n: number): string {
  return `ⓒ ${n.toLocaleString()}`;
}

// Format milliseconds
export function formatMs(ms: number): string {
  return ms < 1000 ? `${ms}ms` : `${(ms / 1000).toFixed(1)}s`;
}

// Format percentage
export function formatPercent(n: number): string {
  return `${n.toFixed(1)}%`;
}

// Get badge variant from AgentStatus
export function statusToBadgeVariant(status: AgentStatus): BadgeVariant {
  const map = { PUBLISHED: 'success', PENDING: 'warning', REJECTED: 'error', DRAFT: 'neutral' };
  return map[status] as BadgeVariant;
}

// Get badge variant from ComplianceTier
export function complianceToBadgeVariant(tier: ComplianceTier): BadgeVariant {
  return tier === 'VERIFIED' ? 'success' : tier === 'PROVISIONAL' ? 'warning' : 'neutral';
}
```

---

## 10. Constants (`lib/constants.ts`)

```typescript
export const ROLES = {
  ADMIN: 'ADMIN',
  DEVELOPER: 'DEVELOPER',
  CONSUMER: 'CONSUMER',
} as const;

export const AGENT_CATEGORIES = [
  'All', 'NLP Processing', 'Document Analysis', 'Code Review',
  'Customer Support', 'Data Extraction', 'Fraud Detection', 'Translation', 'Summarization'
];

export const METRIC_THRESHOLDS = {
  hallucinationRate: { max: 5.0, unit: '%' },       // Must be BELOW this
  promptCompletionRate: { min: 95.0, unit: '%' },    // Must be ABOVE this
  latencyMs: { max: 1500, unit: 'ms' },              // Must be BELOW this
  errorRate: { max: 2.0, unit: '%' },                // Must be BELOW this
  uptimePercent: { min: 99.5, unit: '%' },           // Must be ABOVE this
};

// Returns 'success' | 'warning' | 'error' for a given metric value
export function getMetricHealth(metric: keyof typeof METRIC_THRESHOLDS, value: number): 'success' | 'warning' | 'error' {
  // Implement threshold checking logic here
}

export const LOG_MESSAGE_POOL = [
  '[INFO] Processing inference request',
  '[INFO] PII scan passed — no violations detected',
  '[INFO] Token usage: 1842/4096',
  '[INFO] Cache hit — returning stored response',
  '[INFO] Guardrail output check passed',
  '[WARN] Response latency elevated: 820ms',
  '[INFO] Hallucination score: 0.03 (within SLA)',
  '[INFO] Request queue depth: 2',
  '[INFO] Model temperature applied: 0.7',
  '[INFO] Semantic similarity check: 0.94',
  '[INFO] Rate limit check: 42/60 requests',
  '[WARN] Memory usage at 73% — monitoring',
  '[INFO] Compliance tag applied: GDPR-SAFE',
  '[INFO] Response serialized in 12ms',
  '[INFO] Container health check: OK',
  '[INFO] Outbound filter applied',
  '[ERROR] Timeout on external lookup — using fallback',
  '[INFO] Auto-scaled: +1 replica',
  '[INFO] Request completed successfully',
  '[INFO] Audit log entry created',
];
```

---

## 11. Tailwind Configuration (`tailwind.config.ts`)

```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        brand: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['JetBrains Mono', 'Menlo', 'monospace'],
      },
    },
  },
  plugins: [],
};

export default config;
```

---

## 12. Next.js Config (`next.config.js`)

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  typescript: {
    ignoreBuildErrors: false, // Enforce type safety
  },
  eslint: {
    ignoreDuringBuilds: false,
  },
  images: {
    domains: [],
    unoptimized: true, // Required for Vercel static export
  },
};

module.exports = nextConfig;
```

---

## 13. Design System Rules

These rules must be followed across every component. No exceptions.

**Spacing:** Use Tailwind's spacing scale. The minimum padding inside any card is `p-5` or `p-6`. Never use `p-1` or `p-2` as primary card padding.

**Typography:**
- Page titles: `text-2xl font-bold text-gray-900`
- Section headers: `text-lg font-semibold text-gray-800`
- Body text: `text-sm text-gray-600`
- Labels and captions: `text-xs text-gray-500`
- Monospace (logs): `font-mono text-sm`

**Colors:**
- Primary action buttons: `bg-blue-600 hover:bg-blue-700 text-white`
- Secondary buttons: `bg-white border border-gray-300 hover:bg-gray-50 text-gray-700`
- Danger buttons: `bg-red-600 hover:bg-red-700 text-white`
- Page background: `bg-gray-50`
- Card background: `bg-white`
- Border color: `border-gray-200`

**Shadows:** Cards use `shadow-sm`. Modals use `shadow-xl`. Hover effects add `hover:shadow-md`.

**Border radius:** Cards and inputs: `rounded-xl`. Buttons: `rounded-lg`. Badges: `rounded-full`.

**Transitions:** All interactive elements: `transition-all duration-200`.

**No inline styles.** No `style={{}}` props anywhere.

---

## 14. Data Flow Architecture (How It All Connects)

Think of it like this — since we have no backend, all state lives in React:

```
MOCK_DATA (static truth)
    ↓
Page Component (imports mock data, sets up useState)
    ↓
Passes props to child components
    ↓
Child components render UI
    ↓
User interactions → setState (local updates only, not persisted)
    ↓
UI re-renders to reflect change
    ↓
Toast notifications confirm actions
```

For the Deployment Console specifically:
```
MOCK_DEPLOYMENTS → selected deployment state
    ↓
useLiveLogs(status) → appends logs via setInterval
    ↓
<LogPanel> auto-scrolls to bottom
    ↓
Start/Stop/Restart → setStatus() → clears or starts interval
```

---

## 15. Error States and Empty States

Every list or table must have an empty state. Every data load must have a skeleton state.

**Empty state pattern:** Centered `<div>` with a Lucide icon (muted gray), a title ("No agents found"), and a subtitle ("Try adjusting your filters"). Optional CTA button.

**Skeleton pattern:** Use `<Skeleton>` component which renders `animate-pulse bg-gray-200 rounded` divs matching the shape of the real content.

**Error state pattern:** A yellow warning card saying "Unable to load data. Showing cached results." (Never show a blank page.)

---

## 16. Build and Run Commands

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production (what Vercel runs)
npm run build

# Type check only
npx tsc --noEmit
```

The app must have zero TypeScript errors and zero build errors before being considered complete.

---

## 17. Vercel Deployment Checklist

Before marking the project complete, verify:

- [ ] `npm run build` completes with zero errors
- [ ] No `console.error` in the browser for hydration mismatches
- [ ] All pages render correctly at `/dashboard`, `/marketplace`, `/deployments`, `/analytics`, `/billing`, `/my-agents`, `/admin`
- [ ] `<ProtectedRoute>` correctly blocks `/my-agents` for CONSUMER role
- [ ] `<ProtectedRoute>` correctly blocks `/admin` for non-ADMIN roles
- [ ] Live logs in `/deployments` start and stop correctly with the controls
- [ ] Marketplace filters work in real-time
- [ ] Agent detail page works for any agent ID from `MOCK_AGENTS`
- [ ] Charts render correctly on `/analytics` and `/dashboard`
- [ ] All Tailwind classes are used from the utility library — no inline styles exist
- [ ] TypeScript is in strict mode and has no `any` types (use `unknown` if needed)
- [ ] `vercel.json` is NOT required — Next.js 14 deploys natively on Vercel

---

## 18. What NOT to Build (Scope Constraints)

These are explicitly out of scope for the Vercel-only deployment:

- No real authentication (no Clerk, Auth0, NextAuth)
- No real database (no Supabase, Neon, PlanetScale)
- No real payment processing (no Stripe API calls)
- No real container orchestration (no Kubernetes API)
- No real-time WebSockets (use `setInterval` only)
- No server actions that write to a database
- No file uploads
- No email sending
- No external API calls (everything is mock data)

The future backend roadmap (Phase 2+) would add: Supabase for auth + database, Stripe for payments, and a separate microservices backend. But for this phase, everything is frontend-only.

---

## 19. Bonus Features (Implement If Core Is Complete)

Implement these only after all core pages and components are verified working:

1. **Dark mode toggle** in the `<TopBar>`. Use `next-themes` for this. Store preference in localStorage.
2. **Role switcher** in the user dropdown. Lets you switch between `ADMIN | DEVELOPER | CONSUMER` during a session to test RBAC. Updates the `CURRENT_USER` in a React context.
3. **Toast notification system** — use a `<ToastProvider>` at root layout level. All actions (Deploy, Start, Stop, Submit Agent, Process Payout) trigger a toast.
4. **Skeleton loading states** — add a 600ms artificial delay on all pages using `useEffect` + `setTimeout` to simulate data loading, then show skeletons during that delay.

---

## 20. Summary: Build Order

Follow this order to avoid dependency issues:

1. `types/index.ts` — define all types first
2. `lib/constants.ts` — define all constants
3. `lib/mockData.ts` — create all mock data
4. `lib/utils.ts` and `lib/auth.ts` — utilities
5. `components/ui/` — all primitive UI components (Badge, Card, KpiCard, etc.)
6. `components/layout/` — Sidebar, TopBar, AppShell, ProtectedRoute
7. `hooks/` — useAuth, useLiveLogs, useMetrics
8. `app/layout.tsx` — root layout
9. Pages in this order: dashboard → marketplace → marketplace/[id] → deployments → analytics → billing → my-agents → admin
10. Final: verify build, check all routes, run TypeScript

---

*This document is the complete specification. Do not make architectural decisions outside of what is defined here. If something is not specified, use the closest analogy from what is specified.*
