# Hayden NeSmith — Job Hunt Project Context
## Claude Code Session Briefing

---

## Who Hayden Is

Hayden NeSmith is a Lead Software Engineer at Washington Generators (Kent, WA) with 5 years of experience. He is actively job hunting for IC SWE roles in Seattle metro or remote. Minimum salary $100K. Targeting roles before they hit LinkedIn/Indeed via career monitor automation.

**Contact:** nesmithhayden@gmail.com | 206-636-2907 | Seattle, WA
**GitHub:** github.com/CodeBun203
**Education:** B.S. Computer Science, Cybersecurity and Information Assurance — University of Washington Seattle

**Resume summary:** Built entire internal platform at Washington Generators from scratch: 20+ containerized services, zero-trust security (OAuth 2.0/OIDC/RBAC/Keycloak/Cloudflare Tunnel/Tailscale), event-driven pipelines (ServiceTitan/Google/CRM APIs), on-premises RAG assistant (Llama 3.1 8B + LangChain + FAISS), 70%+ test coverage (Pytest/Jest), Prometheus/Grafana observability. Reduced manual processing by 50+ hours/week.

**When writing about Hayden:** Say 2 years of professional experience (not 1.5). No em dashes in professional writing (AI tell). Avoid parallel buzzword structures. Natural phrasing only.

---

## Active Interview Pipeline (as of 2026-05-30)

### 1. Snowflake — Software Engineer, Backend (ASHREQ-1339)
- **Status:** Completed HackerRank Chakra AI Talent Intake
- **Role:** IC SWE, backend distributed systems for Data Cloud
- **Salary range:** $160K-$230K
- **Key prep file:** `Snowflake_Interview_Prep.md`
- **Hayden's alignment:** Distributed systems, OAuth/RBAC/governance, RAG pipeline

### 2. Amazon — Software Development Engineer, Amazon Leo
- **Status:** Assessment invitation received, not yet started
- **Role:** SDE-I, Leo low Earth orbit satellite network
- **Assessment:** 100min coding (2 problems) + 45min work simulation + 15min surveys
- **Key prep file:** `Amazon_SDE_Assessment_Prep.md`
- **Hayden's alignment:** Distributed systems, event-driven architecture, Python
- **Note:** Use Python 3 + Django for repo question. Know Leadership Principles cold.

### 3. Apple — Software Development Engineer in Test, ASE Media Platform Quality
- **Status:** Initial 45-min interview scheduled (CoderPad + Webex, hiring manager)
- **Role:** SDET building internal full-stack tools + AI integration for commerce testing
- **Interview format:** Resume walkthrough + basic coding exercise on CoderPad
- **Key prep file:** `Apple_Find_The_Bug.md`, `Apple_Coding_Challenges.md`
- **Hayden's alignment:** Full stack (React + FastAPI), LLM integration, CI/CD, testing
- **Recruiter:** Rich (recruiter), hiring manager is female (name unknown)
- **Note:** SDET = Software Development Engineer in Test. Full engineer role, not QA.

---

## Interview Prep Files (all in C:\Users\nesmi\OneDrive\Desktop\Job_Hunt\)

| File | Contents |
|---|---|
| `Snowflake_Interview_Prep.md` | 50 Q&A with tech explanations, company overview, alignment map |
| `Amazon_SDE_Assessment_Prep.md` | Coding patterns, Leadership Principles, Leo context, time management |
| `Apple_Find_The_Bug.md` | 25 bugs across Python/JS/SQL/API/async/test code with answers |
| `Apple_Coding_Challenges.md` | 17 challenges: data transformation, algorithms, SDET-specific |
| `Python3_Complete_Guide.md` | Complete Python 3 reference: types, collections, classes, async, etc. |
| `Pytest_Complete_Guide.md` | Full pytest guide: fixtures, parametrize, mocking, async, coverage |
| `Git_Version_Control_Complete_Guide.md` | Git from basics to FAANG-level: workflows, rebasing, hooks, internals |
| `Professional_Coding_Standards.md` | Type hints, docstrings, Google-style (NOT @param/@author), comments |
| `AI_Engineering_Best_Practices.md` | Cursor + Claude Code workflow, CLAUDE.md setup, what AI is/isn't for |
| `Nexus_Project_Plan.md` | Full 12-week production project plan covering all resume skills |

