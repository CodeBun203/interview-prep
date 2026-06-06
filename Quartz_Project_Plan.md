# Quartz: Commerce API Test Intelligence Platform
## Apple SDET Interview Project Plan — 5-Day Sprint

---

## What This Project Is

Quartz is an internal test harness for commerce API workflows. Engineers paste in an API specification or describe an endpoint, and Quartz generates edge-case test suites using an LLM, executes them against the target API, stores results with full request/response history, and uses AI to explain failures in plain English.

This is exactly what the Apple ASE Media Platform Quality team does every day: build internal tools that help engineers test purchasing, subscription, offer redemption, and entitlement workflows at scale.

**Stack:** Pure React + Vite (frontend) · Node.js + Express (backend) · PostgreSQL · Anthropic API · GitHub Actions · Docker · Bash

**Why this stack matches the job description exactly:**
- "React-based frontends" — pure React, not Next.js. Shows you know the difference.
- "Powerful Node.js backend services" — Node is the primary backend, not a BFF.
- "Experience integrating LLM APIs" — Anthropic Claude API with real auth, rate limiting, and error handling.
- "Python" — Python CLI test runner and data analysis scripts alongside Node.
- "Shell scripting" — Bash scripts for CI integration and bulk test execution.
- "SQL" — PostgreSQL with hand-written queries for complex result aggregations.
- "CI/CD pipeline design" — GitHub Actions with multiple quality gates.

**Timeline:** 5 days. Do not over-scope. Every feature here maps directly to a talking point in the interview.

---

## Commerce Domain Vocabulary — Learn This Before Writing Any Code

The hiring manager uses this language daily. Using it correctly signals immediate domain fit.

| Term | Meaning |
|---|---|
| **SKU** | Stock Keeping Unit — a specific purchasable item (e.g., `com.apple.music.individual.monthly`) |
| **Entitlement** | Permission granted to a user after a successful purchase (right to access content) |
| **Receipt** | Cryptographically signed proof of purchase from the App Store |
| **Offer code** | Single-use or multi-use promotional code for subscription discounts |
| **Grace period** | Window after a failed renewal where the subscription remains active |
| **Billing retry** | Automatic reattempt of a failed subscription payment |
| **Proration** | Credit applied when a user upgrades mid-cycle |
| **Restore purchase** | Re-granting entitlements when a user reinstalls or switches devices |
| **Sandbox** | Apple's test environment for commerce — real flows, fake money |
| **Storefront** | A regional instance of the App Store (US, EU, JP, etc.) |
| **State machine** | A subscription moves through states: active → grace_period → expired → churned |

When the hiring manager describes what the team tests, you will recognize every word. That recognition matters.

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│   Quartz React App (Vite + TypeScript strict)           │
│                                                         │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────┐ │
│  │ Suite Builder│  │  Run Console │  │  AI Assistant │ │
│  │              │  │  (live logs) │  │  (generate +  │ │
│  │ Define suites│  │              │  │   analyze)    │ │
│  │ Import OpenAPI│  │ Pass/Fail   │  │               │ │
│  └──────┬───────┘  └──────┬───────┘  └───────┬───────┘ │
└─────────┼────────────────┼────────────────────┼─────────┘
          │ REST + WS      │                    │
┌─────────▼────────────────▼────────────────────▼─────────┐
│   Node.js / Express Backend                             │
│                                                         │
│  /api/suites     → CRUD for test suites and cases       │
│  /api/runs       → Execute suites, stream results       │
│  /api/ai         → LLM generation and analysis          │
│  /api/commerce   → Mock commerce endpoints (sandbox)    │
│  /health         → Liveness probe                       │
│                                                         │
│  WebSocket server → streams live test output to browser │
└──────────────────────┬──────────────────────────────────┘
                       │
          ┌────────────┼────────────┐
          │            │            │
   ┌──────▼──┐  ┌──────▼──┐  ┌─────▼─────────┐
   │Postgres │  │Anthropic│  │ Commerce Mock  │
   │(results)│  │  API    │  │ (purchase/sub) │
   └─────────┘  └─────────┘  └───────────────┘
```

---

## FAANG Engineering Setup — Do This Before Writing a Line of Feature Code

```bash
mkdir quartz && cd quartz
git init && git checkout -b main

# Project structure — establish this before writing any code
mkdir -p backend/{src/{routes,services,db,middleware,commerce},tests/{unit,integration}}
mkdir -p frontend/src/{components,pages,hooks,lib,types}
mkdir -p scripts
mkdir -p docs/adr
mkdir -p .github/workflows
mkdir -p infra

# CLAUDE.md — always first
cat > CLAUDE.md << 'EOF'
# Quartz — Commerce API Test Intelligence Platform

## Purpose
Internal test harness for commerce API workflows. Built specifically to demonstrate
Apple SDET capabilities: React + Node.js + LLM API integration + CI/CD.

## Stack
- Frontend: React 18 + Vite + TypeScript strict mode (src: frontend/)
- Backend: Node.js 20 + Express 4 (src: backend/)
- Database: PostgreSQL 15 (schema: backend/src/db/schema.sql)
- AI: Anthropic Claude API (service: backend/src/services/ai.service.ts)
- Testing: Jest (backend) + Vitest (frontend) + Playwright (E2E)

## Run locally
docker-compose up -d        # start postgres
cd backend && npm run dev   # Node server on :3001
cd frontend && npm run dev  # Vite dev server on :5173

## Key environment variables (see .env.example)
DATABASE_URL=postgresql://quartz:quartz_dev@localhost:5432/quartz
ANTHROPIC_API_KEY=sk-ant-...
COMMERCE_API_BASE_URL=http://localhost:3001/api/commerce

## Test commands
cd backend && npm test
cd frontend && npm test
npx playwright test

## Commerce domain
This tool tests: purchase, subscribe, redeem-offer, refund, restore-purchase flows.
See docs/adr/001-commerce-domain.md for domain vocabulary.
EOF

# .gitignore
cat > .gitignore << 'EOF'
node_modules/
dist/
.next/
.env
.env.*
*.pem
*.key
coverage/
.pytest_cache/
__pycache__/
*.pyc
.DS_Store
EOF

# Conventional commits + pre-commit hook
cat > .commitlintrc.json << 'EOF'
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "scope-enum": [2, "always",
      ["frontend", "backend", "db", "ai", "commerce", "ci", "infra", "docs"]
    ]
  }
}
EOF

git add CLAUDE.md .gitignore .commitlintrc.json
git commit -m "chore: quartz project scaffold"
git remote add origin https://github.com/CodeBun203/quartz.git
git push -u origin main
```

**Write the ADR before writing schema or routes:**

```markdown
# docs/adr/001-architecture-decisions.md

## ADR 001: Node.js as Primary Backend

**Context:** Choosing between FastAPI (Python) and Node.js/Express for the backend.

**Decision:** Node.js/Express.

**Reasoning:** The Apple job description says "powerful Node.js backend services."
Using Node.js here demonstrates that claim directly. Node's non-blocking I/O also
suits this workload: the test runner makes many concurrent outbound HTTP calls to
commerce endpoints. In Python, asyncio would work, but Node's event loop handles
this natively without any special framework.

**Trade-off:** Python's requests library is more ergonomic for HTTP testing.
Mitigated by using axios and the node-fetch native API.

---

## ADR 002: Anthropic API over OpenAI

**Context:** Choosing an LLM provider.

**Decision:** Anthropic Claude API (claude-sonnet-4-6).

**Reasoning:** Anthropic's API has the best support for structured JSON output
via tool_use, which is critical for generating machine-parseable test cases
rather than markdown text. Also demonstrates familiarity with a provider other
than OpenAI, which interviewers at AI-forward companies notice.

