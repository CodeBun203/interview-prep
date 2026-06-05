# Experience Deep-Dive — Hayden NeSmith, Washington Generators LLC

> Purpose: a complete, *truthful* reference for everything you actually built and operate, written
> in the vocabulary interviewers expect. Every claim here is backed by something running on the
> `washgen` server today. Read it until each section feels like *your own words*, not a script.

---

## 0. The 30-second framing (who you are)

You are the **sole owner of a production, on-premises microservices platform** for a generator
installation/service company. You run ~18 distinct applications across 28 Docker containers, plus a
6-service observability stack, behind a zero-trust ingress, with centralized identity, automated
backups, and a fully local LLM. You cover **infrastructure, backend, frontend, data, and release** —
i.e., you are a one-person platform + DevOps + full-stack team.

The senior-level story is **ownership and breadth under real constraints** (a small business, no big
cloud budget, real users depending on it). Lead with that.

---

## 1. Platform architecture (the system-design answer)

**One-liner:** "A service-oriented platform of ~20 containerized apps behind a single zero-trust
ingress, with a centralized OIDC identity plane, a shared Postgres data tier, an event-driven
integration layer, and a full observability stack — all self-hosted on a single Ryzen box with
RAID-1 redundancy."

### Request flow (be able to draw this on a whiteboard)
```
End user (browser / external system)
   │  HTTPS
   ▼
Cloudflare edge  ──(WAF, TLS termination, DDoS)
   │  encrypted QUIC tunnel (no inbound ports open on the server)
   ▼
cloudflared daemon (systemd service on washgen)
   │  HTTP to 127.0.0.1
   ▼
Next.js Portal (reverse-proxy + path routing + auth middleware)
   │  injects identity headers / enforces session
   ▼
Backend microservice (FastAPI / Node) ── reads/writes ──► PostgreSQL 16
   ▲
   └── identity: validates OIDC/JWT issued by Keycloak
```

### Key terms & acronyms to own here
- **Microservices / service-oriented architecture (SOA):** independent, separately deployable
  services, each with its own container, port (bound to `127.0.0.1`), and usually its own Postgres
  *schema* (logical isolation inside one shared DB instance).
- **Ingress:** the single entry point for traffic. Yours is the Cloudflare Tunnel.
- **Reverse proxy:** the Portal (and Cloudflare) forward requests to internal services. Path-based
  routing: `app.washingtonelectric.info/lead-claimer` → `lead-claimer` container.
- **Zero-trust:** never trust based on network location; authenticate/authorize every request.
  Evidence: **no open inbound ports**, every container bound to loopback, admin only via VPN.
- **Defense in depth:** multiple independent layers — Cloudflare WAF → tunnel → 127.0.0.1 binding →
  per-network Docker isolation → app-level auth. Name all five; that's the phrase.
- **Declarative orchestration:** services defined in `docker-compose.yml` (desired-state, not
  imperative scripts). This is your honest stand-in for "infrastructure as code."

---

## 2. Identity, authentication & authorization (your strongest area — lead with it)

**What runs:** Keycloak (containerized, isolated on its own Docker network with its own Postgres),
acting as the company **Identity Provider (IdP)**. The Next.js Portal is an **OIDC relying party**
using the `next-auth` Keycloak provider.

**The flow, in correct terminology:**
1. User hits the Portal → redirected to Keycloak (the **Authorization Code flow**, OAuth 2.0 / OIDC).
2. Keycloak authenticates the user against the **WashGen realm** and issues an **ID token** and
   **access token** — both **JWTs (JSON Web Tokens)**, signed with Keycloak's keys.
3. The Portal's `jwt` callback extracts a **`role` claim** from the token profile and attaches it to
   the session (`Admin`, `Technician`, `Scanner`, `Guest`).
4. Downstream apps trust the Portal's enforced session / forwarded identity headers — **RBAC
   (role-based access control)** decides what each role can reach.