---

## The Nexus Project

Hayden is building **Nexus**, an AI-Native Developer Intelligence Platform, to demonstrate all resume skills in a single production-grade project.

**What it is:** Full-stack internal tooling platform that monitors GitHub repositories, ingests webhook events, runs an AI assistant over repository docs, and surfaces analytics dashboards.

**Why this project:** Maps directly to Apple SDET role (internal tooling + AI), Amazon Leo (distributed systems + event processing), and Snowflake (data pipelines + AI).

**Tech stack used:**
- Python/FastAPI (backend), TypeScript/Next.js 14 (frontend), Go (event ingestor), Java (Spark)
- PostgreSQL + SQLAlchemy/Alembic, MongoDB (events), Redis (cache/pub-sub)
- Keycloak (OAuth 2.0/OIDC/RBAC), Prometheus/Grafana, GitHub Actions
- Ollama + Llama 3.1 8B + LangChain + FAISS (RAG), Docker, Kubernetes, Terraform, AWS
- BigQuery, Apache Spark, WebSockets, Playwright, Pytest, Jest

**Project phases:**
1. Foundation: FastAPI scaffold, PostgreSQL, SQLAlchemy, Alembic, Pytest (Weeks 1-2)
2. Auth: Keycloak, OAuth 2.0, JWT validation, RBAC middleware (Week 3)
3. Event pipeline: Go ingestor, MongoDB, Redis pub/sub, WebSockets (Week 4)
4. Frontend: Next.js 14, TypeScript, Jest, Playwright E2E (Weeks 5-6)
5. AI features: Ollama, RAG pipeline, code review agent, prompt engineering (Weeks 7-8)
6. Observability + CI/CD: Prometheus, Grafana, Alertmanager, GitHub Actions (Week 9)
7. Infrastructure: Terraform, AWS, Kubernetes (Week 10)
8. Analytics: Apache Spark (Java), BigQuery, SQL analytics (Week 11)
9. Hardening: 90%+ coverage, security audit, documentation (Week 12)

**Project plan file:** `Nexus_Project_Plan.md` — full phase-by-phase guide with code templates, teaching notes, and deliverable checklists.

---

## Career Monitor

Automated job monitor at `C:\Users\nesmi\OneDrive\Desktop\Job_Hunt\career-monitor\` runs 3x/day and emails nesmithhayden@gmail.com when new jobs match. Covers 68 companies via Greenhouse, Lever, Workday, and custom scrapers.

Run: `node monitor.js --once` (one-shot + email)

Known broken companies (bot-blocked Workday): Starbucks, PACCAR, Avalara, Apptio, Adaptive Biotechnologies.
Zero-result companies (possible ATS change): Netflix, Blue Origin, Alaska Airlines, Meta, Apple.

Full status: `memory/project_career_monitor.md`

---

## Key Preferences and Constraints

- No em dashes in any professional writing (known AI tell)
- Do not describe experience as "1.5 years" — say "2 years"
- Avoid "positive, not negative" sentence structure ("I built X from scratch, not added to existing")
- Avoid corporate buzzword phrasing that sounds AI-generated
- Targeting IC SWE roles only (not staff, principal, or management)
- Seattle metro or remote only
- Minimum $100K salary
- Automation must be fully headless (no visible browser windows)

---

## How to Resume Work in a New Session

1. Read this file first
2. Check which interview is most urgent based on dates above
3. Ask Hayden what he wants to work on
4. Reference the relevant prep file for that company
5. If working on Nexus, ask which phase he is on and read `Nexus_Project_Plan.md`