**Trade-off:** Slightly higher cost per token than gpt-3.5-turbo.
Acceptable because this is an internal tool with low call volume.

---

## ADR 003: Pure React over Next.js

**Context:** Choosing between React (Vite) and Next.js for the frontend.

**Decision:** Pure React with Vite.

**Reasoning:** The job says "React-based frontends." Next.js is a framework
built on React — using it would obscure whether you know React itself.
Internal tooling does not need SSR, SEO, or file-based routing. Pure React
with Vite starts faster and is simpler for this use case. Also: the hiring
manager will see "React" in the stack without the Next.js wrapper, which
matches the exact language of the job description.
```

---

## Day 1: Database, Docker, and Node.js Foundation
### PostgreSQL schema, Express scaffold, Docker Compose, TypeScript config

---

### Git branch: `feat/backend-foundation`

Open these GitHub Issues before writing any code:
```bash
gh issue create --title "Task 1.1: Docker Compose and database setup" --label "infrastructure"
gh issue create --title "Task 1.2: PostgreSQL schema — commerce testing domain" --label "database"
gh issue create --title "Task 1.3: Node.js/Express TypeScript scaffold" --label "backend"
gh issue create --title "Task 1.4: Database access layer (pg + typed queries)" --label "backend"
```

---

### Task 1.1: Docker Compose

```yaml
# docker-compose.yml
version: "3.9"
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: quartz
      POSTGRES_USER: quartz
      POSTGRES_PASSWORD: quartz_dev
    ports: ["5432:5432"]
    volumes: [postgres_data:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U quartz"]
      interval: 5s
      retries: 5

volumes:
  postgres_data:
```

---

### Task 1.2: PostgreSQL Schema

Write the schema as raw SQL before touching any ORM. Writing SQL is a minimum qualification for this job — demonstrate it explicitly.

```sql
-- backend/src/db/schema.sql
-- Quartz: Commerce Test Intelligence Platform
-- Schema represents the core testing domain

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- A test suite is a named collection of test cases for one commerce workflow
CREATE TABLE test_suites (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name        TEXT NOT NULL,
    description TEXT,
    workflow    TEXT NOT NULL CHECK (workflow IN (
                    'purchase', 'subscribe', 'redeem_offer',
                    'refund', 'restore_purchase', 'subscription_lifecycle'
                )),
    base_url    TEXT NOT NULL,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- A test case is one specific scenario within a suite
CREATE TABLE test_cases (
    id            UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    suite_id      UUID NOT NULL REFERENCES test_suites(id) ON DELETE CASCADE,
    name          TEXT NOT NULL,
    description   TEXT,
    -- HTTP request definition
    method        TEXT NOT NULL CHECK (method IN ('GET','POST','PUT','PATCH','DELETE')),
    path          TEXT NOT NULL,
    headers       JSONB NOT NULL DEFAULT '{}',
    body          JSONB,
    -- Expected outcome
    expected_status   INTEGER NOT NULL,
    expected_body     JSONB,         -- null means don't validate body
    expected_contains TEXT[],        -- response body must contain these strings
    -- Metadata
    tags          TEXT[] NOT NULL DEFAULT '{}',
    ai_generated  BOOLEAN NOT NULL DEFAULT FALSE,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- A test run is one execution of a full suite
CREATE TABLE test_runs (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    suite_id    UUID NOT NULL REFERENCES test_suites(id),
    status      TEXT NOT NULL DEFAULT 'pending'
                    CHECK (status IN ('pending','running','completed','failed')),
    triggered_by TEXT NOT NULL DEFAULT 'manual',  -- 'manual', 'ci', 'schedule'
    started_at  TIMESTAMPTZ,
    completed_at TIMESTAMPTZ,
    -- Aggregate counts — denormalized for fast dashboard queries
    total_cases  INTEGER NOT NULL DEFAULT 0,
    passed       INTEGER NOT NULL DEFAULT 0,
    failed       INTEGER NOT NULL DEFAULT 0,
    skipped      INTEGER NOT NULL DEFAULT 0,
    duration_ms  INTEGER,
    created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- One result per test case per run — full request/response captured
CREATE TABLE test_results (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    run_id          UUID NOT NULL REFERENCES test_runs(id) ON DELETE CASCADE,
    case_id         UUID NOT NULL REFERENCES test_cases(id),
    status          TEXT NOT NULL CHECK (status IN ('passed','failed','skipped','error')),
    -- What actually happened
    actual_status   INTEGER,
    actual_body     JSONB,
    actual_headers  JSONB,
    duration_ms     INTEGER,
    -- AI analysis — populated asynchronously after failure
    failure_reason  TEXT,          -- plain English explanation from LLM
    ai_suggestion   TEXT,          -- LLM's suggested fix
    -- Full HTTP trace for debugging
    request_sent    JSONB,
    error_message   TEXT,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Index for dashboard queries
CREATE INDEX idx_test_results_run_id ON test_results(run_id);
CREATE INDEX idx_test_results_status ON test_results(run_id, status);
CREATE INDEX idx_test_runs_suite_id  ON test_runs(suite_id, created_at DESC);
CREATE INDEX idx_test_cases_suite_id ON test_cases(suite_id);

-- View: pass rate per suite over the last 30 days
-- Hand-written SQL to demonstrate SQL skill — not generated by an ORM
CREATE VIEW suite_pass_rates AS
SELECT
    s.id                                        AS suite_id,
    s.name                                      AS suite_name,
    s.workflow,
    COUNT(r.id)                                 AS total_runs,
    ROUND(
        AVG(r.passed::NUMERIC / NULLIF(r.total_cases, 0)) * 100, 1
    )                                           AS avg_pass_rate,
    MAX(r.completed_at)                         AS last_run_at,
    -- Trend: compare last 7 days to previous 7 days
    ROUND(
        AVG(CASE WHEN r.created_at >= NOW() - INTERVAL '7 days'
            THEN r.passed::NUMERIC / NULLIF(r.total_cases, 0) END) * 100 -
        AVG(CASE WHEN r.created_at BETWEEN NOW() - INTERVAL '14 days'
                                       AND NOW() - INTERVAL '7 days'
            THEN r.passed::NUMERIC / NULLIF(r.total_cases, 0) END) * 100,
        1
    )                                           AS pass_rate_trend_7d
FROM test_suites s
LEFT JOIN test_runs r ON r.suite_id = s.id
    AND r.status = 'completed'
    AND r.created_at >= NOW() - INTERVAL '30 days'
GROUP BY s.id, s.name, s.workflow;
```

Apply it:
```bash
docker-compose up -d postgres
PGPASSWORD=quartz_dev psql -h localhost -U quartz -d quartz -f backend/src/db/schema.sql
```

---

### Task 1.3: Node.js TypeScript Scaffold

```bash
cd backend
npm init -y
npm install express cors helmet morgan pg uuid dotenv ws axios
npm install --save-dev typescript @types/node @types/express @types/pg \
  @types/cors @types/morgan @types/ws @types/uuid \
  ts-node nodemon jest @types/jest ts-jest supertest @types/supertest
```

```json
// backend/tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "CommonJS",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "esModuleInterop": true,
    "resolveJsonModule": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

```typescript
// backend/src/app.ts
import express from "express"
import cors from "cors"
import helmet from "helmet"
import morgan from "morgan"
import { suitesRouter } from "./routes/suites"
import { runsRouter } from "./routes/runs"
import { aiRouter } from "./routes/ai"
import { commerceRouter } from "./routes/commerce"

export function createApp(): express.Application {
  const app = express()

  app.use(helmet())
  app.use(cors({ origin: process.env.FRONTEND_URL ?? "http://localhost:5173" }))
  app.use(express.json({ limit: "1mb" }))
  app.use(morgan("combined"))

  app.use("/api/suites", suitesRouter)
  app.use("/api/runs", runsRouter)
  app.use("/api/ai", aiRouter)
  app.use("/api/commerce", commerceRouter)

  app.get("/health", (_req, res) => res.json({ status: "ok" }))

  return app
}
```

```typescript
// backend/src/server.ts
import http from "http"
import { WebSocketServer } from "ws"
import { createApp } from "./app"
import { runEventBus } from "./services/run-event-bus"

const PORT = parseInt(process.env.PORT ?? "3001", 10)
const app = createApp()
const server = http.createServer(app)

// WebSocket server — streams live test output to the React dashboard
const wss = new WebSocketServer({ server, path: "/ws" })
wss.on("connection", (socket) => {
  const unsubscribe = runEventBus.subscribe((event) => {
    if (socket.readyState === socket.OPEN) {
      socket.send(JSON.stringify(event))
    }
  })
  socket.on("close", unsubscribe)
})

server.listen(PORT, () => {
  console.log(`Quartz backend listening on port ${PORT}`)
})
```

---

### Task 1.4: Database Layer

```typescript
// backend/src/db/client.ts
import { Pool } from "pg"

// Single pool for the whole process — pg manages connection reuse
export const db = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 10,
  idleTimeoutMillis: 30_000,
  connectionTimeoutMillis: 2_000,
})

// Typed query helper — catch SQL errors before they reach the client
export async function query<T extends Record<string, unknown>>(
  text: string,
  params?: unknown[],
): Promise<T[]> {
  const result = await db.query<T>(text, params)
  return result.rows
}
```

```typescript
// backend/src/db/suites.ts
import { query } from "./client"

export interface TestSuite {
  id: string
  name: string
  description: string | null
  workflow: string
  base_url: string
  created_at: string
}

export async function getAllSuites(): Promise<TestSuite[]> {
  return query<TestSuite>(`
    SELECT s.*, v.avg_pass_rate, v.last_run_at, v.pass_rate_trend_7d
    FROM test_suites s
    LEFT JOIN suite_pass_rates v ON v.suite_id = s.id
    ORDER BY s.created_at DESC
  `)
}

export async function getSuiteById(id: string): Promise<TestSuite | null> {
  const rows = await query<TestSuite>(
    "SELECT * FROM test_suites WHERE id = $1",
    [id],
  )
  return rows[0] ?? null
}

export async function createSuite(
  data: Omit<TestSuite, "id" | "created_at" | "updated_at">,
): Promise<TestSuite> {
  const rows = await query<TestSuite>(
    `INSERT INTO test_suites (name, description, workflow, base_url)
     VALUES ($1, $2, $3, $4)
     RETURNING *`,
    [data.name, data.description, data.workflow, data.base_url],
  )
  // Non-null assertion safe here: INSERT ... RETURNING always returns a row
  return rows[0]!
}
```

---

### Day 1 Deliverable Checklist
- [ ] `docker-compose up -d` starts postgres healthy
- [ ] Schema applied with no errors — all tables, indexes, and view created
- [ ] `npm run dev` starts Node server on port 3001
- [ ] `GET /health` returns `{"status": "ok"}`
- [ ] `GET /api/suites` returns an empty array (not a 500)
- [ ] ADR 001-003 written and committed
- [ ] Branch merged to main via PR with conventional commit message

---

## Day 2: Commerce Mock Server and Test Execution Engine
### Node.js, Express, WebSockets — the core product

---

### Git branch: `feat/commerce-mock-and-runner`

---

### Task 2.1: Commerce Mock API

This is the thing you test Quartz against. It simulates real commerce API behavior — including failure modes that engineers need to catch before production.

```typescript
// backend/src/commerce/mock-server.ts
import { Router, Request, Response } from "express"

// Why this exists: the Apple team tests against sandbox environments.
// In development, you need a local mock that behaves like the sandbox
// but gives you full control over failure injection.

export const commerceRouter = Router()

interface PurchaseRequest {
  sku: string
  user_id: string
  storefront: string
  payment_method_id: string
}

interface SubscribeRequest {
  sku: string
  user_id: string
  storefront: string
  offer_code?: string
}

// In-memory state for the mock — reset between test runs
const subscriptions = new Map<string, { sku: string; status: string; expires_at: string }>()
const usedOfferCodes = new Set<string>()

commerceRouter.post("/purchase", (req: Request, res: Response) => {
  const body = req.body as PurchaseRequest

  // Simulate edge cases that the real API can return
  if (!body.sku || !body.user_id)
    return res.status(400).json({ error: "missing_required_field", field: !body.sku ? "sku" : "user_id" })

  if (body.storefront === "XX")  // invalid storefront
    return res.status(422).json({ error: "invalid_storefront", storefront: body.storefront })

  if (body.payment_method_id === "pm_declined")
    return res.status(402).json({ error: "payment_declined", code: "do_not_honor" })

  if (body.payment_method_id === "pm_expired")
    return res.status(402).json({ error: "payment_declined", code: "expired_card" })

  const receipt = {
    transaction_id: `txn_${Date.now()}`,
    sku: body.sku,
    user_id: body.user_id,
    storefront: body.storefront,
    amount: 0.99,
    currency: storefront_currencies[body.storefront] ?? "USD",
    purchased_at: new Date().toISOString(),
    entitlement: `ent_${body.sku}`,
  }
  return res.status(201).json(receipt)
})

commerceRouter.post("/subscribe", (req: Request, res: Response) => {
  const body = req.body as SubscribeRequest

  if (!body.sku || !body.user_id)
    return res.status(400).json({ error: "missing_required_field" })

  if (body.offer_code !== undefined) {
    if (!VALID_OFFER_CODES.has(body.offer_code))
      return res.status(422).json({ error: "invalid_offer_code", code: body.offer_code })
    if (usedOfferCodes.has(body.offer_code))
      return res.status(409).json({ error: "offer_code_already_redeemed", code: body.offer_code })
    usedOfferCodes.add(body.offer_code)
  }

  const expiresAt = new Date(Date.now() + 30 * 24 * 60 * 60 * 1000)
  const subscription = {
    subscription_id: `sub_${Date.now()}`,
    sku: body.sku,
    user_id: body.user_id,
    status: "active",
    current_period_start: new Date().toISOString(),
    current_period_end: expiresAt.toISOString(),
    auto_renew: true,
  }
  subscriptions.set(`${body.user_id}:${body.sku}`, {
    sku: body.sku,
    status: "active",
    expires_at: expiresAt.toISOString(),
  })
  return res.status(201).json(subscription)
})

commerceRouter.get("/entitlements/:user_id", (req: Request, res: Response) => {
  const userId = req.params["user_id"]!
  const userSubs = Array.from(subscriptions.entries())
    .filter(([key]) => key.startsWith(`${userId}:`))
    .map(([, sub]) => sub)
  return res.json({ user_id: userId, entitlements: userSubs })
})

commerceRouter.post("/refund", (req: Request, res: Response) => {
  const { transaction_id } = req.body as { transaction_id: string }
  if (!transaction_id)
    return res.status(400).json({ error: "missing_transaction_id" })
  if (transaction_id === "txn_nonrefundable")
    return res.status(422).json({ error: "outside_refund_window" })
  return res.json({ refund_id: `ref_${Date.now()}`, status: "approved" })
})

// Reset mock state — called between test runs
commerceRouter.post("/reset", (_req, res) => {
  subscriptions.clear()
  usedOfferCodes.clear()
  res.json({ message: "mock state reset" })
})

const VALID_OFFER_CODES = new Set(["TRIAL3M", "STUDENT50", "HOLIDAY2025"])
const storefront_currencies: Record<string, string> = {
  US: "USD", GB: "GBP", JP: "JPY", EU: "EUR", CA: "CAD",
}
```

---

### Task 2.2: Test Execution Engine

```typescript
// backend/src/services/test-runner.service.ts
import axios, { AxiosError } from "axios"
import { query } from "../db/client"
import { runEventBus } from "./run-event-bus"

interface TestCase {
  id: string
  name: string
  method: string
  path: string
  headers: Record<string, string>
  body: unknown
  expected_status: number
  expected_body: unknown
  expected_contains: string[]
}

export async function executeRun(runId: string, suiteBaseUrl: string): Promise<void> {
  // Mark run as started
  await query("UPDATE test_runs SET status = 'running', started_at = NOW() WHERE id = $1", [runId])

  const cases = await query<TestCase>(`
    SELECT tc.* FROM test_cases tc
    JOIN test_runs tr ON tr.suite_id = tc.suite_id
    WHERE tr.id = $1
    ORDER BY tc.created_at ASC
  `, [runId])

  // Reset commerce mock state before the run so tests are idempotent
  // Idempotency is critical: running the same suite twice must give the same result
  await axios.post(`${suiteBaseUrl}/reset`).catch(() => null)

  let passed = 0, failed = 0

  for (const testCase of cases) {
    runEventBus.emit({ type: "case_started", runId, caseId: testCase.id, caseName: testCase.name })
    const result = await executeCase(testCase, suiteBaseUrl)

    await query(`
      INSERT INTO test_results
        (run_id, case_id, status, actual_status, actual_body, actual_headers,
         duration_ms, request_sent, error_message)
      VALUES ($1,$2,$3,$4,$5,$6,$7,$8,$9)
    `, [
      runId, testCase.id, result.status, result.actual_status,
      JSON.stringify(result.actual_body), JSON.stringify(result.actual_headers),
      result.duration_ms, JSON.stringify(result.request_sent), result.error_message,
    ])

    if (result.status === "passed") passed++
    else failed++

    runEventBus.emit({ type: "case_completed", runId, caseId: testCase.id, result })
  }

  await query(`
    UPDATE test_runs SET
      status = 'completed', completed_at = NOW(),
      total_cases = $2, passed = $3, failed = $4,
      duration_ms = EXTRACT(EPOCH FROM (NOW() - started_at)) * 1000
    WHERE id = $1
  `, [runId, cases.length, passed, failed])

  runEventBus.emit({ type: "run_completed", runId, passed, failed, total: cases.length })
}

async function executeCase(
  testCase: TestCase,
  baseUrl: string,
): Promise<{
  status: "passed" | "failed" | "error"
  actual_status: number | null
  actual_body: unknown
  actual_headers: Record<string, string>
  duration_ms: number
  request_sent: unknown
  error_message: string | null
}> {
  const url = `${baseUrl}${testCase.path}`
  const start = Date.now()

  try {
    const response = await axios({
      method: testCase.method,
      url,
      headers: testCase.headers,
      data: testCase.body,
      validateStatus: () => true,  // don't throw on 4xx/5xx — we handle them
      timeout: 10_000,
    })

    const duration_ms = Date.now() - start
    const passed = evaluateResult(testCase, response.status, response.data)

    return {
      status: passed ? "passed" : "failed",
      actual_status: response.status,
      actual_body: response.data as unknown,
      actual_headers: response.headers as Record<string, string>,
      duration_ms,
      request_sent: { method: testCase.method, url, body: testCase.body },
      error_message: null,
    }
  } catch (err) {
    const duration_ms = Date.now() - start
    const message = err instanceof AxiosError ? err.message : "unknown error"
    return {
      status: "error",
      actual_status: null,
      actual_body: null,
      actual_headers: {},
      duration_ms,
      request_sent: { method: testCase.method, url, body: testCase.body },
      error_message: message,
    }
  }
}

function evaluateResult(
  testCase: TestCase,
  actualStatus: number,
  actualBody: unknown,
): boolean {
  if (actualStatus !== testCase.expected_status) return false

  if (testCase.expected_contains && Array.isArray(testCase.expected_contains)) {
    const bodyStr = JSON.stringify(actualBody)
    for (const expected of testCase.expected_contains) {
      if (!bodyStr.includes(expected)) return false
    }
  }

  return true
}
```

```typescript
// backend/src/services/run-event-bus.ts
// Simple in-process event bus — decouples the runner from WebSocket management
type RunEvent =
  | { type: "case_started"; runId: string; caseId: string; caseName: string }
  | { type: "case_completed"; runId: string; caseId: string; result: unknown }
  | { type: "run_completed"; runId: string; passed: number; failed: number; total: number }

type Subscriber = (event: RunEvent) => void

class RunEventBus {
  private subscribers: Set<Subscriber> = new Set()

  emit(event: RunEvent): void {
    this.subscribers.forEach((sub) => sub(event))
  }

  subscribe(fn: Subscriber): () => void {
    this.subscribers.add(fn)
    return () => this.subscribers.delete(fn)
  }
}

export const runEventBus = new RunEventBus()
```

---

### Task 2.3: Tests for the Runner

Test the runner before the frontend exists. Professional discipline: test at the service layer, not just end-to-end.

```typescript
// backend/tests/integration/test-runner.test.ts
import { createApp } from "../../src/app"
import supertest from "supertest"

const app = createApp()
const request = supertest(app)

describe("Commerce Mock API", () => {
  beforeEach(async () => {
    await request.post("/api/commerce/reset")
  })

  test("POST /purchase returns 201 with valid input", async () => {
    const res = await request.post("/api/commerce/purchase").send({
      sku: "com.apple.music.individual.monthly",
      user_id: "user_001",
      storefront: "US",
      payment_method_id: "pm_valid",
    })
    expect(res.status).toBe(201)
    expect(res.body).toHaveProperty("transaction_id")
    expect(res.body).toHaveProperty("entitlement")
  })

  test("POST /purchase returns 402 when card is declined", async () => {
    const res = await request.post("/api/commerce/purchase").send({
      sku: "com.apple.music.individual.monthly",
      user_id: "user_001",
      storefront: "US",
      payment_method_id: "pm_declined",
    })
    expect(res.status).toBe(402)
    expect(res.body.error).toBe("payment_declined")
    expect(res.body.code).toBe("do_not_honor")
  })

  test("offer code cannot be redeemed twice", async () => {
    const payload = {
      sku: "com.apple.music.family.monthly",
      user_id: "user_002",
      storefront: "US",
      offer_code: "TRIAL3M",
    }
    await request.post("/api/commerce/subscribe").send(payload).expect(201)
    const second = await request.post("/api/commerce/subscribe").send(payload)
    expect(second.status).toBe(409)
    expect(second.body.error).toBe("offer_code_already_redeemed")
  })
})
```

---

### Day 2 Deliverable Checklist
- [ ] All 5 commerce endpoints working: purchase, subscribe, entitlements, refund, reset
- [ ] Declined card, invalid storefront, expired offer code — all return correct error responses
- [ ] Test runner executes a manually constructed test suite end-to-end
- [ ] Results written to `test_results` table with full request/response captured
- [ ] WebSocket emits `case_completed` events visible in `wscat` or browser console
- [ ] Integration tests pass: `npm test`

---

## Day 3: React Frontend
### Pure React 18, Vite, TypeScript strict, WebSocket integration

---

### Git branch: `feat/react-frontend`

```bash
cd frontend
npm create vite@latest . -- --template react-ts
npm install axios react-router-dom @tanstack/react-query date-fns lucide-react
npm install --save-dev vitest @vitest/ui @testing-library/react @testing-library/jest-dom jsdom
```

```json
// frontend/tsconfig.json — strict mode, no exceptions
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "exactOptionalPropertyTypes": true
  }
}
```

---

### Task 3.1: API Client and Types

```typescript
// frontend/src/lib/api.ts
import axios from "axios"

const client = axios.create({
  baseURL: import.meta.env.VITE_API_URL ?? "http://localhost:3001",
  headers: { "Content-Type": "application/json" },
})

export interface TestSuite {
  id: string
  name: string
  description: string | null
  workflow: string
  base_url: string
  avg_pass_rate: number | null
  last_run_at: string | null
  pass_rate_trend_7d: number | null
  created_at: string
}

export interface TestCase {
  id: string
  suite_id: string
  name: string
  method: string
  path: string
  headers: Record<string, string>
  body: unknown
  expected_status: number
  expected_contains: string[]
  ai_generated: boolean
}

export interface TestRun {
  id: string
  suite_id: string
  status: "pending" | "running" | "completed" | "failed"
  total_cases: number
  passed: number
  failed: number
  duration_ms: number | null
  created_at: string
}

export const api = {
  suites: {
    list: () => client.get<TestSuite[]>("/api/suites").then(r => r.data),
    create: (data: Omit<TestSuite, "id" | "created_at" | "avg_pass_rate" | "last_run_at" | "pass_rate_trend_7d">) =>
      client.post<TestSuite>("/api/suites", data).then(r => r.data),
  },
  cases: {
    list: (suiteId: string) =>
      client.get<TestCase[]>(`/api/suites/${suiteId}/cases`).then(r => r.data),
    create: (suiteId: string, data: Omit<TestCase, "id" | "suite_id" | "ai_generated">) =>
      client.post<TestCase>(`/api/suites/${suiteId}/cases`, data).then(r => r.data),
  },
  runs: {
    trigger: (suiteId: string) =>
      client.post<TestRun>(`/api/runs`, { suite_id: suiteId }).then(r => r.data),
    list: (suiteId: string) =>
      client.get<TestRun[]>(`/api/runs?suite_id=${suiteId}`).then(r => r.data),
  },
  ai: {
    generateCases: (suiteId: string, spec: string) =>
      client.post<TestCase[]>("/api/ai/generate-cases", { suite_id: suiteId, spec }).then(r => r.data),
    analyzeFailure: (resultId: string) =>
      client.post<{ reason: string; suggestion: string }>("/api/ai/analyze-failure", { result_id: resultId })
        .then(r => r.data),
  },
}
```

---

### Task 3.2: Live Run Console with WebSocket

This is the most impressive UI feature. Real-time test output streaming to the browser — the same UX engineers expect from a CI dashboard.

```typescript
// frontend/src/hooks/useRunStream.ts
import { useState, useEffect, useCallback } from "react"

type RunEvent =
  | { type: "case_started"; caseId: string; caseName: string }
  | { type: "case_completed"; caseId: string; result: { status: string; duration_ms: number } }
  | { type: "run_completed"; passed: number; failed: number; total: number }

export function useRunStream(runId: string | null) {
  const [events, setEvents] = useState<RunEvent[]>([])
  const [isComplete, setIsComplete] = useState(false)

  useEffect(() => {
    if (!runId) return

    const ws = new WebSocket(`ws://localhost:3001/ws`)
    ws.onmessage = (msg) => {
      const event = JSON.parse(msg.data as string) as RunEvent & { runId: string }
      if (event.runId !== runId) return

      setEvents(prev => [...prev, event])
      if (event.type === "run_completed") setIsComplete(true)
    }
    ws.onerror = (err) => console.error("WS error", err)

    return () => ws.close()
  }, [runId])

  const reset = useCallback(() => {
    setEvents([])
    setIsComplete(false)
  }, [])

  return { events, isComplete, reset }
}
```

```tsx
// frontend/src/pages/RunConsolePage.tsx
import { useState } from "react"
import { useParams } from "react-router-dom"
import { useMutation } from "@tanstack/react-query"
import { CheckCircle, XCircle, Clock, Play } from "lucide-react"
import { api, TestRun } from "../lib/api"
import { useRunStream } from "../hooks/useRunStream"

export function RunConsolePage() {
  const { suiteId } = useParams<{ suiteId: string }>()
  const [activeRunId, setActiveRunId] = useState<string | null>(null)
  const { events, isComplete, reset } = useRunStream(activeRunId)

  const triggerMutation = useMutation({
    mutationFn: () => api.runs.trigger(suiteId!),
    onSuccess: (run: TestRun) => {
      reset()
      setActiveRunId(run.id)
    },
  })

  return (
    <div className="run-console">
      <header>
        <h1>Test Run Console</h1>
        <button
          onClick={() => triggerMutation.mutate()}
          disabled={triggerMutation.isPending || (activeRunId !== null && !isComplete)}
          className="btn-primary"
        >
          <Play size={16} />
          {triggerMutation.isPending ? "Starting..." : "Run Suite"}
        </button>
      </header>

      <div className="event-log">
        {events.map((event, i) => (
          <div key={i} className={`event event--${event.type}`}>
            {event.type === "case_started" && (
              <span className="event-running">
                <Clock size={14} /> Running: {event.caseName}
              </span>
            )}
            {event.type === "case_completed" && (
              <span className={`event-result event-result--${event.result.status}`}>
                {event.result.status === "passed"
                  ? <CheckCircle size={14} />
                  : <XCircle size={14} />}
                {event.result.status.toUpperCase()} — {event.result.duration_ms}ms
              </span>
            )}
            {event.type === "run_completed" && (
              <div className="event-summary">
                Run complete: {event.passed}/{event.total} passed
              </div>
            )}
          </div>
        ))}
      </div>
    </div>
  )
}
```

---

### Task 3.3: Suite Dashboard

```tsx
// frontend/src/pages/DashboardPage.tsx
import { useQuery } from "@tanstack/react-query"
import { api, TestSuite } from "../lib/api"
import { Link } from "react-router-dom"
import { TrendingUp, TrendingDown, Minus } from "lucide-react"

export function DashboardPage() {
  const { data: suites, isLoading } = useQuery({
    queryKey: ["suites"],
    queryFn: api.suites.list,
    refetchInterval: 30_000,
  })

  if (isLoading) return <div className="loading">Loading suites...</div>

  return (
    <div className="dashboard">
      <h1>Quartz — Commerce Test Suites</h1>
      <div className="suite-grid">
        {suites?.map(suite => (
          <SuiteCard key={suite.id} suite={suite} />
        ))}
      </div>
    </div>
  )
}

function SuiteCard({ suite }: { suite: TestSuite }) {
  const trend = suite.pass_rate_trend_7d
  const TrendIcon = trend == null ? Minus : trend > 0 ? TrendingUp : TrendingDown
  const trendColor = trend == null ? "gray" : trend > 0 ? "green" : "red"

  return (
    <div className="suite-card">
      <div className="suite-card__header">
        <span className={`workflow-badge workflow-badge--${suite.workflow}`}>
          {suite.workflow.replace("_", " ")}
        </span>
        <TrendIcon size={16} color={trendColor} />
      </div>
      <h3>{suite.name}</h3>
      <p>{suite.description}</p>
      <div className="suite-card__metrics">
        <span className="pass-rate">
          {suite.avg_pass_rate != null ? `${suite.avg_pass_rate}% pass rate` : "No runs yet"}
        </span>
        {suite.last_run_at && (
          <span className="last-run">
            Last run {new Date(suite.last_run_at).toLocaleDateString()}
          </span>
        )}
      </div>
      <div className="suite-card__actions">
        <Link to={`/suite/${suite.id}`} className="btn-secondary">View Cases</Link>
        <Link to={`/suite/${suite.id}/run`} className="btn-primary">Run</Link>
      </div>
    </div>
  )
}
```

---

### Day 3 Deliverable Checklist
- [ ] `npm run dev` starts Vite on port 5173 with zero TypeScript errors
- [ ] Dashboard renders suite cards with pass rate from the SQL view
- [ ] Run Console shows live events as they stream over WebSocket
- [ ] Passed tests show green checkmark, failed tests show red X in real time
- [ ] React Query caches suite list, refetches every 30 seconds
- [ ] Vitest unit tests for `useRunStream` hook and `evaluateResult` utility

---

## Day 4: LLM Integration — Anthropic API
### The feature that makes this an Apple SDET interview win

---

### Git branch: `feat/ai-integration`

This is the feature that separates Quartz from a standard test framework. An engineer pastes in an API endpoint description and the LLM generates edge-case test cases covering scenarios a human would likely miss: currency boundary conditions, state machine violations, concurrent redemption attempts.

---

### Task 4.1: Anthropic API Service

```typescript
// backend/src/services/ai.service.ts
import Anthropic from "@anthropic-ai/sdk"

const client = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY })

// Tool definition — forces Claude to return structured JSON, not markdown
const generateCasesTool: Anthropic.Tool = {
  name: "create_test_cases",
  description: "Create a structured list of test cases for a commerce API endpoint",
  input_schema: {
    type: "object" as const,
    properties: {
      test_cases: {
        type: "array",
        items: {
          type: "object",
          properties: {
            name:             { type: "string", description: "Short descriptive test name" },
            description:      { type: "string", description: "What this test verifies" },
            method:           { type: "string", enum: ["GET","POST","PUT","PATCH","DELETE"] },
            path:             { type: "string" },
            body:             { type: "object" },
            expected_status:  { type: "integer" },
            expected_contains:{ type: "array", items: { type: "string" } },
            tags:             { type: "array", items: { type: "string" } },
          },
          required: ["name", "method", "path", "expected_status"],
        },
      },
    },
    required: ["test_cases"],
  },
}

export interface GeneratedTestCase {
  name: string
  description: string
  method: string
  path: string
  body: unknown
  expected_status: number
  expected_contains: string[]
  tags: string[]
}

export async function generateTestCases(
  workflow: string,
  endpointSpec: string,
): Promise<GeneratedTestCase[]> {
  const systemPrompt = `You are an expert software engineer in test specializing in e-commerce API testing.
You generate comprehensive test cases that cover:
- Happy path scenarios
- Validation failures (missing fields, invalid formats)
- Business rule violations (expired offers, duplicate redemptions, invalid storefronts)
- State machine edge cases (subscribing when already subscribed, refunding after grace period)
- Boundary conditions (minimum/maximum amounts, exactly-expired timestamps)
- Currency and locale edge cases

Focus on the commerce domain: purchases, subscriptions, offer redemption, refunds, entitlements.
Be specific about expected HTTP status codes — 400 for validation, 402 for payment issues,
409 for conflicts, 422 for business rule violations.`

  const userMessage = `Generate test cases for this ${workflow} endpoint:

${endpointSpec}

Generate at least 8 test cases. Include at least 3 failure scenarios.
Focus on edge cases that are easy to miss but critical for commerce reliability.`

  const response = await client.messages.create({
    model: "claude-sonnet-4-6",
    max_tokens: 4096,
    system: systemPrompt,
    tools: [generateCasesTool],
    tool_choice: { type: "tool", name: "create_test_cases" },
    messages: [{ role: "user", content: userMessage }],
  })

  const toolUse = response.content.find(b => b.type === "tool_use")
  if (!toolUse || toolUse.type !== "tool_use") {
    throw new Error("LLM did not call the expected tool")
  }

  const result = toolUse.input as { test_cases: GeneratedTestCase[] }
  return result.test_cases
}