5. Logout hits Keycloak's `openid-connect/logout` endpoint to end the SSO session (so users can
   switch accounts) — i.e., true **Single Sign-On / Single Logout**.

### Terms to own
- **OAuth 2.0** = authorization framework (delegated access). **OIDC** = an *identity* layer on top
  of OAuth 2.0 (adds the ID token / "who is this user"). Know the difference — it's a classic probe.
- **Realm** (Keycloak's tenant boundary), **client** (an app registered with the IdP),
  **client secret**, **issuer URL**, **claims**, **scopes**.
- **JWT structure:** header.payload.signature, base64url-encoded; signature verified against the
  IdP's public key (JWKS endpoint). Stateless — the resource server doesn't call back to the IdP.
- **SSO / SLO**, **RBAC**, **principle of least privilege**, **token introspection vs. local
  validation**.

### STAR story — "centralized auth"
- **Situation:** every internal app had ad-hoc or no auth; no single user directory.
- **Task:** unify identity and enforce role-based access across the whole platform.
- **Action:** stood up Keycloak as an OIDC IdP in an isolated container/network, modeled four roles
  in the WashGen realm, integrated the Portal as a relying party with JWT role-claim propagation,
  and put the apps behind path-routed, session-enforced access.
- **Result:** one login for all apps, role-gated access, and a single place to add/remove employees.

---

## 3. Networking & zero-trust access (the security answer)

- **Cloudflare Tunnel (`cloudflared`):** outbound-only encrypted tunnel (QUIC). The server has
  **zero inbound ports open**. Cloudflare provides TLS termination, **WAF (Web Application
  Firewall)**, and DDoS protection. Public hostnames: `app.`, `inventory.`, `auth.`,
  `webhooks.` under `washingtonelectric.info`.
- **Tailscale (WireGuard mesh VPN):** the *administrative backchannel*. SSH and admin access happen
  over the tailnet (`100.x` CGNAT range), peer-to-peer, end-to-end encrypted. Not public.
- **Port-binding hardening:** every Docker publish is `127.0.0.1:PORT` — services are unreachable
  from the LAN or the Tailscale IP directly; traffic must traverse the tunnel or an SSH tunnel.
- **Email deliverability:** you manage **SPF, DKIM, and DMARC** DNS records so company mail
  authenticates and isn't spoofed/junked. Know what each does:
  - **SPF** = which servers may send for the domain (TXT record).
  - **DKIM** = cryptographic signature on outbound mail (public key in DNS).
  - **DMARC** = policy + reporting tying SPF/DKIM alignment together (quarantine/reject).

### Terms to own
- **Zero-trust network access (ZTNA)**, **WireGuard**, **mesh VPN**, **CGNAT**, **QUIC**,
  **TLS 1.3**, **WAF**, **split-horizon access** (public vs. admin planes).

---

## 4. Data tier (databases)

- **PostgreSQL 16** is the system of record. You run multiple logical databases/**schemas**
  (`emailreader`, `permitacquirer`, `fleetst`, `docbuilder`, `webhookgw`, inventory, portal
  profiles, Keycloak). Schema-per-service = logical isolation without the overhead of N database
  servers.
- **Never exposed to host:** Postgres ports are internal to Docker networks only.
- **Migrations:** **Alembic** (SQLAlchemy's migration tool) for versioned schema changes — you can
  speak to *forward/backward migrations*, *revisions*, *autogenerate*.
- **ORM:** **SQLAlchemy** + **Pydantic** models in the FastAPI services (validation at the edge,
  ORM for persistence).
- **SCRAM-SHA-256** password auth on the DB.

### Terms to own
- **ORM (object-relational mapping)**, **schema**, **migration / DDL**, **connection pooling**,
  **ACID**, **transaction**, **index**, **`pg_dump` logical backup**, **normalization**.

### STAR story — "schema-per-service data design"
Situation: many apps needed persistence; spinning up a DB per app wastes resources. Action: one
hardened Postgres instance, one schema per service for isolation, Alembic migrations per service,
SQLAlchemy/Pydantic in the apps. Result: clean separation, simple backups (one `pg_dump`), low
overhead.

---

## 5. The integration / data-pipeline layer (your highest business impact)

This is where you "reduced manual processing by 50+ hours/week." Be concrete.

- **Sources:** ServiceTitan (field-service CRM/FSM platform), lead vendors (Modernize, Costco,
  Lowe's funnels), Google Workspace, web forms.
- **Destinations:** internal CRMs (`wg-crm`), Postgres stores, Google Sheets/BigQuery.
- **Pattern — event-driven:** the **`webhook-gw`** is a **config-driven webhook gateway**. A single
  public endpoint receives inbound events; `routes.yml` maps each path to an internal container;
  per-source **signature/secret validation** (HMAC-style shared-secret header) authenticates
  senders. Adding a new source = a YAML edit + restart, **no code change**. This is the phrase:
  *"config-driven, declarative routing with per-source signature verification."*
- **Automation platforms:** **Zapier** and **n8n** for glue/orchestration where a full service is
  overkill; a **Router** pattern fans one trigger out to multiple actions.
- **Headless-browser automation (the integration fallback):** when a vendor exposes **no API**, you
  drive the site programmatically with **Puppeteer / Playwright** (headless Chromium) — e.g.,
  Lead Claimer pulling from PowerPlay, Permit Acquirer filing on ePermit portals. Frame it as a
  *deliberate fallback*: "API first; headless automation only where no API exists."
- **Google integration:** **service accounts** in GCP with **OAuth 2.0** scopes for server-to-server
  access — reading inboxes (Gmail API), Sheets, Docs, Drive, Calendar, and **BigQuery** for
  analytics. Know **service account vs. user OAuth**, **scopes**, **domain-wide delegation**.

### Terms to own
- **Event-driven architecture**, **webhook**, **idempotency** (handling duplicate webhook
  deliveries safely), **HMAC signature verification**, **ETL / data ingestion**, **rate limiting**,
  **backoff/retry**, **FSM (field-service management)**, **CRM**.

### STAR story — "the webhook gateway"
Situation: each app exposed its own inbound endpoint; adding sources meant code + redeploys, and
auth was inconsistent. Action: built one FastAPI gateway with declarative `routes.yml`, centralized
signature validation, and centralized logging; new sources are config-only. Result: faster
onboarding of lead sources, consistent security posture, one place to audit inbound traffic.

---

## 6. The on-prem RAG assistant (your standout AI project)

**What it is:** a **conversational, retrieval-augmented** technical assistant for ~15 field techs,
answering from your service manuals — running **fully on-prem, zero external API cost**.

**Architecture (be precise — this is sophisticated and you should sound like it):**
- **LLM:** **Llama 3.1 8B** served locally via **Ollama** (`ChatOllama`).
- **Embeddings:** **`nomic-embed-text`** via Ollama (`OllamaEmbeddings`).
- **Vector store:** **FAISS** (Facebook AI Similarity Search) — persisted index of manual chunks.
- **Chunking:** `RecursiveCharacterTextSplitter` over the documents.
- **Orchestration:** **LangChain** — specifically a **history-aware retriever**
  (`create_history_aware_retriever`) that *rewrites the user's question using chat history* before
  retrieval, then a `create_retrieval_chain` that stuffs retrieved context into a grounded prompt.
- **Grounding/guardrail:** the QA system prompt instructs the model to answer **only** from
  retrieved context — reducing hallucination.

**The migration story (the part that shows engineering judgment):**
- v1: **Google Gemini Flash 2.5**, hosted on **Render**, pulling from a private GitHub repo.
- v2: migrated to **local Llama 3.1 8B + LangChain + FAISS on the server**.
- **Why:** eliminate per-token API cost and keep proprietary manuals in-house (**data
  sovereignty**). This is a cost + privacy + control tradeoff you made deliberately.

### Terms to own (RAG vocabulary interviewers love)
- **RAG (retrieval-augmented generation)**, **embedding / vector**, **semantic search**,
  **cosine similarity / ANN (approximate nearest neighbor)**, **vector store / index**,
  **chunking & overlap**, **context window**, **grounding**, **hallucination**, **prompt
  template**, **system prompt**, **history-aware / contextualized retrieval**,
  **inference**, **quantization** (why an 8B model fits on a 30 GB box), **local inference**,
  **token / per-token cost**, **data sovereignty**.

### Likely follow-ups + your answers
- *"How do you handle hallucination?"* → grounded prompt ("answer only from context"), plus
  retrieval quality (chunk size/overlap, top-k). Honest next step: add answer-citations + an
  eval set.
- *"Why 8B and not a bigger model?"* → fits in RAM/VRAM budget on the Ryzen iGPU box, fast enough
  for interactive use, and RAG does the heavy lifting so raw model size matters less.
- *"Why FAISS over a hosted vector DB?"* → on-prem, no external dependency, free, fast for this
  corpus size; would consider pgvector/Qdrant if it grew.

---

## 7. Observability & reliability (the SRE answer)

- **Metrics:** **Prometheus** scrapes targets and stores **time-series** data; **exporters** expose
  metrics — **node-exporter** (host), **cAdvisor** (per-container), **postgres-exporter** (DB).
- **Dashboards:** **Grafana** visualizes Prometheus data.
- **Alerting:** **Alertmanager** routes alerts from Prometheus rules (grouping, dedup, routing).
- **Health checks:** Docker `healthcheck` per service (you can see `(healthy)` in `docker ps`).
- **Data integrity:** **RAID-1** mirror (`mdadm`, `/dev/md0`, `[UU]`) across 2×3.6 TB drives;
  **`mdmonitor`** for failure alerts; **SMART** disk monitoring.
- **Backups:** scheduled **`pg_dump`** to the RAID array via **cron**; monthly `docker system
  prune`; Fleet sync jobs nightly/weekly.

### The maturity line (say this proactively — it reads as senior)
> "RAID is redundancy, not a backup — it protects against a drive dying, not against deletion,
> corruption, or ransomware. My next hardening step is true off-site backup to satisfy 3-2-1."

### Terms to own
- **Observability (metrics/logs/traces — the three pillars)**, **time-series database (TSDB)**,
  **exporter / scrape target / PromQL**, **SLI/SLO/SLA**, **MTTR**, **alert routing**,
  **RAID-1 / mirroring / `mdadm`**, **SMART**, **3-2-1 backup rule**, **cron**, **idempotent jobs**.

---

## 8. Application & backend engineering

- **Backend:** **FastAPI** (Python, **async/await**, **ASGI** via uvicorn) for most services;
  **Node/Express** for a few (Lead Claimer, Doc Builder backend).
- **Concurrency, said correctly:** "FastAPI is async — a single worker multiplexes many
  **I/O-bound** requests (waiting on ServiceTitan, Google, the DB) without blocking, using
  **`asyncio`** and `await`. For **CPU-bound** work you'd reach for multiprocessing/threads; async
  is for I/O concurrency, not parallelism." Knowing this distinction is a senior signal.
- **Validation:** **Pydantic** models validate/serialize at the API boundary; **SQLAlchemy** for
  persistence; **Alembic** for migrations.
- **Frontend:** **Next.js 14** (React framework — **App Router**, **SSR/SSG**, **middleware**-based
  auth, **rewrites** for path routing), Tailwind CSS, `next-auth` for the OIDC flow. Several
  mobile-friendly scanner/dashboard UIs.
- **Testing:** **Pytest** (Python) and **Jest** (JS/TS) across services (hundreds of test files).
  Be ready to describe **unit vs. integration vs. e2e**, **fixtures**, **mocking external APIs**,
  **test DB**, and **coverage** measurement.
- **Version control:** Git + GitHub; per-app repos; you keep an architecture **paper trail**
  (`SYSTEM_OVERVIEW.md`, `NETWORKING.md`, `SECURITY.md`, `DATABASE_SCHEMA.md`, diagrams).

### Terms to own
- **ASGI/WSGI**, **async/await / event loop / coroutine**, **I/O-bound vs. CPU-bound**,
  **dependency injection** (FastAPI `Depends`), **DTO / schema validation**, **SSR/CSR/SSG/ISR**,
  **middleware**, **REST**, **WebSocket** (Lead Claimer real-time updates), **idempotency**.

---

## 9. Stakeholder / business-facing work (don't skip — it's the lead-engineer part)

- Translate **business needs ↔ technical requirements** with non-technical staff (accounting/sales).
- Negotiate **technical requirements with external partners** (e.g., the ServiceTitan→Sameday lead
  integration; arguing for API access / `booking_provider` permissions).
- Work with **two CRMs and sales teams** to define data-management best practices.
- Author **multi-altitude documentation** — exec overviews down to low-level implementation — and
  **process diagrams**.

Frame this as: *"I'm the technical translator. I own the system AND the conversation about what the
business actually needs it to do."*

---

## 10. The honest "scale & constraints" framing

If asked about scale, be honest and turn the constraint into a strength:
> "This is a small-business platform — tens of internal users, not millions. The interesting
> constraints weren't horizontal scale; they were doing platform engineering *as one person* on a
> tight budget: zero-trust security without enterprise tooling, an LLM with no API spend,
> reliability with RAID + backups instead of a cloud SLA. I made deliberate build-vs-buy and
> cost-vs-control tradeoffs at every layer."

That answer is more impressive than pretending you ran web-scale infra — and it's true.

---

## Appendix A — Acronym quick-reference (drill these)

| Acronym | Expansion | One-line meaning |
|---|---|---|
| OIDC | OpenID Connect | identity layer on top of OAuth 2.0 |
| OAuth 2.0 | — | delegated authorization framework |
| JWT | JSON Web Token | signed, stateless token carrying claims |
| RBAC | Role-Based Access Control | permissions by role |
| IdP | Identity Provider | issues identity tokens (Keycloak) |
| SSO/SLO | Single Sign-On / Logout | one login/logout across apps |
| ZTNA | Zero-Trust Network Access | authenticate every request, trust no network |
| WAF | Web Application Firewall | filters malicious HTTP (Cloudflare) |
| RAG | Retrieval-Augmented Generation | LLM answers grounded in retrieved docs |
| ANN | Approximate Nearest Neighbor | fast vector similarity search (FAISS) |
| ORM | Object-Relational Mapping | objects ↔ DB rows (SQLAlchemy) |
| ASGI | Async Server Gateway Interface | async Python web standard (FastAPI) |
| FSM | Field-Service Management | ServiceTitan's category |
| SPF/DKIM/DMARC | email auth trio | who-can-send / signature / policy |
| SLI/SLO/SLA | service level indicator/objective/agreement | reliability targets |
| MTTR | Mean Time To Recovery | how fast you recover from failure |
| RAID | Redundant Array of Independent Disks | disk redundancy (you run RAID-1) |
| TSDB | Time-Series Database | Prometheus' storage model |
| ETL | Extract-Transform-Load | data pipeline pattern |
| HMAC | Hash-based Message Auth Code | webhook signature verification |

## Appendix B — "Tell me about a project" — your three go-to stories
1. **The on-prem RAG migration** (cost + data sovereignty + RAG depth) — §6.
2. **The zero-trust auth platform** (Keycloak/OIDC/RBAC, defense in depth) — §2 + §3.
3. **The webhook gateway / integration layer** (event-driven, 50+ hrs/week saved) — §5.

Each already in STAR form above. Memorize one sentence per letter; let the rest be conversational.