export async function analyzeFailure(
  testCase: { name: string; method: string; path: string; body: unknown; expected_status: number },
  actual: { status: number; body: unknown; error_message: string | null },
): Promise<{ reason: string; suggestion: string }> {
  const response = await client.messages.create({
    model: "claude-haiku-4-5-20251001",  // haiku for fast, cheap analysis
    max_tokens: 512,
    messages: [{
      role: "user",
      content: `A commerce API test failed. Explain why and suggest a fix.

Test: "${testCase.name}"
Request: ${testCase.method} ${testCase.path}
Request body: ${JSON.stringify(testCase.body, null, 2)}
Expected status: ${testCase.expected_status}
Actual status: ${actual.status}
Actual response: ${JSON.stringify(actual.body, null, 2)}
${actual.error_message ? `Error: ${actual.error_message}` : ""}

Respond in JSON: {"reason": "...", "suggestion": "..."}`
    }],
  })

  const text = response.content[0]
  if (text?.type !== "text") throw new Error("unexpected response type")

  try {
    // Extract JSON from response — handles cases where LLM wraps it in markdown
    const match = text.text.match(/\{[\s\S]*\}/)
    if (!match) throw new Error("no JSON found")
    return JSON.parse(match[0]) as { reason: string; suggestion: string }
  } catch {
    return { reason: text.text, suggestion: "Review the test case and API documentation." }
  }
}
```

---

### Task 4.2: AI Routes

```typescript
// backend/src/routes/ai.ts
import { Router } from "express"
import { generateTestCases, analyzeFailure } from "../services/ai.service"
import { getSuiteById } from "../db/suites"
import { query } from "../db/client"

export const aiRouter = Router()

aiRouter.post("/generate-cases", async (req, res) => {
  const { suite_id, spec } = req.body as { suite_id: string; spec: string }

  if (!suite_id || !spec)
    return res.status(400).json({ error: "suite_id and spec are required" })

  const suite = await getSuiteById(suite_id)
  if (!suite) return res.status(404).json({ error: "suite not found" })

  const generated = await generateTestCases(suite.workflow, spec)

  // Insert generated cases into the database, marked as AI-generated
  const inserted = await Promise.all(
    generated.map(c => query(`
      INSERT INTO test_cases
        (suite_id, name, description, method, path, body, expected_status,
         expected_contains, tags, ai_generated)
      VALUES ($1,$2,$3,$4,$5,$6,$7,$8,$9,true)
      RETURNING *
    `, [suite_id, c.name, c.description, c.method, c.path,
        JSON.stringify(c.body), c.expected_status,
        c.expected_contains, c.tags]))
  )

  return res.status(201).json(inserted.flat())
})

aiRouter.post("/analyze-failure", async (req, res) => {
  const { result_id } = req.body as { result_id: string }

  const rows = await query(`
    SELECT r.*, tc.name, tc.method, tc.path, tc.body, tc.expected_status
    FROM test_results r
    JOIN test_cases tc ON tc.id = r.case_id
    WHERE r.id = $1
  `, [result_id])

  const result = rows[0]
  if (!result) return res.status(404).json({ error: "result not found" })

  const analysis = await analyzeFailure(
    { name: result.name, method: result.method, path: result.path,
      body: result.body, expected_status: result.expected_status },
    { status: result.actual_status, body: result.actual_body,
      error_message: result.error_message },
  )

  // Store analysis on the result for future reference
  await query(
    "UPDATE test_results SET failure_reason=$1, ai_suggestion=$2 WHERE id=$3",
    [analysis.reason, analysis.suggestion, result_id],
  )

  return res.json(analysis)
})
```

---

### Task 4.3: AI Panel in React

```tsx
// frontend/src/components/AiGeneratorPanel.tsx
import { useState } from "react"
import { useMutation, useQueryClient } from "@tanstack/react-query"
import { Sparkles, Loader } from "lucide-react"
import { api } from "../lib/api"

interface Props {
  suiteId: string
  workflow: string
}

const SPEC_PLACEHOLDER = `POST /subscribe

Request body:
{
  "sku": "com.apple.music.individual.monthly",
  "user_id": "string",
  "storefront": "US",
  "offer_code": "optional string"
}

Response 201 on success:
{
  "subscription_id": "string",
  "status": "active",
  "current_period_end": "ISO8601 timestamp"
}

Business rules:
- offer_code must be valid and unused
- user cannot subscribe to the same SKU twice while active`

export function AiGeneratorPanel({ suiteId, workflow }: Props) {
  const [spec, setSpec] = useState("")
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: () => api.ai.generateCases(suiteId, spec),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["cases", suiteId] })
      setSpec("")
    },
  })

  return (
    <div className="ai-panel">
      <h3>
        <Sparkles size={16} />
        AI Test Case Generator
      </h3>
      <p className="ai-panel__description">
        Describe the {workflow} endpoint. The AI will generate edge-case test
        scenarios covering validation failures, business rule violations, and
        state machine edge cases.
      </p>
      <textarea
        value={spec}
        onChange={(e) => setSpec(e.target.value)}
        placeholder={SPEC_PLACEHOLDER}
        rows={12}
        className="ai-panel__input"
      />
      <button
        onClick={() => mutation.mutate()}
        disabled={!spec.trim() || mutation.isPending}
        className="btn-primary"
      >
        {mutation.isPending ? (
          <><Loader size={14} className="spin" /> Generating...</>
        ) : (
          <><Sparkles size={14} /> Generate Test Cases</>
        )}
      </button>
      {mutation.isSuccess && (
        <p className="ai-panel__success">
          Generated {mutation.data.length} test cases. Review them in the cases list below.
        </p>
      )}
      {mutation.isError && (
        <p className="ai-panel__error">Generation failed. Check your API key and try again.</p>
      )}
    </div>
  )
}
```

---

### Day 4 Deliverable Checklist
- [ ] `POST /api/ai/generate-cases` calls Anthropic API and returns structured test cases
- [ ] Generated cases are stored in the DB with `ai_generated = true`
- [ ] `POST /api/ai/analyze-failure` returns a plain-English explanation and suggestion
- [ ] Claude `tool_use` forces structured JSON output — no markdown parsing hacks
- [ ] AI panel in React shows generation status, success count, and error state
- [ ] Rate limiting on the AI route: max 10 requests per minute (add express-rate-limit)
- [ ] API key is in `.env`, never logged, never returned to the client

---

## Day 5: Shell Scripts, CI/CD, Python CLI, Polish
### Bash, Python, GitHub Actions, Docker image

---

### Git branch: `feat/ci-and-cli`

---

### Task 5.1: Bash CLI Runner

This is the CI-compatible interface. Engineers run this from the terminal and from GitHub Actions. It is also your answer to "familiarity with Shell scripting."

```bash
#!/usr/bin/env bash
# scripts/run-suite.sh
# Runs a Quartz test suite from the command line and exits non-zero on failure
# Usage: ./scripts/run-suite.sh <suite_id> [--fail-fast] [--format json|table]

set -euo pipefail

SUITE_ID="${1:?Usage: run-suite.sh <suite_id> [--fail-fast] [--format json|table]}"
FAIL_FAST=false
FORMAT="table"
API_BASE="${QUARTZ_API:-http://localhost:3001}"

# Parse optional flags
shift
while [[ $# -gt 0 ]]; do
  case "$1" in
    --fail-fast) FAIL_FAST=true ;;
    --format)    FORMAT="$2"; shift ;;
    *) echo "Unknown flag: $1" >&2; exit 1 ;;
  esac
  shift
done

echo "==> Triggering test run for suite: ${SUITE_ID}"
RUN_RESPONSE=$(curl -sf -X POST "${API_BASE}/api/runs" \
  -H "Content-Type: application/json" \
  -d "{\"suite_id\": \"${SUITE_ID}\"}")

RUN_ID=$(echo "${RUN_RESPONSE}" | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")
echo "==> Run started: ${RUN_ID}"

# Poll until complete
MAX_WAIT=300
ELAPSED=0
while true; do
  STATUS=$(curl -sf "${API_BASE}/api/runs/${RUN_ID}" \
    | python3 -c "import sys,json; print(json.load(sys.stdin)['status'])")

  if [[ "${STATUS}" == "completed" || "${STATUS}" == "failed" ]]; then
    break
  fi

  if [[ ${ELAPSED} -ge ${MAX_WAIT} ]]; then
    echo "ERROR: Run timed out after ${MAX_WAIT}s" >&2
    exit 2
  fi

  sleep 2
  ELAPSED=$((ELAPSED + 2))
  echo "  ... ${STATUS} (${ELAPSED}s)"
done

# Fetch and display results
RESULT=$(curl -sf "${API_BASE}/api/runs/${RUN_ID}")
PASSED=$(echo "${RESULT}" | python3 -c "import sys,json; print(json.load(sys.stdin)['passed'])")
FAILED=$(echo "${RESULT}" | python3 -c "import sys,json; print(json.load(sys.stdin)['failed'])")
TOTAL=$(echo "${RESULT}" | python3 -c "import sys,json; print(json.load(sys.stdin)['total_cases'])")

echo ""
echo "==> Run complete: ${PASSED}/${TOTAL} passed, ${FAILED} failed"

if [[ "${FAILED}" -gt 0 ]]; then
  echo "FAILED" >&2
  exit 1
fi
echo "PASSED"
exit 0
```

---

### Task 5.2: Python Analysis Script

Python is a minimum qualification. Demonstrate it in a natural way: a CLI tool that queries the PostgreSQL database directly and produces a test trend report.

```python
#!/usr/bin/env python3
# scripts/trend_report.py
# Usage: python3 scripts/trend_report.py --days 30
# Reads from the Quartz database and prints a pass rate trend report

import argparse
import os
import sys
from datetime import datetime, timezone, timedelta
import psycopg2
from psycopg2.extras import RealDictCursor

def generate_report(days: int) -> None:
    conn = psycopg2.connect(os.environ["DATABASE_URL"])
    since = datetime.now(timezone.utc) - timedelta(days=days)

    with conn.cursor(cursor_factory=RealDictCursor) as cur:
        cur.execute("""
            SELECT
                s.name                                              AS suite,
                s.workflow,
                COUNT(r.id)                                         AS runs,
                ROUND(AVG(r.passed::NUMERIC / NULLIF(r.total_cases,0)) * 100, 1)
                                                                    AS pass_rate,
                MIN(r.passed::NUMERIC / NULLIF(r.total_cases,0))    AS worst_run,
                MAX(r.duration_ms)                                  AS slowest_ms
            FROM test_suites s
            JOIN test_runs r ON r.suite_id = s.id
            WHERE r.status = 'completed'
              AND r.created_at >= %s
            GROUP BY s.name, s.workflow
            ORDER BY pass_rate ASC
        """, (since,))

        rows = cur.fetchall()

    if not rows:
        print(f"No completed runs in the last {days} days.")
        return

    print(f"\nQuartz Trend Report — Last {days} days")
    print("=" * 70)
    print(f"{'Suite':<30} {'Workflow':<20} {'Runs':>4} {'Pass%':>6} {'Slowest':>8}")
    print("-" * 70)
    for row in rows:
        flag = " ⚠" if (row["pass_rate"] or 0) < 90 else ""
        print(
            f"{row['suite']:<30} {row['workflow']:<20} {row['runs']:>4} "
            f"{row['pass_rate'] or 0:>5.1f}% {row['slowest_ms'] or 0:>7}ms{flag}"
        )
    print()


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Quartz trend report")
    parser.add_argument("--days", type=int, default=30)
    args = parser.parse_args()
    generate_report(args.days)
```

---

### Task 5.3: GitHub Actions CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  backend:
    name: Backend — Lint, Test, Security
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: quartz_test
          POSTGRES_USER: quartz
          POSTGRES_PASSWORD: quartz_dev
        ports: ["5432:5432"]
        options: --health-cmd pg_isready --health-interval 5s --health-retries 5

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
          cache-dependency-path: backend/package-lock.json

      - run: cd backend && npm ci

      - name: TypeScript typecheck
        run: cd backend && npx tsc --noEmit

      - name: Apply test schema
        run: |
          PGPASSWORD=quartz_dev psql -h localhost -U quartz -d quartz_test \
            -f backend/src/db/schema.sql

      - name: Run tests
        run: cd backend && npm test -- --coverage
        env:
          DATABASE_URL: postgresql://quartz:quartz_dev@localhost:5432/quartz_test
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}

      - name: Check coverage threshold
        run: cd backend && npx jest --coverage --coverageThreshold='{"global":{"lines":75}}'
        env:
          DATABASE_URL: postgresql://quartz:quartz_dev@localhost:5432/quartz_test

      - name: Audit dependencies
        run: cd backend && npm audit --audit-level=high

  frontend:
    name: Frontend — Lint, Test, Typecheck
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
          cache-dependency-path: frontend/package-lock.json

      - run: cd frontend && npm ci

      - name: TypeScript typecheck
        run: cd frontend && npx tsc --noEmit

      - name: ESLint
        run: cd frontend && npx eslint src/

      - name: Unit tests
        run: cd frontend && npm test

  run-suite-smoke:
    name: Smoke — CLI runner against mock
    needs: [backend]
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: quartz_test
          POSTGRES_USER: quartz
          POSTGRES_PASSWORD: quartz_dev
        ports: ["5432:5432"]
        options: --health-cmd pg_isready --health-interval 5s --health-retries 5

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Start backend
        run: |
          cd backend && npm ci
          PGPASSWORD=quartz_dev psql -h localhost -U quartz -d quartz_test \
            -f src/db/schema.sql
          DATABASE_URL=postgresql://quartz:quartz_dev@localhost:5432/quartz_test \
            npm run dev &
          sleep 5
        env:
          ANTHROPIC_API_KEY: dummy_for_smoke_test

      - name: Create and run a test suite via CLI
        run: |
          # Create a suite via API
          SUITE_ID=$(curl -sf -X POST http://localhost:3001/api/suites \
            -H "Content-Type: application/json" \
            -d '{"name":"Smoke Suite","workflow":"purchase","base_url":"http://localhost:3001/api/commerce"}' \
            | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")

          # Add a test case
          curl -sf -X POST "http://localhost:3001/api/suites/${SUITE_ID}/cases" \
            -H "Content-Type: application/json" \
            -d '{"name":"valid purchase","method":"POST","path":"/purchase","expected_status":201,"body":{"sku":"com.apple.music.individual.monthly","user_id":"ci_user","storefront":"US","payment_method_id":"pm_valid"}}'

          # Run it
          bash scripts/run-suite.sh "${SUITE_ID}"
```

---

### Task 5.4: Docker Image for the Backend

```dockerfile
# backend/Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npx tsc --noEmit && npm run build

FROM node:20-alpine AS runtime
WORKDIR /app
RUN addgroup -S quartz && adduser -S quartz -G quartz
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/src/db/schema.sql ./src/db/schema.sql
USER quartz
EXPOSE 3001
CMD ["node", "dist/server.js"]
```

---

### Day 5 Deliverable Checklist
- [ ] `bash scripts/run-suite.sh <id>` triggers a run, polls, exits 0 on pass / 1 on fail
- [ ] `python3 scripts/trend_report.py --days 30` prints a formatted trend table
- [ ] GitHub Actions CI runs on every PR: typecheck + tests + audit + coverage gate
- [ ] Smoke job creates a real suite and runs it end-to-end in CI
- [ ] Docker image builds without errors
- [ ] `npm audit` shows zero high-severity vulnerabilities
- [ ] `.env.example` committed with all required keys documented (no values)

---

## Interview Preparation — Talking Points for Each Question

### "Tell me about a project you've built."
"I built Quartz, a commerce API test harness with an AI-powered test case generator. Engineers paste in an endpoint spec — for example, a subscription endpoint with business rules about offer codes — and the LLM generates edge cases: what happens if the offer code was already redeemed, what if the user is already subscribed to the same SKU, what does the API return on an invalid storefront code. The test runner executes those cases, streams live results to a React dashboard over WebSockets, and when a test fails, a second LLM call explains the failure in plain English and suggests a fix. The CI pipeline runs the full suite on every PR and fails the build if pass rate drops."

### "Walk me through the AI integration."
"I used the Anthropic API with tool_use — structured output that forces the LLM to return machine-parseable JSON instead of markdown. The tool schema defines the shape of a test case: method, path, expected status code, expected response body fields. The system prompt includes the commerce domain vocabulary — offer codes, entitlements, storefront codes, grace periods — so the generated cases use the right terminology and cover commerce-specific failure modes. I also use a separate haiku call for failure analysis because it's faster and cheaper — I don't need a lot of reasoning capability to explain a 409 conflict response."

### "How would you test a subscription API?"
"Start with the state machine. A subscription has states: pending, active, grace_period, expired, churned. The happy path is easy — test that. The interesting cases are state transitions: what happens if you try to subscribe while already active? What does the API return during the grace period? Does renewing during grace period reset the billing date or extend from the original date? I'd also test storefront-specific behavior, because subscription pricing varies by region. And offer code validation — invalid code, expired code, already-redeemed code, code for a different SKU."

### "What CI/CD experience do you have?"
"The Quartz CI pipeline has three jobs: backend tests with coverage gate at 75%, frontend typecheck and lint, and a full smoke test that starts the backend server and runs a real end-to-end test suite via the CLI runner. The coverage gate is enforced in CI, not locally — if it only lives in a pre-commit hook, developers skip it. The smoke job proves the system works as a whole, not just that unit tests pass."

### "How do you approach building internal tools?"
"The first question is always: who is the engineer using this and what is the most annoying thing they do manually today? For Quartz, the answer is writing edge-case test scenarios. Engineers know the happy path. They miss the failure modes. The AI generator is targeted at exactly that gap. Second question: does it have to be running for someone else's work to continue? If yes, it needs to be observable — pass rates, run history, failure trends. I added a PostgreSQL view for pass rate trending specifically so the team can see if a recent code change degraded test stability."

---

## Final Self-Review Checklist Before the Interview

```
□ Can you demo Quartz live in 3 minutes without reading notes?
□ Can you explain the Anthropic tool_use design choice vs asking for JSON in the prompt?
□ Can you recite 5 commerce-specific edge cases off the top of your head?
□ Do you know what "entitlement" and "grace period" mean precisely?
□ Can you explain why the test runner resets mock state before each run?
□ Do you know the difference between 400, 402, 409, and 422 status codes
  in a commerce context?
□ Can you explain the WebSocket architecture: runner → event bus → WS server → React?
□ Can you walk through the GitHub Actions workflow from PR open to merge?
```
