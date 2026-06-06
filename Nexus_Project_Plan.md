# Nexus: AI-Native Developer Intelligence Platform
## Complete Production Project Plan — Every Resume Skill Covered

---

## What This Project Is

Nexus is a production-grade internal developer intelligence platform. It ingests GitHub webhook events, monitors code quality and deployment health across repositories, runs AI-powered analysis, and surfaces insights through multiple frontends. It integrates with Google Workspace, deploys across AWS/Azure/GCP, and includes mobile, network monitoring, and ML capabilities.

This is not a toy. Every architecture decision should be made the way a senior engineer at Apple, Amazon, or Snowflake would make it. Every phase has a deliverable you can demo in an interview, discuss at the design level, and point to as evidence of professional capability.

**Total duration:** 17-18 weeks
**Total services:** 14 microservices across 8 languages
**Final result:** A system you can talk about at FAANG depth

---

## Skills Coverage Map — Every Skill on Your Resume

### Languages
| Skill | Where in Nexus |
|---|---|
| Python | FastAPI backend, Flask ML server, Pandas data pipelines, all AI/ML work |
| TypeScript | Next.js 14 user dashboard, Angular admin panel (strict mode throughout) |
| JavaScript | Express.js BFF, Puppeteer scrapers, vanilla browser utilities |
| SQL | PostgreSQL schema design, complex window functions, MySQL for Ruby service |
| Go | High-performance event ingestor, API gateway |
| Scala | Apache Spark batch analytics jobs (Spark's native language) |
| Swift | iOS companion app — real-time metrics, alerts, repo health scores |
| Java | Spring Boot REST analytics API |
| C++ | Custom FAISS vector store wrapper for high-performance semantic search |
| C# | .NET notification hub with SignalR (real-time WebSocket push) |
| Bash | Deployment scripts, pre-commit hooks, CI pipeline shell steps, Docker entrypoints |
| Ruby | Webhook preprocessor service, Rake tasks, MySQL-backed job queue |

### Frameworks
| Skill | Where in Nexus |
|---|---|
| FastAPI | Primary Python backend — user, repo, auth, AI routes |
| Next.js 14 | User-facing dashboard (App Router, Server Components, streaming) |
| React | Component library shared across Next.js pages |
| Angular | Internal admin panel (separate app, role-gated to ops team) |
| Node.js | Express BFF runtime, Puppeteer scraper runtime |
| Express | Backend-for-frontend layer between Next.js and FastAPI |
| Spring | Spring Boot REST API serving analytics queries |
| Flask | Lightweight ML model server (PyTorch/TF inference endpoints) |
| SQLAlchemy | ORM for FastAPI service (2.0 async engine) |
| Alembic | All PostgreSQL migrations |
| Pydantic | Request/response schemas throughout Python services |
| LangChain | RAG pipeline, agent orchestration, tool-use chains |
| Puppeteer | External repo scraping, dependency vulnerability scanning |
| Playwright | E2E test suite for all web frontends |
| Pandas | Data transformation in analytics ETL pipeline |
| Pytest | Full test suite for all Python services |
| Jest | Unit/integration tests for all TypeScript/JavaScript services |

### Infrastructure
| Skill | Where in Nexus |
|---|---|
| Docker | Every service has a Dockerfile; docker-compose for local dev |
| Linux/systemd | Systemd unit files for bare-metal services; process supervision |
| Keycloak | OAuth 2.0/OIDC identity provider; manages all auth flows |
| Cloudflare Tunnel | Exposes local services to internet without open firewall ports |
| Tailscale | Zero-trust mesh VPN between all internal services |
| Prometheus | Metrics scraped from every service via /metrics endpoints |
| Grafana | Dashboards for all services; Alertmanager integration |
| Kubernetes | Production deployment on EKS (AWS); manifests for all services |
| Terraform | IaC for AWS, Azure, and GCP resources |
| RAID/mdadm | On-premises raw event storage with RAID-5 for durability |

### Cloud & Data
| Skill | Where in Nexus |
|---|---|
| AWS | EKS (Kubernetes), RDS (PostgreSQL), ElastiCache (Redis), S3 |
| Azure | Azure Kubernetes Service (AKS) as DR target; Azure Blob Storage |
| GCP | BigQuery (analytics DW), GCS (artifact storage), Cloud Run |
| BigQuery | Long-term analytics warehouse; Spark writes here |
| PostgreSQL | Primary relational DB — users, repos, sessions, audit logs |
| MongoDB | Event log, webhook payloads, document-style repo metadata |
| Redis | API response caching, pub/sub for real-time events, rate limiting |
| MySQL | Ruby webhook service job queue and processing state |

### Security
| Skill | Where in Nexus |
|---|---|
| OAuth 2.0 | Keycloak handles all token issuance and introspection |
| OIDC | ID tokens validated in every service middleware |
| RBAC | Four roles: viewer, developer, admin, ops — enforced at API layer |
| Zero-trust | No service trusts another without verified identity (Tailscale + mTLS) |
| SSDLC | Threat model in Phase 2; OWASP scans in CI from Phase 6 onward |
| SDN/IoT | Phase 12: software-defined network monitoring module |
| TCP/IP | Phase 12: raw packet inspection, flow analysis |
| OpenFlow | Phase 12: OpenFlow controller for network policy automation |

### AI
| Skill | Where in Nexus |
|---|---|
| AI-Native | Architecture principle: AI features are first-class, not bolted on |
| PyTorch | Code quality prediction model (Phase 7) |
| TensorFlow | Commit classification model — bugfix vs feature vs refactor (Phase 7) |
| JAX | Experimentation: fast gradient computation for model fine-tuning (Phase 7) |
| Scikit-learn | Anomaly detection on metrics timeseries (Phase 7) |
| NumPy | Foundation for all ML work; custom embedding operations |
| Ollama | Local LLM runtime hosting Llama 3.1 8B |
| Llama | Primary LLM for code review suggestions and RAG responses |
| FAISS | Vector index for semantic search over repository documentation |
| RAG pipelines | Full RAG over repo docs, commit history, and issue text |
| Agent orchestration | LangChain agent with GitHub tools, search tools, analysis tools |
| Agentic workflow design | Multi-step autonomous workflows: analyze → summarize → notify |
| Gemini | Secondary LLM provider (Google Generative AI API) for comparison |
| Prompt engineering | System prompts, few-shot examples, chain-of-thought for all agents |

### Tools
| Skill | Where in Nexus |
|---|---|
| Git | Feature branch workflow, conventional commits, interactive rebase |
| GitHub Actions | CI/CD pipeline: lint → test → security scan → build → deploy |
| CI/CD | Full pipeline with coverage gates, staging, and production promotion |
| RESTful APIs | All internal and external API surfaces follow REST conventions |
| WebSockets | Real-time dashboard updates via Redis pub/sub + WS relay |
| Google APIs | Gmail (alert emails), Sheets (weekly reports), BigQuery OAuth, Calendar |

---

## Full Architecture

```
                         ┌──────────────────────┐
                         │   Swift iOS App       │  ← Swift, SwiftUI
                         │   (Mobile Client)     │
                         └──────────┬───────────┘
                                    │ HTTPS
          ┌─────────────────────────┼─────────────────────────┐
          │                         │                         │
┌─────────▼────────┐    ┌───────────▼──────────┐   ┌─────────▼────────┐
│  Next.js 14      │    │  Angular Admin        │   │  Express BFF     │
│  (User Dashboard)│    │  (Ops Panel)          │   │  (Node.js)       │
└─────────┬────────┘    └───────────┬──────────┘   └─────────┬────────┘
          │                         │                         │
          └─────────────────────────┼─────────────────────────┘
                                    │ OAuth Token
                         ┌──────────▼───────────┐
                         │   Go API Gateway      │  ← Token validation, rate limiting
                         └──────────┬────────────┘
                                    │
          ┌─────────────────────────┼──────────────────────────┐
          │                         │                          │
┌─────────▼────────┐    ┌───────────▼──────────┐   ┌──────────▼───────┐
│  FastAPI Backend │    │  Flask ML Server      │   │  Spring Boot     │
│  (Python)        │    │  (PyTorch/TF/JAX)     │   │  Analytics API   │
└─────────┬────────┘    └───────────────────────┘   └──────────┬───────┘
          │                                                     │
          │         ┌──────────────────────┐                   │
          │         │  C# .NET Notif Hub   │  ← SignalR WS     │
          │         └──────────────────────┘                   │
          │                                                     │
┌─────────▼────────┐    ┌──────────────────────┐   ┌──────────▼───────┐
│  Go Ingestor     │    │  Ruby Webhook Proc.   │   │  C++ FAISS Wrap  │
│  (GitHub Events) │    │  (Preprocessing)      │   │  (Vector Ops)    │
└─────────┬────────┘    └──────────┬────────────┘   └──────────────────┘
          │                        │
          └────────────────────────┘
                        │
       ┌────────────────┼──────────────────┐
       │                │                  │
┌──────▼──┐    ┌────────▼──┐    ┌──────────▼──┐
│Postgres │    │  MongoDB  │    │   Redis      │
│(primary)│    │  (events) │    │  (cache/ps)  │
└─────────┘    └───────────┘    └─────────────┘
                        │
          ┌─────────────┼──────────────┐
          │             │              │
   ┌──────▼───┐  ┌──────▼──────┐  ┌───▼─────┐
   │  MySQL   │  │  BigQuery   │  │  FAISS  │
   │  (Ruby)  │  │  (DW)       │  │  Index  │
   └──────────┘  └──────┬──────┘  └─────────┘
                        │
              ┌──────────▼──────────┐
              │  Apache Spark       │  ← Scala
              │  (Batch Analytics)  │
              └─────────────────────┘
                        │
              ┌──────────▼──────────┐
              │ LangChain RAG Agent │  ← Ollama/Llama + Gemini + FAISS
              └─────────────────────┘
                        │
     ┌──────────────────┼──────────────────┐
     │                  │                  │
┌────▼─────┐   ┌────────▼────────┐  ┌──────▼──────┐
│Keycloak  │   │Prometheus/Grafana│  │ SDN/Network │
│(Auth)    │   │(Observability)  │  │ Monitor     │
└──────────┘   └─────────────────┘  └─────────────┘
                        │
         ┌──────────────┼──────────────┐
         │              │              │
      ┌──▼──┐        ┌──▼──┐       ┌──▼──┐
      │ AWS │        │Azure│       │ GCP │
      │(EKS)│        │(AKS)│       │(BQ) │
      └─────┘        └─────┘       └─────┘
```

---

## FAANG Engineering Practices — Read This Before Writing Any Code

This section defines HOW you work on Nexus. Not just what you build, but the process. This is what separates a portfolio project from evidence of professional readiness.

---

### Git Workflow: Feature Branch Strategy

Every change, no matter how small, follows this process:

```bash
# 1. Always start from main, always pull first
git checkout main
git pull origin main

# 2. Create a feature branch — conventional naming
git checkout -b feat/phase1-fastapi-scaffold
# OR
git checkout -b fix/auth-token-expiry-validation
# OR
git checkout -b chore/update-dependencies

# 3. Make atomic commits — one logical change per commit
git add -p    # ALWAYS use -p, never git add .
git commit -m "feat(auth): add JWT expiry validation middleware"

# 4. Keep branch short-lived — push daily, open PR within 3 days
git push -u origin feat/phase1-fastapi-scaffold

# 5. Before PR: rebase onto main to keep history clean
git fetch origin
git rebase origin/main
# Resolve any conflicts, then:
git push --force-with-lease   # NEVER git push --force

# 6. Open PR via GitHub CLI
gh pr create --title "feat(auth): JWT validation middleware" \
  --body "## What\nAdds middleware to validate JWT exp claim on all protected routes.\n\n## Why\nPrevents requests with expired tokens from reaching route handlers.\n\n## Test plan\n- [ ] Unit tests pass\n- [ ] Manual test with expired token returns 401\n- [ ] CI green"

# 7. After merge, clean up
git checkout main
git pull origin main
git branch -d feat/phase1-fastapi-scaffold
```

**Conventional Commits format (mandatory):**
```
<type>(<scope>): <short description>

Types: feat, fix, chore, docs, refactor, test, perf, ci
Scope: the service/module (auth, api, frontend, ingestor, ml, infra)

Examples:
feat(api): add repository health score endpoint
fix(auth): handle token refresh race condition
test(ingestor): add integration tests for webhook deduplication
ci: add OWASP dependency check to pipeline
refactor(ml): extract embedding generation into service class
```

---

### SCRUM as a Solo Developer

FAANG teams run in 2-week sprints. As a solo developer, simulate the ceremonies:

**Sprint Planning (30 min, start of each phase):**
- Write down the sprint goal in one sentence
- Break the phase tasks into GitHub Issues
- Estimate each issue (S=2h, M=4h, L=8h, XL=2 days)
- Assign them to a GitHub Milestone named "Sprint N"

```bash
# Create issues for each task
gh issue create --title "Task 1.1: Docker Compose environment setup" \
  --body "Set up docker-compose.yml with postgres, redis, mongodb. Add health checks." \
  --label "infrastructure,sprint-1" \
  --milestone "Sprint 1"
```

**Daily Standup (5 min, every day you work on Nexus):**
Write three lines in a scratch file or Notion before you start:
```
Yesterday: Completed JWT middleware, opened PR #12
Today: Start on RBAC model and unit tests
Blockers: None
```

**Sprint Review (15 min, end of each phase):**
- Demo the feature to yourself (or record a Loom)
- Check off deliverable checklist
- Note what took longer than expected

**Sprint Retrospective (10 min):**
- What went well?
- What was harder than expected?
- What would you do differently?

This process gives you real answers for behavioral interview questions. "Tell me about a time you managed competing priorities" — you have a genuine story.

---

### SDLC: From Ticket to Production

For every non-trivial feature, follow this lifecycle:

```
1. REQUIREMENTS     → GitHub Issue with acceptance criteria
2. DESIGN           → ADR (Architecture Decision Record) in docs/adr/
3. IMPLEMENTATION   → Feature branch, atomic commits
4. CODE REVIEW      → Self-review checklist + PR description
5. CI               → Automated: lint, tests, security scan, build
6. STAGING DEPLOY   → Deploy to staging, smoke test
7. PRODUCTION       → Merge to main triggers production deploy
8. MONITOR          → Grafana dashboard, check metrics post-deploy
```

**Architecture Decision Record template** (`docs/adr/001-database-choice.md`):
```markdown
# ADR 001: PostgreSQL as Primary Database

## Status
Accepted

## Context
Need a relational database for users, repos, and audit logs.
Considered: PostgreSQL, MySQL, SQLite.

## Decision
PostgreSQL with SQLAlchemy 2.0 async engine.

## Consequences
+ Full ACID compliance
+ Rich JSON support for semi-structured data
+ Native array types useful for tag storage
- Slightly more complex local setup than SQLite
```

**Self-review checklist before opening any PR:**
```
□ Does every new function have a type hint?
□ Does every non-obvious function have a docstring?
□ Are there tests for the happy path AND at least one error case?
□ Did I run git add -p and review every line I am committing?
□ No secrets or .env values in the diff?
□ No TODO comments without a linked GitHub issue?
□ Does the PR description explain WHY, not just WHAT?
```

---

### SSDLC: Secure Software Development Lifecycle

Security is threaded through every phase. This is what SSDLC means in practice:

**Phase 2:** Threat model the auth system before writing a line of code
**Phase 3 onward:** Secrets only via environment variables, never hardcoded
**Phase 6 onward:** OWASP Dependency Check in every CI run
**Phase 11:** Full security audit: SQL injection, XSS, broken auth, IDOR

Threat model template:
```
STRIDE per component:
- Spoofing: Can an attacker impersonate another user? (→ test RBAC enforcement)
- Tampering: Can data be modified in transit? (→ TLS everywhere, verify signatures)
- Repudiation: Can actions be denied? (→ audit log for all writes)
- Information Disclosure: What data leaks on error? (→ sanitize error messages)
- Denial of Service: What happens under load? (→ rate limiting, circuit breakers)
- Elevation of Privilege: Can a viewer reach admin routes? (→ RBAC middleware tests)
```

---

## Before You Write Any Code: Environment Setup

```bash
# 1. Create project and initialize git
mkdir nexus && cd nexus
git init
git checkout -b main

# 2. Create CLAUDE.md — this is the first file in every professional project
cat > CLAUDE.md << 'EOF'
# Nexus — AI-Native Developer Intelligence Platform

## What this project is
[Brief description]

## Architecture
[Link to docs/architecture.md]

## Services
- backend/: FastAPI Python backend
- frontend/: Next.js 14 user dashboard
- admin/: Angular admin panel
- services/go-ingestor/: Go webhook event processor
- services/ruby-webhook/: Ruby preprocessing service
- services/dotnet-notif/: C# .NET notification hub
- services/spring-analytics/: Java Spring Boot analytics API
- services/cpp-faiss/: C++ FAISS wrapper
- services/flask-ml/: Flask ML model server
- mobile/ios/: Swift iOS companion app
- infra/: Terraform configs (AWS, Azure, GCP)
- spark/: Scala Spark analytics jobs
- scripts/: Bash deployment and utility scripts

## How to run locally
docker-compose up -d
cd backend && uvicorn app.main:app --reload

## Test commands
cd backend && pytest
cd frontend && npm test
cd frontend && npx playwright test

## Conventions
- Python: Google-style docstrings, type hints always
- TypeScript: strict mode, no any
- Commits: Conventional Commits
- Branches: feat/, fix/, chore/, refactor/
EOF

# 3. Create .cursor/rules/ for AI-assisted development
mkdir -p .cursor/rules
cat > .cursor/rules/general.mdc << 'EOF'
---
description: General coding standards for Nexus
globs: ["**/*"]
alwaysApply: true
---
- Type hints on every Python function
- TypeScript strict mode, no any
- Google-style docstrings for non-obvious functions
- Conventional Commits format
- No hardcoded secrets — use environment variables
- No TODO without a linked GitHub issue number
EOF

# 4. Create project structure
mkdir -p backend/{app/{models,schemas,api,services,middleware},tests/{unit,integration},alembic/versions}
mkdir -p frontend/src/{app,components,lib,hooks}
mkdir -p admin/src/{app,components,services}
mkdir -p services/{go-ingestor,ruby-webhook,dotnet-notif,spring-analytics,cpp-faiss,flask-ml}
mkdir -p mobile/ios
mkdir -p infra/{aws,azure,gcp,kubernetes}
mkdir -p spark/src/main/scala/nexus
mkdir -p scripts
mkdir -p docs/adr
mkdir -p .github/workflows

# 5. Create .gitignore
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.pyc
*.pyo
.venv/
venv/
.pytest_cache/
.coverage
htmlcov/
dist/
*.egg-info/

# Node
node_modules/
.next/
dist/
.angular/

# Go
*.exe
vendor/

# Java/Scala
target/
*.jar
*.class
.gradle/

# C++
*.o
*.so
build/

# C#
bin/
obj/
*.user

# Ruby
.bundle/
Gemfile.lock

# Swift
.build/
*.xcworkspace/
Pods/

# Secrets — NEVER commit these
.env
.env.*
*.pem
*.key
secrets/

# IDE
.idea/
.vscode/
*.swp
EOF

# 6. First commit
git add CLAUDE.md .gitignore .cursor/
git commit -m "chore: project scaffold with CLAUDE.md, gitignore, and cursor rules"
git remote add origin https://github.com/CodeBun203/nexus.git
git push -u origin main
```

---

## Phase 1: Foundation and Data Layer
### Weeks 1-2 | Python, FastAPI, SQL, PostgreSQL, SQLAlchemy, Alembic, Pydantic, Pytest, Docker, Bash

---

### Sprint Goal
A running FastAPI service with a PostgreSQL-backed data model for users, organizations, and repositories. Full Pytest suite. All infrastructure in Docker.

**Before coding, read:**
- `Python3_Complete_Guide.md` sections 3, 8, 10, 11, 12, 14, 20
- `Pytest_Complete_Guide.md` sections 1-9
- `Professional_Coding_Standards.md` — full file

**Open these GitHub Issues before starting:**
```bash
gh issue create --title "Task 1.1: Docker Compose dev environment" --label "infrastructure,sprint-1"
gh issue create --title "Task 1.2: FastAPI scaffold and project structure" --label "backend,sprint-1"
gh issue create --title "Task 1.3: SQLAlchemy models and Alembic migrations" --label "backend,sprint-1"
gh issue create --title "Task 1.4: Pydantic schemas and CRUD endpoints" --label "backend,sprint-1"
gh issue create --title "Task 1.5: Pytest suite with fixtures and coverage gate" --label "testing,sprint-1"
gh issue create --title "Task 1.6: GitHub Actions CI pipeline" --label "ci,sprint-1"
```

---

### Task 1.1: Docker Compose Environment

```yaml
# docker-compose.yml
version: "3.9"
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: nexus
      POSTGRES_USER: nexus
      POSTGRES_PASSWORD: nexus_dev
    ports: ["5432:5432"]
    volumes: [postgres_data:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U nexus"]
      interval: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]

  mongodb:
    image: mongo:7
    ports: ["27017:27017"]
    volumes: [mongo_data:/data/db]

  mysql:
    image: mysql:8
    environment:
      MYSQL_DATABASE: nexus_ruby
      MYSQL_USER: ruby_svc
      MYSQL_PASSWORD: ruby_dev
      MYSQL_ROOT_PASSWORD: root_dev
    ports: ["3306:3306"]
    volumes: [mysql_data:/var/lib/mysql]

volumes:
  postgres_data:
  mongo_data:
  mysql_data:
```

**Why Docker first:** Environment drift between local and production causes bugs that are impossible to reproduce. Containerizing from day one means if it runs on your machine, it runs everywhere.

Create a Bash script for common dev operations:
```bash
#!/usr/bin/env bash
# scripts/dev.sh — local development helpers
# Why Bash: automation tasks that orchestrate multiple CLI tools belong in shell scripts

set -euo pipefail  # exit on error, undefined var, pipe failure

start() {
  docker-compose up -d
  echo "Waiting for postgres..." && sleep 3
  cd backend && alembic upgrade head
  echo "Dev environment ready"
}

test_all() {
  cd backend && pytest --cov=app --cov-report=term-missing
  cd ../frontend && npm test -- --watchAll=false
}

reset_db() {
  docker-compose down -v
  docker-compose up -d postgres
  sleep 3
  cd backend && alembic upgrade head
}

# Dispatch to function by argument
"$@"
```

---

### Task 1.2: FastAPI Backend Structure

```
backend/
├── app/
│   ├── main.py           # App instance, router registration, lifespan
│   ├── config.py         # pydantic-settings — all env vars typed
│   ├── database.py       # Async SQLAlchemy engine and session factory
│   ├── dependencies.py   # get_db, get_current_user — dependency injection
│   ├── models/           # SQLAlchemy ORM models
│   ├── schemas/          # Pydantic v2 request/response models
│   ├── api/              # Route handlers (thin — delegate to services)
│   ├── services/         # Business logic (no direct DB access in routes)
│   └── middleware/       # Auth, logging, rate limiting
├── tests/
│   ├── conftest.py       # Fixtures: test_db, test_client, mock_user
│   ├── unit/
│   └── integration/
├── alembic/
└── alembic.ini
```

```python
# backend/app/config.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str
    redis_url: str = "redis://localhost:6379"
    mongodb_url: str = "mongodb://localhost:27017"
    keycloak_url: str
    keycloak_realm: str
    secret_key: str

    class Config:
        env_file = ".env"

settings = Settings()
```

```python
# backend/app/models/repository.py
from datetime import datetime
from sqlalchemy import String, Integer, DateTime, ForeignKey, Float
from sqlalchemy.orm import Mapped, mapped_column, relationship
from app.database import Base

class Repository(Base):
    __tablename__ = "repositories"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)
    github_id: Mapped[int] = mapped_column(Integer, unique=True, nullable=False)
    full_name: Mapped[str] = mapped_column(String(255), nullable=False)
    health_score: Mapped[float] = mapped_column(Float, default=0.0)
    last_analyzed_at: Mapped[datetime | None] = mapped_column(DateTime, nullable=True)
    organization_id: Mapped[int] = mapped_column(ForeignKey("organizations.id"))

    organization: Mapped["Organization"] = relationship(back_populates="repositories")
```

---

### Task 1.3: Alembic Migrations

```bash
# Initialize Alembic
cd backend
alembic init alembic

# Create first migration after defining models
alembic revision --autogenerate -m "create users organizations repositories tables"
alembic upgrade head

# Every schema change gets its own migration — never edit existing migrations
alembic revision --autogenerate -m "add health_score to repositories"
alembic upgrade head
```

**Why never edit existing migrations:** Alembic tracks which migrations have run per environment. Editing a migration that has already run in staging means staging and production will have different schemas than what the migration file says. Always create a new migration.

---

### Task 1.4: Complex SQL — Go Beyond the ORM

Write at least two raw SQL queries using SQLAlchemy's text() for things the ORM makes awkward:

```python
# backend/app/services/analytics_service.py
from sqlalchemy import text
from sqlalchemy.ext.asyncio import AsyncSession

async def get_repository_health_trend(
    db: AsyncSession,
    org_id: int,
    days: int = 30,
) -> list[dict]:
    """Return daily average health scores per repository over a window.

    Uses a window function because computing running averages via ORM
    requires fetching all rows into Python first — unacceptable at scale.
    """
    query = text("""
        SELECT
            r.full_name,
            DATE(e.created_at) as day,
            AVG(e.health_score) OVER (
                PARTITION BY r.id
                ORDER BY DATE(e.created_at)
                ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
            ) as rolling_7d_avg
        FROM repository_health_events e
        JOIN repositories r ON e.repository_id = r.id
        WHERE r.organization_id = :org_id
          AND e.created_at >= NOW() - INTERVAL ':days days'
        ORDER BY r.full_name, day
    """)
    result = await db.execute(query, {"org_id": org_id, "days": days})
    return [dict(row) for row in result.mappings()]
```

---

### Task 1.5: Pytest Suite

```python
# backend/tests/conftest.py
import pytest
from httpx import AsyncClient, ASGITransport
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.database import Base
from app.dependencies import get_db

TEST_DB_URL = "postgresql+asyncpg://nexus:nexus_dev@localhost:5432/nexus_test"

@pytest.fixture(scope="session")
async def engine():
    eng = create_async_engine(TEST_DB_URL)
    async with eng.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    yield eng
    async with eng.begin() as conn:
        await conn.run_sync(Base.metadata.drop_all)

@pytest.fixture
async def db(engine):
    async_session = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
    async with async_session() as session:
        yield session
        await session.rollback()

@pytest.fixture
async def client(db):
    app.dependency_overrides[get_db] = lambda: db
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as c:
        yield c
    app.dependency_overrides.clear()
```

```python
# backend/tests/integration/test_repositories.py
import pytest

@pytest.mark.asyncio
async def test_create_repository(client, db):
    response = await client.post("/api/v1/repositories", json={
        "github_id": 12345,
        "full_name": "org/repo",
    })
    assert response.status_code == 201
    data = response.json()
    assert data["full_name"] == "org/repo"
    assert data["health_score"] == 0.0

@pytest.mark.asyncio
async def test_get_nonexistent_repository_returns_404(client):
    response = await client.get("/api/v1/repositories/999999")
    assert response.status_code == 404
```

---

### Task 1.6: GitHub Actions CI

```yaml
# .github/workflows/backend-ci.yml
name: Backend CI

on:
  pull_request:
    paths: ["backend/**"]
  push:
    branches: [main]
    paths: ["backend/**"]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: nexus_test
          POSTGRES_USER: nexus
          POSTGRES_PASSWORD: nexus_dev
        ports: ["5432:5432"]
        options: --health-cmd pg_isready --health-interval 5s --health-retries 5

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v4
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: cd backend && pip install -r requirements.txt

      - name: Lint
        run: cd backend && ruff check . && mypy app/

      - name: Test with coverage
        run: |
          cd backend
          pytest --cov=app --cov-report=xml --cov-fail-under=80

      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

**Why coverage gate in CI:** A coverage threshold that only exists locally gets ignored. When CI enforces it on every PR, coverage becomes a team contract, not a suggestion.

---

### Phase 1 Deliverable Checklist
- [ ] `docker-compose up -d` starts all services in under 30 seconds
- [ ] `alembic upgrade head` runs without errors
- [ ] At least 5 REST endpoints with Pydantic schemas (users, repos, orgs)
- [ ] At least one raw SQL window function query
- [ ] Pytest suite with 80%+ coverage and passing CI
- [ ] Bash `scripts/dev.sh` with start, test_all, reset_db commands
- [ ] Feature branch merged via PR with conventional commit messages

**Interview talking point:** "I set up a FastAPI service with SQLAlchemy 2.0 async, Alembic migrations, and a Pytest suite enforced in GitHub Actions. I made a deliberate choice to write some queries in raw SQL — specifically window functions for trending data — because the ORM would have fetched all rows into memory first, which doesn't scale."

---

## Phase 2: Authentication and Security
### Week 3 | Keycloak, OAuth 2.0, OIDC, RBAC, Zero-trust, SSDLC

---

### Sprint Goal
Every API route is protected. Users authenticate through Keycloak. RBAC enforces four roles. Threat model documented before any code written.

**Before coding:**
1. Write the threat model for the auth system (STRIDE template above)
2. Read OAuth 2.0 authorization code flow diagram until you can draw it from memory
3. Document the RBAC model as an ADR

---

### Task 2.1: Threat Model (Do This First)

Create `docs/adr/002-auth-system-threat-model.md` before writing any auth code. The discipline of threat modeling before implementation is what SSDLC means in practice. At Amazon and Apple, this is a required artifact before touching security-sensitive systems.

Key threats to model:
- Token replay attacks (→ short expiry + refresh token rotation)
- RBAC bypass (→ test every role against every protected route)
- JWT algorithm confusion (→ enforce RS256, reject HS256)
- Keycloak admin console exposure (→ bind to internal network only)

---

### Task 2.2: Keycloak Setup

```yaml
# Add to docker-compose.yml
keycloak:
  image: quay.io/keycloak/keycloak:23.0
  environment:
    KEYCLOAK_ADMIN: admin
    KEYCLOAK_ADMIN_PASSWORD: admin_dev
  command: start-dev
  ports: ["8080:8080"]
```

Configure via Keycloak admin UI (localhost:8080):
1. Create realm: `nexus`
2. Create client: `nexus-api` (confidential, authorization code flow)
3. Create roles: `viewer`, `developer`, `admin`, `ops`
4. Create test users, assign roles
5. Export realm config to `infra/keycloak/nexus-realm.json` — this enables reproducible setup

---

### Task 2.3: JWT Validation Middleware

```python
# backend/app/middleware/auth.py
from fastapi import HTTPException, Security, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
import httpx
from jose import jwt, JWTError
from app.config import settings

security = HTTPBearer()

async def get_jwks() -> dict:
    """Fetch public keys from Keycloak for JWT signature verification."""
    url = f"{settings.keycloak_url}/realms/{settings.keycloak_realm}/protocol/openid-connect/certs"
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.json()

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Security(security),
) -> dict:
    """Validate JWT and return the decoded claims.

    Verifies: signature (RS256 only), expiry, issuer, audience.
    Never accepts HS256 — algorithm confusion is a known attack vector.
    """
    token = credentials.credentials
    try:
        jwks = await get_jwks()
        payload = jwt.decode(
            token,
            jwks,
            algorithms=["RS256"],  # RS256 only — reject HS256 explicitly
            audience="nexus-api",
        )
        return payload
    except JWTError as e:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid or expired token",
        )

def require_role(role: str):
    """Dependency factory for role-based access control."""
    def check_role(user: dict = Security(get_current_user)) -> dict:
        user_roles = user.get("realm_access", {}).get("roles", [])
        if role not in user_roles:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail=f"Role '{role}' required",
            )
        return user
    return check_role
```

```python
# Usage in routes
from app.middleware.auth import require_role

@router.delete("/repositories/{repo_id}")
async def delete_repository(
    repo_id: int,
    _: dict = Depends(require_role("admin")),  # only admins can delete
    db: AsyncSession = Depends(get_db),
):
    ...
```

---

### Task 2.4: Zero-trust Service-to-Service Auth

Add Tailscale to your dev environment. Every service communicates over Tailscale, not the public internet:

```bash
# Install Tailscale on your dev machine
# Each Docker service gets a Tailscale sidecar or runs on the same Tailnet

# services bind to 127.0.0.1, not 0.0.0.0 — only accessible via Tailscale
# This is the zero-trust principle: no service is trusted by default
```

Document in ADR: why you chose this over VPC security groups. Answer: defense in depth. VPC groups are perimeter security. Zero-trust assumes the perimeter is already breached.

---

### Phase 2 Deliverable Checklist
- [ ] STRIDE threat model documented as ADR
- [ ] Keycloak realm exported to `infra/keycloak/nexus-realm.json`
- [ ] JWT middleware validates RS256, expiry, issuer, audience
- [ ] RBAC enforced on at least 3 routes with different role requirements
- [ ] Tests that verify each role can and cannot access appropriate routes
- [ ] Keycloak admin console NOT exposed on 0.0.0.0 in production config

---

## Phase 3: Event Pipeline
### Week 4 | Go, MongoDB, Redis pub/sub, WebSockets

---

### Sprint Goal
GitHub webhook events are received, deduplicated, stored in MongoDB, and broadcast to connected dashboard clients via WebSockets — all with sub-100ms latency.

---

### Task 3.1: Go Event Ingestor

```
services/go-ingestor/
├── main.go
├── handlers/
│   ├── webhook.go     # HTTP handler for GitHub webhook POSTs
│   └── health.go
├── processor/
│   ├── dedup.go       # Redis-based deduplication
│   └── publisher.go   # Redis pub/sub publisher
├── storage/
│   └── mongodb.go     # MongoDB event persistence
├── Dockerfile
└── go.mod
```

```go
// services/go-ingestor/handlers/webhook.go
package handlers

import (
    "crypto/hmac"
    "crypto/sha256"
    "encoding/hex"
    "encoding/json"
    "fmt"
    "net/http"
    "github.com/yourusername/nexus/processor"
)

// WebhookHandler verifies GitHub signature and dispatches to processor.
// Why Go here: GitHub can send hundreds of webhook events per second during
// a large push. Go's goroutine model handles this without blocking.
type WebhookHandler struct {
    secret    string
    processor *processor.EventProcessor
}

func (h *WebhookHandler) Handle(w http.ResponseWriter, r *http.Request) {
    if !h.verifySignature(r) {
        http.Error(w, "invalid signature", http.StatusUnauthorized)
        return
    }

    var payload map[string]interface{}
    if err := json.NewDecoder(r.Body).Decode(&payload); err != nil {
        http.Error(w, "invalid json", http.StatusBadRequest)
        return
    }

    eventType := r.Header.Get("X-GitHub-Event")
    go h.processor.Process(eventType, payload)  // non-blocking

    w.WriteHeader(http.StatusAccepted)
}

func (h *WebhookHandler) verifySignature(r *http.Request) bool {
    signature := r.Header.Get("X-Hub-Signature-256")
    if signature == "" {
        return false
    }
    mac := hmac.New(sha256.New, []byte(h.secret))
    // read body, compute HMAC, compare — truncated for brevity
    return hmac.Equal([]byte(fmt.Sprintf("sha256=%s", hex.EncodeToString(mac.Sum(nil)))),
        []byte(signature))
}
```

---

### Task 3.2: Redis Pub/Sub + WebSockets

```python
# backend/app/api/websocket.py
import asyncio
import json
import redis.asyncio as redis
from fastapi import WebSocket
from app.config import settings

async def event_stream(websocket: WebSocket, org_id: int):
    """Stream real-time events to a connected dashboard client.

    Architecture: Go ingestor → Redis pub/sub → FastAPI WebSocket relay → browser.
    Redis decouples the ingestor from the WebSocket server so either can be
    scaled independently.
    """
    await websocket.accept()
    r = redis.from_url(settings.redis_url)
    pubsub = r.pubsub()
    await pubsub.subscribe(f"org:{org_id}:events")

    try:
        async for message in pubsub.listen():
            if message["type"] == "message":
                await websocket.send_text(message["data"])
    except Exception:
        await pubsub.unsubscribe(f"org:{org_id}:events")
        await r.aclose()
```

---

### Phase 3 Deliverable Checklist
- [ ] Go ingestor verifies GitHub HMAC-SHA256 signature on every request
- [ ] Events deduplicated via Redis SETNX with 24h TTL
- [ ] All events stored in MongoDB with eventType, payload, timestamp
- [ ] Real-time event stream visible in browser via WebSocket connection
- [ ] Go unit tests for signature verification and deduplication logic

---

## Phase 4: User Frontend
### Weeks 5-6 | Next.js 14, React, TypeScript, Jest, Playwright

---

### Sprint Goal
A working user dashboard showing repository health scores, real-time event stream, and basic analytics. Fully typed TypeScript. E2E tests with Playwright.

---

### Task 4.1: Next.js 14 App Router Structure

```
frontend/
├── src/
│   ├── app/
│   │   ├── layout.tsx          # Root layout with auth provider
│   │   ├── page.tsx            # Landing/redirect
│   │   ├── dashboard/
│   │   │   └── page.tsx        # Server component — fetches initial data
│   │   ├── repositories/
│   │   │   ├── page.tsx
│   │   │   └── [id]/
│   │   │       └── page.tsx
│   │   └── api/                # Next.js API routes (thin proxy to FastAPI)
│   ├── components/
│   │   ├── RepositoryCard.tsx  # Client component
│   │   ├── EventFeed.tsx       # Client component with WebSocket
│   │   └── HealthChart.tsx     # Recharts wrapper
│   └── lib/
│       ├── api.ts              # Type-safe API client
│       └── auth.ts             # next-auth configuration (Keycloak provider)
```

```typescript
// frontend/src/lib/api.ts
// Type-safe API client — every endpoint is typed, no any
const BASE_URL = process.env.NEXT_PUBLIC_API_URL

interface Repository {
  id: number
  full_name: string
  health_score: number
  last_analyzed_at: string | null
}

async function fetchWithAuth<T>(path: string, options?: RequestInit): Promise<T> {
  const session = await getSession()
  const response = await fetch(`${BASE_URL}${path}`, {
    ...options,
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer ${session?.accessToken}`,
      ...options?.headers,
    },
  })
  if (!response.ok) throw new Error(`API error: ${response.status}`)
  return response.json() as Promise<T>
}

export const api = {
  repositories: {
    list: () => fetchWithAuth<Repository[]>("/api/v1/repositories"),
    get: (id: number) => fetchWithAuth<Repository>(`/api/v1/repositories/${id}`),
  },
}
```

---

### Task 4.2: Playwright E2E Tests

```typescript
// frontend/tests/e2e/dashboard.spec.ts
import { test, expect } from "@playwright/test"

test.beforeEach(async ({ page }) => {
  // Use a fixture user that is pre-authenticated
  await page.goto("/auth/login")
  await page.fill('[name="username"]', "test_developer")
  await page.fill('[name="password"]', "test_password")
  await page.click('[type="submit"]')
  await expect(page).toHaveURL("/dashboard")
})

test("dashboard shows repository list", async ({ page }) => {
  await expect(page.locator('[data-testid="repo-list"]')).toBeVisible()
  await expect(page.locator('[data-testid="repo-card"]').first()).toBeVisible()
})

test("repository health score is displayed as a number between 0 and 100", async ({ page }) => {
  const score = await page.locator('[data-testid="health-score"]').first().textContent()
  const parsed = parseFloat(score ?? "")
  expect(parsed).toBeGreaterThanOrEqual(0)
  expect(parsed).toBeLessThanOrEqual(100)
})
```

---

### Phase 4 Deliverable Checklist
- [ ] TypeScript strict mode enabled with zero `any` in the codebase
- [ ] Dashboard shows live repository health scores
- [ ] WebSocket event feed updates in real time without page refresh
- [ ] Keycloak login flow works end to end
- [ ] Playwright E2E suite covers login, dashboard render, repo detail
- [ ] Jest unit tests for utility functions and API client
- [ ] CI workflow runs Playwright tests against a local server

---

## Phase 5: Admin Panel and Node.js BFF
### Week 7 | Angular, Express, Node.js, JavaScript

---

### Sprint Goal
An Angular admin panel for ops team use, backed by an Express BFF that aggregates data from multiple backend services. This is a second, separate frontend — intentionally different from Next.js to demonstrate both React and Angular.

---

### Task 5.1: Express Backend-for-Frontend

The BFF pattern is used when a frontend needs to aggregate data from multiple microservices. Instead of the Angular app making 5 separate API calls and assembling the result in the browser, the Express BFF does it server-side.

```javascript
// services/express-bff/src/routes/admin.js
// Why JavaScript (not TypeScript) here: demonstrates you can work in both.
// In production at a company like Amazon, many services are plain Node.js.
const express = require("express")
const axios = require("axios")
const router = express.Router()

// Aggregate data for the admin overview page in one request
router.get("/admin/overview", async (req, res) => {
  try {
    const [users, repos, events] = await Promise.all([
      axios.get(`${process.env.BACKEND_URL}/api/v1/admin/users/count`, {
        headers: { Authorization: req.headers.authorization },
      }),
      axios.get(`${process.env.BACKEND_URL}/api/v1/repositories/summary`, {
        headers: { Authorization: req.headers.authorization },
      }),
      axios.get(`${process.env.BACKEND_URL}/api/v1/events/recent?limit=10`, {
        headers: { Authorization: req.headers.authorization },
      }),
    ])

    res.json({
      userCount: users.data.count,
      repositoryCount: repos.data.count,
      avgHealthScore: repos.data.avg_health_score,
      recentEvents: events.data,
    })
  } catch (error) {
    res.status(502).json({ error: "Backend service unavailable" })
  }
})

module.exports = router
```

---

### Task 5.2: Angular Admin Panel

```bash
npx @angular/cli new admin --routing --style=scss --strict
cd admin
ng generate component pages/overview
ng generate component pages/users
ng generate component pages/repositories
ng generate service services/api
ng generate guard guards/auth
```

```typescript
// admin/src/app/services/api.service.ts
import { Injectable } from "@angular/core"
import { HttpClient, HttpHeaders } from "@angular/common/http"
import { Observable } from "rxjs"
import { AuthService } from "./auth.service"

interface AdminOverview {
  userCount: number
  repositoryCount: number
  avgHealthScore: number
  recentEvents: Event[]
}

@Injectable({ providedIn: "root" })
export class ApiService {
  private readonly bffUrl = environment.bffUrl

  constructor(private http: HttpClient, private auth: AuthService) {}

  getAdminOverview(): Observable<AdminOverview> {
    const headers = new HttpHeaders({
      Authorization: `Bearer ${this.auth.getToken()}`,
    })
    return this.http.get<AdminOverview>(`${this.bffUrl}/admin/overview`, { headers })
  }
}
```

**Why Angular instead of more React:** Companies that were building enterprise tools in 2018-2020 standardized on Angular. If you interview with a team that has an Angular codebase, knowing only React is a gap. Both are in your resume. This phase proves it.

---

### Phase 5 Deliverable Checklist
- [ ] Express BFF aggregates 3 backend services into one admin overview response
- [ ] Angular admin panel renders overview with real backend data
- [ ] Angular AuthGuard redirects unauthenticated users to login
- [ ] Admin routes only accessible with `ops` Keycloak role
- [ ] Jest tests for Express BFF routes (mocked backend responses)

---

## Phase 6: AI Core — RAG, Agents, and Multi-LLM
### Weeks 8-9 | LangChain, FAISS, Ollama, Llama, Gemini, RAG, Agent Orchestration, Prompt Engineering

---

### Sprint Goal
A working RAG pipeline that answers questions about a repository's codebase. A LangChain agent that can autonomously analyze PRs and post summaries. Multi-LLM support (Ollama/Llama for local, Gemini for cloud).

**Before coding:**
- Read `AI_Engineering_Best_Practices.md` — full file
- Set up Ollama: `ollama serve` + `ollama pull llama3.1:8b`
- Get a Gemini API key from Google AI Studio

---

### Task 6.1: RAG Pipeline

```python
# backend/app/services/rag_service.py
from langchain_community.embeddings import OllamaEmbeddings
from langchain_community.vectorstores import FAISS
from langchain_community.llms import Ollama
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.chains import RetrievalQA
from langchain.prompts import PromptTemplate

class RepositoryRAGService:
    """RAG pipeline over a repository's documentation and code comments.

    Architecture:
    1. Ingest: fetch README, docs/, and code files from GitHub API
    2. Chunk: split into 512-token chunks with 50-token overlap
    3. Embed: Ollama nomic-embed-text model (runs locally, no API cost)
    4. Store: FAISS index persisted to disk per repository
    5. Query: retrieve top-k chunks, pass to LLM with system prompt
    """

    def __init__(self):
        self.embeddings = OllamaEmbeddings(model="nomic-embed-text")
        self.llm = Ollama(model="llama3.1:8b")
        self.splitter = RecursiveCharacterTextSplitter(
            chunk_size=512,
            chunk_overlap=50,
        )

    async def ingest_repository(self, repo_full_name: str, documents: list[str]) -> None:
        chunks = self.splitter.create_documents(documents)
        vectorstore = FAISS.from_documents(chunks, self.embeddings)
        vectorstore.save_local(f"data/faiss/{repo_full_name.replace('/', '_')}")

    async def query(self, repo_full_name: str, question: str) -> str:
        vectorstore = FAISS.load_local(
            f"data/faiss/{repo_full_name.replace('/', '_')}",
            self.embeddings,
            allow_dangerous_deserialization=True,
        )

        system_prompt = PromptTemplate(
            input_variables=["context", "question"],
            template="""You are a code assistant with knowledge of this repository.
Answer questions about the codebase using only the provided context.
If the context does not contain enough information to answer, say so.

Context:
{context}

Question: {question}
Answer:""",
        )

        chain = RetrievalQA.from_chain_type(
            llm=self.llm,
            retriever=vectorstore.as_retriever(search_kwargs={"k": 5}),
            chain_type_kwargs={"prompt": system_prompt},
        )
        return chain.run(question)
```

---

### Task 6.2: Gemini Integration — Multi-LLM Support

```python
# backend/app/services/llm_factory.py
import google.generativeai as genai
from langchain_community.llms import Ollama
from app.config import settings

def get_llm(provider: str = "ollama"):
    """Return an LLM instance based on provider.

    Why multi-LLM: local Llama for development (no API cost, privacy),
    Gemini for production quality when accuracy matters more than cost.
    """
    if provider == "gemini":
        genai.configure(api_key=settings.gemini_api_key)
        return genai.GenerativeModel("gemini-1.5-pro")
    return Ollama(model="llama3.1:8b")
```

---

### Task 6.3: LangChain Agent with Tools

```python
# backend/app/services/pr_review_agent.py
from langchain.agents import AgentExecutor, create_react_agent
from langchain.tools import Tool
from langchain import hub

class PRReviewAgent:
    """Autonomous agent that analyzes a pull request and produces a review summary.

    The agent follows a ReAct loop:
    1. Thought: what do I need to know?
    2. Action: call a tool (fetch PR diff, search docs, check test coverage)
    3. Observation: what did the tool return?
    4. Repeat until it has enough context to synthesize a review
    """

    def __init__(self, llm, github_client):
        self.tools = [
            Tool(
                name="FetchPRDiff",
                func=github_client.get_pr_diff,
                description="Fetch the code diff for a pull request. Input: 'owner/repo#PR_NUMBER'",
            ),
            Tool(
                name="SearchRepoDocs",
                func=self._search_docs,
                description="Search repository documentation for context. Input: search query string",
            ),
            Tool(
                name="CheckTestCoverage",
                func=github_client.get_coverage_report,
                description="Get test coverage report for the PR branch. Input: 'owner/repo branch_name'",
            ),
        ]
        prompt = hub.pull("hwchase17/react")
        agent = create_react_agent(llm, self.tools, prompt)
        self.executor = AgentExecutor(agent=agent, tools=self.tools, verbose=True)

    def review_pr(self, pr_ref: str) -> str:
        return self.executor.invoke({
            "input": f"Review PR {pr_ref}. Check: code quality, test coverage, potential bugs, missing edge cases."
        })["output"]
```

**Prompt engineering practice:** Write three versions of the system prompt. Test each against 5 sample PRs. Document which produces better output and why. This is the kind of empirical process interviewers at Snowflake specifically probe for when they say "AI-native."

---

### Phase 6 Deliverable Checklist
- [ ] RAG pipeline ingests a real GitHub repo's README and docs
- [ ] Query endpoint returns grounded answers (not hallucinations) with source citations
- [ ] Gemini endpoint works as an alternative to Ollama
- [ ] PR review agent autonomously uses at least 2 tools per review
- [ ] Prompt variants documented in `docs/prompt-engineering/`
- [ ] FAISS index persistence tested (save, reload, query still returns same results)

---

## Phase 7: ML Models — PyTorch, TensorFlow, JAX, Scikit-learn
### Week 10 | PyTorch, TensorFlow, JAX, Scikit-learn, NumPy, Pandas, Flask

---

### Sprint Goal
Three ML models powering Nexus intelligence features: a commit classifier (TF), a code quality predictor (PyTorch), and an anomaly detector on metrics (Scikit-learn). All served via Flask.

---

### Task 7.1: Pandas + NumPy Data Pipeline

Before training any model, process the raw data:

```python
# services/flask-ml/src/data/pipeline.py
import pandas as pd
import numpy as np
from pathlib import Path

def prepare_commit_features(raw_commits: list[dict]) -> pd.DataFrame:
    """Transform raw GitHub commit data into ML-ready feature matrix.

    Features engineered from commit metadata — no code content needed
    for this classifier, just structural signals.
    """
    df = pd.DataFrame(raw_commits)

    # Time-based features
    df["hour_of_day"] = pd.to_datetime(df["timestamp"]).dt.hour
    df["day_of_week"] = pd.to_datetime(df["timestamp"]).dt.dayofweek
    df["is_weekend"] = df["day_of_week"].isin([5, 6]).astype(int)

    # Size features
    df["lines_changed"] = df["additions"] + df["deletions"]
    df["change_ratio"] = np.where(
        df["additions"] > 0,
        df["deletions"] / df["additions"],
        0.0,
    )

    # Message features
    df["message_length"] = df["message"].str.len()
    df["has_issue_ref"] = df["message"].str.contains(r"#\d+", regex=True).astype(int)

    return df[["hour_of_day", "day_of_week", "is_weekend",
               "lines_changed", "change_ratio", "message_length", "has_issue_ref"]]
```

---

### Task 7.2: TensorFlow Commit Classifier

```python
# services/flask-ml/src/models/commit_classifier.py
import tensorflow as tf
import numpy as np

class CommitClassifier:
    """Classifies commits as: bugfix, feature, refactor, chore, test.

    Architecture: simple feedforward network — this is not a problem
    that requires transformers. Choosing the right tool for the complexity
    of the problem is a senior engineering judgment.
    """

    def build(self, input_dim: int, num_classes: int = 5) -> tf.keras.Model:
        model = tf.keras.Sequential([
            tf.keras.layers.Dense(64, activation="relu", input_shape=(input_dim,)),
            tf.keras.layers.Dropout(0.3),
            tf.keras.layers.Dense(32, activation="relu"),
            tf.keras.layers.Dropout(0.2),
            tf.keras.layers.Dense(num_classes, activation="softmax"),
        ])
        model.compile(
            optimizer="adam",
            loss="sparse_categorical_crossentropy",
            metrics=["accuracy"],
        )
        return model

    def train(self, X_train: np.ndarray, y_train: np.ndarray) -> tf.keras.Model:
        model = self.build(X_train.shape[1])
        model.fit(X_train, y_train, epochs=20, batch_size=32, validation_split=0.2)
        model.save("models/commit_classifier.h5")
        return model
```

---

### Task 7.3: PyTorch Code Quality Predictor

```python
# services/flask-ml/src/models/quality_predictor.py
import torch
import torch.nn as nn
import torch.optim as optim

class QualityPredictor(nn.Module):
    """Predicts a 0-100 code quality score from repository metrics.

    Why PyTorch instead of TensorFlow here: using both demonstrates
    you understand each framework's strengths, not just one API.
    PyTorch's dynamic computation graph makes debugging easier during
    research/experimentation.
    """

    def __init__(self, input_dim: int):
        super().__init__()
        self.layers = nn.Sequential(
            nn.Linear(input_dim, 128),
            nn.ReLU(),
            nn.BatchNorm1d(128),
            nn.Dropout(0.3),
            nn.Linear(128, 64),
            nn.ReLU(),
            nn.Linear(64, 1),
            nn.Sigmoid(),
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        return self.layers(x) * 100  # scale to 0-100

def train_quality_predictor(X_train, y_train, epochs: int = 50):
    model = QualityPredictor(input_dim=X_train.shape[1])
    optimizer = optim.Adam(model.parameters(), lr=1e-3)
    criterion = nn.MSELoss()

    X = torch.FloatTensor(X_train)
    y = torch.FloatTensor(y_train).unsqueeze(1)

    for epoch in range(epochs):
        model.train()
        optimizer.zero_grad()
        output = model(X)
        loss = criterion(output, y)
        loss.backward()
        optimizer.step()

    torch.save(model.state_dict(), "models/quality_predictor.pt")
    return model
```

---

### Task 7.4: JAX Experimentation

```python
# services/flask-ml/src/experiments/jax_embeddings.py
import jax
import jax.numpy as jnp
from jax import grad, jit, vmap

# JAX use case: fast gradient computation for embedding fine-tuning experiments
# Why JAX: if you need to customize training loops or implement novel
# gradient-based algorithms, JAX gives you NumPy-like syntax with
# auto-differentiation and JIT compilation

@jit
def cosine_similarity(a: jnp.ndarray, b: jnp.ndarray) -> jnp.ndarray:
    """JIT-compiled cosine similarity — useful for fast FAISS alternative experiments."""
    return jnp.dot(a, b) / (jnp.linalg.norm(a) * jnp.linalg.norm(b))

# vmap vectorizes over a batch automatically — no explicit for loop
batched_similarity = vmap(cosine_similarity, in_axes=(0, None))
```

---

### Task 7.5: Scikit-learn Anomaly Detection

```python
# services/flask-ml/src/models/anomaly_detector.py
from sklearn.ensemble import IsolationForest
from sklearn.preprocessing import StandardScaler
import numpy as np
import joblib

class MetricsAnomalyDetector:
    """Detects anomalous patterns in repository metrics timeseries.

    IsolationForest is appropriate here: unsupervised (no labeled anomalies
    to train on), handles high-dimensional data, fast inference.
    """

    def __init__(self, contamination: float = 0.05):
        self.scaler = StandardScaler()
        self.model = IsolationForest(
            contamination=contamination,
            random_state=42,
            n_estimators=100,
        )

    def fit(self, metrics: np.ndarray) -> None:
        scaled = self.scaler.fit_transform(metrics)
        self.model.fit(scaled)
        joblib.dump(self.scaler, "models/metrics_scaler.pkl")
        joblib.dump(self.model, "models/anomaly_detector.pkl")

    def predict(self, metrics: np.ndarray) -> np.ndarray:
        scaled = self.scaler.transform(metrics)
        # IsolationForest returns -1 for anomalies, 1 for normal
        return self.model.predict(scaled)
```

---

### Task 7.6: Flask ML Server

```python
# services/flask-ml/src/app.py
from flask import Flask, request, jsonify
import torch
import joblib
from models.quality_predictor import QualityPredictor

app = Flask(__name__)

# Load models once at startup
quality_model = QualityPredictor(input_dim=12)
quality_model.load_state_dict(torch.load("models/quality_predictor.pt"))
quality_model.eval()

anomaly_detector = joblib.load("models/anomaly_detector.pkl")
anomaly_scaler = joblib.load("models/metrics_scaler.pkl")

@app.route("/predict/quality", methods=["POST"])
def predict_quality():
    features = request.json["features"]
    with torch.no_grad():
        x = torch.FloatTensor([features])
        score = quality_model(x).item()
    return jsonify({"quality_score": round(score, 2)})

@app.route("/predict/anomaly", methods=["POST"])
def predict_anomaly():
    import numpy as np
    metrics = np.array(request.json["metrics"])
    predictions = anomaly_detector.predict(anomaly_scaler.transform([metrics]))
    return jsonify({"is_anomaly": bool(predictions[0] == -1)})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001)
```

---

### Phase 7 Deliverable Checklist
- [ ] Pandas pipeline cleans and engineers features from raw commit data
- [ ] TensorFlow classifier trained and saved (`commit_classifier.h5`)
- [ ] PyTorch predictor trained and saved (`quality_predictor.pt`)
- [ ] JAX cosine similarity with `@jit` and `vmap` working
- [ ] Scikit-learn IsolationForest anomaly detector working on metrics data
- [ ] Flask server exposes `/predict/quality` and `/predict/anomaly` endpoints
- [ ] FastAPI backend calls Flask ML server for repository health scoring

---

## Phase 8: Multi-Language Microservices
### Week 11 | C#/.NET, Ruby, Spring Boot (Java), C++, Scala, MySQL

---

### Sprint Goal
Four language-specific microservices, each taking responsibility for a domain where that language is the natural choice. This is not arbitrary — the language choice for each service has a documented rationale.

---

### Task 8.1: C# .NET Notification Hub

.NET is dominant in enterprise environments (Microsoft shops, financial services). This notification hub uses SignalR for real-time push, which is the .NET equivalent of WebSockets with built-in connection management.

```
services/dotnet-notif/
├── NexusNotifications/
│   ├── Hubs/
│   │   └── NotificationHub.cs     # SignalR hub
│   ├── Services/
│   │   └── RedisNotificationService.cs
│   ├── Models/
│   │   └── Notification.cs
│   ├── Program.cs
│   └── appsettings.json
└── Dockerfile
```

```csharp
// services/dotnet-notif/NexusNotifications/Hubs/NotificationHub.cs
using Microsoft.AspNetCore.SignalR;
using Microsoft.AspNetCore.Authorization;

[Authorize]
public class NotificationHub : Hub
{
    // Why C# here: SignalR is the gold standard for real-time push in .NET.
    // The C# .NET ecosystem for enterprise notification infrastructure is
    // more mature than the Python/Node alternatives for this pattern.

    public async Task JoinOrganizationGroup(string organizationId)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, $"org-{organizationId}");
    }

    public override async Task OnConnectedAsync()
    {
        var userId = Context.User?.FindFirst("sub")?.Value;
        await base.OnConnectedAsync();
    }
}
```

```csharp
// services/dotnet-notif/NexusNotifications/Services/RedisNotificationService.cs
using StackExchange.Redis;
using Microsoft.AspNetCore.SignalR;

public class RedisNotificationService : BackgroundService
{
    private readonly IHubContext<NotificationHub> _hubContext;
    private readonly IConnectionMultiplexer _redis;

    public RedisNotificationService(IHubContext<NotificationHub> hubContext,
        IConnectionMultiplexer redis)
    {
        _hubContext = hubContext;
        _redis = redis;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        var sub = _redis.GetSubscriber();
        await sub.SubscribeAsync("notifications:*", async (channel, message) =>
        {
            var orgId = channel.ToString().Split(":")[1];
            await _hubContext.Clients.Group($"org-{orgId}")
                .SendAsync("ReceiveNotification", message.ToString(), stoppingToken);
        });
    }
}
```

---

### Task 8.2: Ruby Webhook Preprocessor

Ruby is the language GitHub itself is built in. Webhook processing tooling in Ruby is idiomatic.

```
services/ruby-webhook/
├── app.rb                # Sinatra app (lightweight — no Rails needed here)
├── lib/
│   ├── validator.rb      # HMAC signature validation
│   ├── normalizer.rb     # Normalize webhook payloads to internal format
│   └── queue.rb          # MySQL-backed job queue
├── db/
│   └── schema.rb         # ActiveRecord migrations
├── Gemfile
└── Rakefile
```

```ruby
# services/ruby-webhook/app.rb
require "sinatra"
require "json"
require_relative "lib/validator"
require_relative "lib/normalizer"
require_relative "lib/queue"

post "/webhooks/github" do
  payload = request.body.read
  signature = request.env["HTTP_X_HUB_SIGNATURE_256"]

  unless Validator.valid?(payload, signature, ENV["GITHUB_WEBHOOK_SECRET"])
    halt 401, "Invalid signature"
  end

  event = JSON.parse(payload)
  event_type = request.env["HTTP_X_GITHUB_EVENT"]

  normalized = Normalizer.normalize(event_type, event)
  Queue.enqueue(normalized)

  status 202
end
```

```ruby
# services/ruby-webhook/lib/queue.rb
require "active_record"

# Why MySQL here: demonstrate you can work with MySQL, not just PostgreSQL.
# ActiveRecord works identically with both — same ORM, different adapter.
ActiveRecord::Base.establish_connection(
  adapter: "mysql2",
  host: ENV.fetch("MYSQL_HOST", "localhost"),
  database: ENV.fetch("MYSQL_DATABASE", "nexus_ruby"),
  username: ENV.fetch("MYSQL_USER", "ruby_svc"),
  password: ENV.fetch("MYSQL_PASSWORD"),
)

class WebhookJob < ActiveRecord::Base
  scope :pending, -> { where(status: "pending").order(created_at: :asc) }

  def self.enqueue(payload)
    create!(payload: payload.to_json, status: "pending")
  end
end
```

Rakefile for background processing:
```ruby
# services/ruby-webhook/Rakefile
task :process_jobs do
  loop do
    WebhookJob.pending.limit(10).each do |job|
      begin
        # Forward to Go ingestor
        job.update!(status: "processing")
        forward_to_ingestor(JSON.parse(job.payload))
        job.update!(status: "completed")
      rescue => e
        job.update!(status: "failed", error: e.message)
      end
    end
    sleep 1
  end
end
```

---

### Task 8.3: Spring Boot Analytics API

```
services/spring-analytics/
├── src/main/java/com/nexus/analytics/
│   ├── AnalyticsApplication.java
│   ├── controller/
│   │   └── AnalyticsController.java
│   ├── service/
│   │   └── AnalyticsService.java
│   ├── repository/
│   │   └── HealthEventRepository.java
│   └── model/
│       └── HealthEvent.java
├── src/main/resources/
│   └── application.yml
└── pom.xml
```

```java
// services/spring-analytics/src/main/java/com/nexus/analytics/controller/AnalyticsController.java
package com.nexus.analytics.controller;

import com.nexus.analytics.service.AnalyticsService;
import org.springframework.web.bind.annotation.*;
import org.springframework.security.access.prepost.PreAuthorize;
import java.util.Map;
import java.util.List;

@RestController
@RequestMapping("/api/v1/analytics")
public class AnalyticsController {

    private final AnalyticsService analyticsService;

    public AnalyticsController(AnalyticsService analyticsService) {
        this.analyticsService = analyticsService;
    }

    // Spring Security + Keycloak: @PreAuthorize enforces RBAC at method level
    @GetMapping("/repositories/{orgId}/trends")
    @PreAuthorize("hasRole('developer')")
    public List<Map<String, Object>> getHealthTrends(
            @PathVariable Long orgId,
            @RequestParam(defaultValue = "30") int days) {
        return analyticsService.getHealthTrends(orgId, days);
    }
}
```

---

### Task 8.4: C++ FAISS Wrapper

This exposes a C++ library as a gRPC service. The key learning here is understanding when C++ is justified: FAISS with custom indexing configurations and AVX-512 SIMD instructions runs 3-5x faster than the Python bindings for high-throughput vector search.

```cpp
// services/cpp-faiss/src/faiss_server.cpp
#include <faiss/IndexFlat.h>
#include <faiss/IndexIVFFlat.h>
#include <grpcpp/grpcpp.h>
#include "nexus_faiss.grpc.pb.h"

class FaissServiceImpl final : public NexusFaiss::Service {
public:
    FaissServiceImpl(int dimension) : dimension_(dimension) {
        // IVF index for large-scale approximate nearest neighbor search
        // Why IVF over flat: O(sqrt(n)) search instead of O(n)
        quantizer_ = std::make_unique<faiss::IndexFlatL2>(dimension);
        index_ = std::make_unique<faiss::IndexIVFFlat>(
            quantizer_.get(), dimension, 100  // 100 Voronoi cells
        );
    }

    grpc::Status Search(grpc::ServerContext* context,
                        const SearchRequest* request,
                        SearchResponse* response) override {
        std::vector<float> query(request->embedding().begin(),
                                  request->embedding().end());
        std::vector<faiss::idx_t> indices(request->top_k());
        std::vector<float> distances(request->top_k());

        index_->search(1, query.data(), request->top_k(),
                       distances.data(), indices.data());

        for (int i = 0; i < request->top_k(); i++) {
            response->add_indices(indices[i]);
            response->add_distances(distances[i]);
        }
        return grpc::Status::OK;
    }

private:
    int dimension_;
    std::unique_ptr<faiss::IndexFlatL2> quantizer_;
    std::unique_ptr<faiss::IndexIVFFlat> index_;
};
```

Build with CMake:
```cmake
# services/cpp-faiss/CMakeLists.txt
cmake_minimum_required(VERSION 3.20)
project(nexus_faiss)

find_package(faiss REQUIRED)
find_package(gRPC REQUIRED)

add_executable(faiss_server src/faiss_server.cpp)
target_link_libraries(faiss_server faiss grpc++)
```

---

### Phase 8 Deliverable Checklist
- [ ] C# SignalR hub connects and broadcasts notifications to browser client
- [ ] Ruby Sinatra app validates GitHub signature and enqueues to MySQL
- [ ] Rake task processes jobs from MySQL queue and forwards downstream
- [ ] Spring Boot API returns analytics data with Keycloak RBAC enforced
- [ ] C++ FAISS gRPC service compiles and responds to search requests
- [ ] Python FastAPI calls C++ gRPC service for high-throughput vector search

---

## Phase 9: Analytics Pipeline — Scala Spark, BigQuery, Google APIs
### Week 12 | Apache Spark (Scala), BigQuery, Google APIs (Gmail/Sheets/BigQuery OAuth), Puppeteer

---

### Sprint Goal
Nightly batch analytics job in Scala processes all events, writes to BigQuery, and sends a weekly summary email via Gmail API with a Google Sheets report attached.

---

### Task 9.1: Spark Job in Scala

```scala
// spark/src/main/scala/nexus/HealthAnalyticsJob.scala
package nexus

import org.apache.spark.sql.{SparkSession, DataFrame}
import org.apache.spark.sql.functions._
import org.apache.spark.sql.types._

object HealthAnalyticsJob {

  // Why Scala for Spark: Spark is written in Scala. The Scala API is
  // the most idiomatic, has the best type safety, and gives access to
  // internal Spark APIs the Java/Python APIs don't expose.
  def main(args: Array[String]): Unit = {
    val spark = SparkSession.builder()
      .appName("NexusHealthAnalytics")
      .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
      .getOrCreate()

    import spark.implicits._

    val events = spark.read
      .format("mongodb")
      .option("uri", sys.env("MONGODB_URI"))
      .option("database", "nexus")
      .option("collection", "health_events")
      .load()

    val dailyStats = events
      .filter($"created_at" >= date_sub(current_date(), 1))
      .groupBy($"repository_id", date_trunc("day", $"created_at").as("day"))
      .agg(
        avg($"health_score").as("avg_health"),
        min($"health_score").as("min_health"),
        max($"health_score").as("max_health"),
        count("*").as("event_count"),
        stddev($"health_score").as("health_stddev"),
      )
      .withColumn("volatility", $"health_stddev" / $"avg_health")

    // Write to BigQuery
    dailyStats.write
      .format("bigquery")
      .option("table", s"${sys.env("GCP_PROJECT")}.nexus_analytics.daily_health_stats")
      .mode("append")
      .save()

    spark.stop()
  }
}
```

---

### Task 9.2: Google APIs Integration

```python
# backend/app/services/google_workspace_service.py
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build
import base64
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

class GoogleWorkspaceService:
    """Integrates with Gmail, Sheets, and BigQuery via Google APIs.

    Authentication: OAuth 2.0 service account for server-to-server,
    or user delegation for sending email as the org.
    """

    def send_weekly_report(self, to_email: str, report_data: dict) -> None:
        """Send weekly analytics summary via Gmail API."""
        creds = self._get_credentials()
        service = build("gmail", "v1", credentials=creds)

        message = MIMEMultipart("alternative")
        message["to"] = to_email
        message["subject"] = f"Nexus Weekly Report — {report_data['week']}"
        message.attach(MIMEText(self._render_html_report(report_data), "html"))

        raw = base64.urlsafe_b64encode(message.as_bytes()).decode()
        service.users().messages().send(
            userId="me",
            body={"raw": raw},
        ).execute()

    def write_to_sheets(self, spreadsheet_id: str, data: list[list]) -> None:
        """Append analytics data to a Google Sheet for stakeholder review."""
        creds = self._get_credentials()
        service = build("sheets", "v4", credentials=creds)
        service.spreadsheets().values().append(
            spreadsheetId=spreadsheet_id,
            range="Sheet1!A:Z",
            valueInputOption="USER_ENTERED",
            body={"values": data},
        ).execute()

    def _get_credentials(self) -> Credentials:
        return Credentials.from_service_account_file(
            "credentials/google-service-account.json",
            scopes=[
                "https://www.googleapis.com/auth/gmail.send",
                "https://www.googleapis.com/auth/spreadsheets",
                "https://www.googleapis.com/auth/bigquery",
            ],
        )
```

---

### Task 9.3: Puppeteer External Scraper

```javascript
// services/express-bff/src/scrapers/dependency-scanner.js
// Why Puppeteer: some vulnerability and dependency data is only available
// via rendered web pages (not APIs). This scraper runs headlessly in CI.
const puppeteer = require("puppeteer")

async function scanDependencyVulnerabilities(packageJson) {
  const browser = await puppeteer.launch({ headless: "new" })
  const page = await browser.newPage()

  // Navigate to Snyk or npm audit equivalent
  await page.goto("https://snyk.io/test/github/...", { waitUntil: "networkidle0" })

  const vulnerabilities = await page.evaluate(() => {
    return Array.from(document.querySelectorAll(".vuln-row")).map(row => ({
      package: row.querySelector(".package-name")?.textContent?.trim(),
      severity: row.querySelector(".severity")?.textContent?.trim(),
      version: row.querySelector(".version")?.textContent?.trim(),
    }))
  })

  await browser.close()
  return vulnerabilities
}

module.exports = { scanDependencyVulnerabilities }
```

---

### Phase 9 Deliverable Checklist
- [ ] Scala Spark job runs locally with `spark-submit` and writes to BigQuery
- [ ] BigQuery table queryable via `bq query` CLI
- [ ] Gmail API sends a real email with HTML report
- [ ] Google Sheets API appends data to a spreadsheet
- [ ] Puppeteer scraper runs headlessly (no visible browser window)
- [ ] GitHub Actions scheduled workflow (`cron: '0 2 * * *'`) runs Spark job nightly

---

## Phase 10: Multi-Cloud Infrastructure
### Week 13 | Terraform, AWS, Azure, GCP, Kubernetes, RAID/mdadm, Linux/systemd

---

### Sprint Goal
Nexus deployed to Kubernetes on AWS. Terraform manages all cloud resources. Azure Kubernetes Service configured as a disaster recovery target. RAID storage for on-premises event archive.

---

### Task 10.1: Terraform Multi-Cloud

```
infra/
├── aws/
│   ├── main.tf          # EKS, RDS, ElastiCache, S3
│   ├── variables.tf
│   └── outputs.tf
├── azure/
│   ├── main.tf          # AKS, Azure Blob
│   ├── variables.tf
│   └── outputs.tf
├── gcp/
│   ├── main.tf          # BigQuery dataset, GCS bucket, Cloud Run
│   ├── variables.tf
│   └── outputs.tf
└── modules/
    ├── kubernetes-cluster/
    └── database/
```

```hcl
# infra/aws/main.tf
terraform {
  required_providers {
    aws = { source = "hashicorp/aws", version = "~> 5.0" }
  }
  backend "s3" {
    bucket = "nexus-terraform-state"
    key    = "aws/terraform.tfstate"
    region = "us-west-2"
  }
}

module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  cluster_name    = "nexus-prod"
  cluster_version = "1.28"
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.private_subnets
  node_groups = {
    general = {
      instance_types = ["t3.medium"]
      min_size       = 2
      max_size       = 10
      desired_size   = 3
    }
  }
}

module "rds" {
  source         = "terraform-aws-modules/rds/aws"
  identifier     = "nexus-postgres"
  engine         = "postgres"
  engine_version = "15"
  instance_class = "db.t3.medium"
}
```

```hcl
# infra/azure/main.tf — DR cluster
provider "azurerm" {
  features {}
}

resource "azurerm_kubernetes_cluster" "nexus_dr" {
  name                = "nexus-dr"
  location            = "westus2"
  resource_group_name = azurerm_resource_group.main.name
  dns_prefix          = "nexus-dr"

  default_node_pool {
    name       = "default"
    node_count = 2
    vm_size    = "Standard_D2_v2"
  }

  identity { type = "SystemAssigned" }
}
```

```hcl
# infra/gcp/main.tf
provider "google" {
  project = var.gcp_project
  region  = "us-central1"
}

resource "google_bigquery_dataset" "nexus_analytics" {
  dataset_id = "nexus_analytics"
  location   = "US"
}

resource "google_storage_bucket" "spark_artifacts" {
  name     = "${var.gcp_project}-nexus-spark"
  location = "US"
}
```

---

### Task 10.2: Kubernetes Manifests

```yaml
# infra/kubernetes/backend/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nexus-backend
  namespace: nexus
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nexus-backend
  template:
    metadata:
      labels:
        app: nexus-backend
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8000"
    spec:
      containers:
        - name: backend
          image: ghcr.io/codebun203/nexus-backend:latest
          ports:
            - containerPort: 8000
          envFrom:
            - secretRef:
                name: nexus-backend-secrets
          livenessProbe:
            httpGet:
              path: /health
              port: 8000
            initialDelaySeconds: 10
          resources:
            requests: { memory: "256Mi", cpu: "250m" }
            limits:   { memory: "512Mi", cpu: "500m" }
```

---

### Task 10.3: RAID/mdadm Storage

On your development or home server (or a VM with multiple virtual disks):

```bash
#!/usr/bin/env bash
# scripts/setup-raid.sh — RAID-5 array for raw event archive storage
# Why RAID-5: parity-based redundancy. Can lose 1 disk without data loss.
# Use case in Nexus: on-premises raw webhook event backup before cloud sync.

# List available block devices first — verify before running
lsblk

# Create RAID-5 array across 3 disks
mdadm --create /dev/md0 \
  --level=5 \
  --raid-devices=3 \
  /dev/sdb /dev/sdc /dev/sdd

# Format
mkfs.ext4 /dev/md0

# Mount
mkdir -p /mnt/nexus-archive
mount /dev/md0 /mnt/nexus-archive

# Persist across reboots
echo "/dev/md0 /mnt/nexus-archive ext4 defaults 0 0" >> /etc/fstab

# Save mdadm config
mdadm --detail --scan >> /etc/mdadm/mdadm.conf
update-initramfs -u

# Monitor array health
mdadm --detail /dev/md0
cat /proc/mdstat
```

---

### Task 10.4: Linux/systemd Service Management

```ini
# /etc/systemd/system/nexus-backend.service
# Why systemd: on bare-metal or VMs outside Kubernetes, systemd is the
# standard process supervisor on every Linux distribution.
[Unit]
Description=Nexus FastAPI Backend
After=network.target postgresql.service redis.service
Requires=postgresql.service

[Service]
Type=exec
User=nexus
WorkingDirectory=/opt/nexus/backend
ExecStart=/opt/nexus/venv/bin/uvicorn app.main:app --host 127.0.0.1 --port 8000 --workers 4
Restart=on-failure
RestartSec=5
EnvironmentFile=/opt/nexus/.env
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

```bash
# Deploy and manage with systemctl
sudo systemctl daemon-reload
sudo systemctl enable nexus-backend
sudo systemctl start nexus-backend
sudo systemctl status nexus-backend
sudo journalctl -u nexus-backend -f    # tail logs
```

---

### Phase 10 Deliverable Checklist
- [ ] `terraform apply` creates EKS cluster on AWS (or plan output verified)
- [ ] Azure Terraform config creates AKS DR cluster
- [ ] GCP Terraform creates BigQuery dataset and GCS bucket
- [ ] Kubernetes deployment runs with 3 replicas, health checks, resource limits
- [ ] RAID-5 array created, formatted, mounted, and persisted in fstab
- [ ] systemd unit file enables backend service to start on boot

---

## Phase 11: Observability and CI/CD
### Week 14 | Prometheus, Grafana, Alertmanager, GitHub Actions, Bash

---

### Sprint Goal
Every service emits metrics. Grafana dashboards cover all services. GitHub Actions runs the full pipeline on every PR. Alertmanager pages on critical failures.

---

### Task 11.1: Prometheus Instrumentation

```python
# backend/app/middleware/metrics.py
from prometheus_client import Counter, Histogram, generate_latest
from fastapi import Request, Response
import time

REQUEST_COUNT = Counter(
    "nexus_http_requests_total",
    "Total HTTP requests",
    ["method", "endpoint", "status_code"],
)
REQUEST_LATENCY = Histogram(
    "nexus_http_request_duration_seconds",
    "HTTP request duration",
    ["method", "endpoint"],
    buckets=[0.01, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5],
)

async def metrics_middleware(request: Request, call_next):
    start = time.perf_counter()
    response = await call_next(request)
    duration = time.perf_counter() - start

    REQUEST_COUNT.labels(
        method=request.method,
        endpoint=request.url.path,
        status_code=response.status_code,
    ).inc()
    REQUEST_LATENCY.labels(
        method=request.method,
        endpoint=request.url.path,
    ).observe(duration)

    return response
```

---

### Task 11.2: Full GitHub Actions Pipeline

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  pull_request:
  push:
    branches: [main]

jobs:
  lint-and-test:
    name: Lint, Test, Security Scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Python lint (ruff + mypy)
        run: cd backend && pip install ruff mypy && ruff check . && mypy app/

      - name: Python tests
        run: cd backend && pytest --cov=app --cov-fail-under=80

      - name: TypeScript lint (eslint + tsc)
        run: cd frontend && npm ci && npm run lint && npm run typecheck

      - name: TypeScript tests
        run: cd frontend && npm test -- --watchAll=false

      - name: OWASP Dependency Check
        uses: dependency-check/Dependency-Check_Action@main
        with:
          project: "nexus"
          path: "."
          format: "JSON"

      - name: Secrets scan (gitleaks)
        uses: gitleaks/gitleaks-action@v2

  build-and-push:
    name: Build Docker Images
    needs: lint-and-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Build and push backend
        uses: docker/build-push-action@v5
        with:
          context: backend
          push: true
          tags: ghcr.io/codebun203/nexus-backend:${{ github.sha }}

  deploy-staging:
    name: Deploy to Staging
    needs: build-and-push
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to staging via kubectl
        run: |
          kubectl set image deployment/nexus-backend \
            backend=ghcr.io/codebun203/nexus-backend:${{ github.sha }} \
            -n nexus-staging

  deploy-production:
    name: Deploy to Production
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production   # requires manual approval in GitHub
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/nexus-backend \
            backend=ghcr.io/codebun203/nexus-backend:${{ github.sha }} \
            -n nexus
```

---

### Phase 11 Deliverable Checklist
- [ ] Every service exports a `/metrics` endpoint Prometheus can scrape
- [ ] Grafana dashboard shows request rate, latency p99, error rate for all services
- [ ] Alertmanager fires when error rate exceeds 5% for 5 minutes
- [ ] CI pipeline catches: lint errors, test failures, coverage drops, dependency vulns, secrets
- [ ] Staging deploy is automatic on main merge; production requires manual approval

---

## Phase 12: Network Architecture and Zero-Trust Deep Dive
### Week 15 | Tailscale, Cloudflare Tunnel, TCP/IP, SDN, IoT, OpenFlow, RAID monitoring

---

### Sprint Goal
Document and implement the full network security model. Add a software-defined networking module to Nexus. Demonstrate TCP/IP knowledge through a raw socket monitor.

---

### Task 12.1: Tailscale Mesh Network

```bash
# Every Nexus service node joins the Tailscale network
# Services bind to their Tailscale IP — accessible only to authenticated nodes

# Install on each node
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --authkey=$TAILSCALE_AUTH_KEY --hostname=nexus-backend-prod

# Get the assigned Tailscale IP
TAILSCALE_IP=$(tailscale ip -4)

# Bind FastAPI to Tailscale IP only
uvicorn app.main:app --host $TAILSCALE_IP --port 8000

# No service is reachable from the public internet — only from within the Tailnet
# This is the zero-trust principle: identity is the perimeter, not network location
```

---

### Task 12.2: Cloudflare Tunnel

```bash
# Expose the Nexus dashboard to the internet without opening firewall ports
# Cloudflare acts as reverse proxy; your server makes outbound connection only

cloudflared tunnel login
cloudflared tunnel create nexus-frontend

# Config file
cat > ~/.cloudflared/config.yml << 'EOF'
tunnel: nexus-frontend
credentials-file: /home/user/.cloudflared/<TUNNEL_ID>.json

ingress:
  - hostname: nexus.yourdomain.com
    service: http://localhost:3000
  - service: http_status:404
EOF

cloudflared tunnel run nexus-frontend
```

---

### Task 12.3: TCP/IP Network Monitor Module

```python
# services/network-monitor/src/packet_capture.py
import socket
import struct
from dataclasses import dataclass

@dataclass
class IPHeader:
    version: int
    ihl: int
    ttl: int
    protocol: int
    src_ip: str
    dst_ip: str

def parse_ip_header(data: bytes) -> IPHeader:
    """Parse a raw IPv4 header from packet bytes.

    This demonstrates understanding of TCP/IP at the packet level.
    In Nexus, this module monitors traffic between internal services
    to detect anomalous connection patterns.
    """
    iph = struct.unpack("!BBHHHBBH4s4s", data[:20])
    version_ihl = iph[0]
    return IPHeader(
        version=version_ihl >> 4,
        ihl=(version_ihl & 0xF) * 4,
        ttl=iph[5],
        protocol=iph[6],
        src_ip=socket.inet_ntoa(iph[8]),
        dst_ip=socket.inet_ntoa(iph[9]),
    )

def start_packet_monitor(interface: str = "eth0"):
    """Capture raw IP packets and log inter-service connections."""
    # Requires root/CAP_NET_RAW — run as privileged container
    raw_socket = socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.htons(0x0800))
    raw_socket.bind((interface, 0))

    print(f"Monitoring packets on {interface}...")
    while True:
        packet, _ = raw_socket.recvfrom(65535)
        header = parse_ip_header(packet[14:])  # skip Ethernet header
        print(f"{header.src_ip} → {header.dst_ip} (proto={header.protocol})")
```

---

### Task 12.4: SDN/OpenFlow Network Policy

```python
# services/network-monitor/src/openflow_controller.py
# Why OpenFlow: software-defined networking lets you programmatically
# control which traffic is allowed between services — the code IS the
# network policy, not firewall rules in a UI.

from ryu.base import app_manager
from ryu.controller import ofp_event
from ryu.controller.handler import MAIN_DISPATCHER, set_ev_cls
from ryu.ofproto import ofproto_v1_3
from ryu.lib.packet import packet, ethernet, ipv4

class NexusNetworkController(app_manager.RyuApp):
    """OpenFlow controller enforcing zero-trust network policies for Nexus.

    Policy: each service may only communicate with its explicitly allowed peers.
    The controller installs flow rules in the virtual switch (OVS) to enforce this.
    """
    OFP_VERSIONS = [ofproto_v1_3.OFP_VERSION]

    ALLOWED_FLOWS = {
        "10.0.0.1": ["10.0.0.2", "10.0.0.5"],  # backend → postgres, redis only
        "10.0.0.3": ["10.0.0.1"],                # frontend → backend only
    }

    @set_ev_cls(ofp_event.EventOFPPacketIn, MAIN_DISPATCHER)
    def packet_in_handler(self, ev):
        pkt = packet.Packet(ev.msg.data)
        ip_pkt = pkt.get_protocol(ipv4.ipv4)
        if not ip_pkt:
            return

        src = ip_pkt.src
        dst = ip_pkt.dst
        allowed_dsts = self.ALLOWED_FLOWS.get(src, [])

        if dst in allowed_dsts:
            self._install_flow(ev, src, dst)  # allow
        else:
            self._drop_packet(ev, src, dst)   # zero-trust: deny by default
```

---

### Phase 12 Deliverable Checklist
- [ ] All Nexus services communicate exclusively over Tailscale IPs
- [ ] Cloudflare Tunnel exposes the frontend without an open firewall port
- [ ] TCP/IP packet monitor logs inter-service connections
- [ ] OpenFlow controller with at least 3 explicit allow rules
- [ ] ADR documenting the network security model and trust boundaries
- [ ] RAID health monitoring via `mdadm --detail` in a cron job

---

## Phase 13: Swift iOS Companion App
### Week 16 | Swift, SwiftUI, iOS

---

### Sprint Goal
An iOS app that shows Nexus metrics, receives push notifications for anomalies, and displays the real-time event feed.

---

### Task 13.1: Swift iOS App

```swift
// mobile/ios/Nexus/Views/DashboardView.swift
import SwiftUI
import Combine

struct DashboardView: View {
    @StateObject private var viewModel = DashboardViewModel()

    var body: some View {
        NavigationStack {
            List {
                Section("Repository Health") {
                    ForEach(viewModel.repositories) { repo in
                        NavigationLink(destination: RepositoryDetailView(repo: repo)) {
                            RepositoryRowView(repo: repo)
                        }
                    }
                }

                Section("Recent Events") {
                    ForEach(viewModel.recentEvents) { event in
                        EventRowView(event: event)
                    }
                }
            }
            .navigationTitle("Nexus")
            .refreshable { await viewModel.refresh() }
        }
        .task { await viewModel.loadInitialData() }
    }
}
```

```swift
// mobile/ios/Nexus/ViewModels/DashboardViewModel.swift
import Foundation
import Combine

@MainActor
class DashboardViewModel: ObservableObject {
    @Published var repositories: [Repository] = []
    @Published var recentEvents: [Event] = []
    @Published var isLoading = false

    private let apiClient = NexusAPIClient()

    func loadInitialData() async {
        isLoading = true
        do {
            async let repos = apiClient.fetchRepositories()
            async let events = apiClient.fetchRecentEvents(limit: 20)
            (repositories, recentEvents) = try await (repos, events)
        } catch {
            print("Failed to load: \(error)")
        }
        isLoading = false
    }

    func refresh() async {
        await loadInitialData()
    }
}
```

```swift
// mobile/ios/Nexus/Services/NexusAPIClient.swift
import Foundation

struct NexusAPIClient {
    private let baseURL = URL(string: "https://nexus.yourdomain.com/api/v1")!
    private let session = URLSession.shared

    func fetchRepositories() async throws -> [Repository] {
        let url = baseURL.appendingPathComponent("repositories")
        var request = URLRequest(url: url)
        request.setValue("Bearer \(AuthManager.shared.accessToken)", forHTTPHeaderField: "Authorization")
        let (data, _) = try await session.data(for: request)
        return try JSONDecoder().decode([Repository].self, from: data)
    }

    func fetchRecentEvents(limit: Int) async throws -> [Event] {
        var components = URLComponents(url: baseURL.appendingPathComponent("events"), resolvingAgainstBaseURL: true)!
        components.queryItems = [URLQueryItem(name: "limit", value: "\(limit)")]
        let (data, _) = try await session.data(from: components.url!)
        return try JSONDecoder().decode([Event].self, from: data)
    }
}
```

---

### Phase 13 Deliverable Checklist
- [ ] iOS app builds and runs in Xcode simulator
- [ ] Dashboard view shows repositories fetched from live FastAPI backend
- [ ] Pull-to-refresh works
- [ ] OAuth token stored securely in iOS Keychain
- [ ] Push notification received when anomaly detector fires

---

## Phase 14: SSDLC Hardening and Full System Completion
### Weeks 17-18 | Security audit, 90%+ coverage, documentation, IoT monitoring

---

### Sprint Goal
The project is production-ready. Security audit complete. Coverage above 90%. All documentation finalized. Every service has been demo'd end to end.

---

### Task 14.1: Security Audit Checklist

Work through the OWASP Top 10 for each surface:

```
API Security (FastAPI):
□ A01 Broken Access Control: test every route with wrong role, no token
□ A02 Cryptographic Failures: verify all tokens use RS256, no HS256 accepted
□ A03 Injection: SQL injection tests on all query params
□ A04 Insecure Design: review threat model from Phase 2 — still accurate?
□ A05 Security Misconfiguration: no debug mode in prod, no default passwords
□ A06 Vulnerable Components: OWASP dep check passes in CI
□ A07 Auth Failures: verify token refresh, session expiry, logout invalidation
□ A09 Logging: all auth failures logged with enough context to investigate

Frontend (Next.js/Angular):
□ XSS: all user content rendered via framework (no dangerouslySetInnerHTML)
□ CSRF: SameSite=Strict cookie attribute on session cookies
□ Content Security Policy header set on all responses

Infrastructure:
□ No service binds to 0.0.0.0 without Tailscale or Cloudflare protection
□ All secrets in environment variables, not in code or logs
□ No admin UIs (Keycloak, Grafana) accessible from public internet
```

---

### Task 14.2: IoT Monitoring Integration

Complete the SDN/IoT section of Nexus by adding a lightweight IoT device monitor:

```python
# services/network-monitor/src/iot_monitor.py
import asyncio
import socket
from dataclasses import dataclass, field

@dataclass
class IoTDevice:
    ip: str
    mac: str
    hostname: str
    last_seen: float
    open_ports: list[int] = field(default_factory=list)

async def scan_network_segment(cidr: str) -> list[IoTDevice]:
    """Discover IoT devices on the local network using ARP scanning.

    Why IoT monitoring in Nexus: the zero-trust security model needs to
    account for unmanaged devices on the same physical network as internal
    services. An IoT device with a compromised firmware could pivot to
    internal services if the network has no segmentation.
    """
    devices = []
    # ARP scan using scapy (simplified — production would use proper ARP library)
    for i in range(1, 255):
        ip = f"{cidr}.{i}"
        try:
            hostname = socket.gethostbyaddr(ip)[0]
            devices.append(IoTDevice(ip=ip, mac="", hostname=hostname, last_seen=0))
        except socket.herror:
            pass
    return devices
```

---

### Task 14.3: Final Coverage Push

```bash
# Get current coverage report with per-file breakdown
pytest --cov=app --cov-report=html --cov-report=term-missing

# Open htmlcov/index.html and find files below 80%
# Write targeted tests for each uncovered branch

# Common coverage gaps to look for:
# - Error handling paths (what happens when the DB is down?)
# - Edge cases in validators (empty strings, null values, boundary numbers)
# - The else branches of permission checks
# - Exception handlers that log but don't raise
```

---

### Phase 14 Deliverable Checklist
- [ ] OWASP Top 10 checklist complete, findings documented and fixed
- [ ] Every service has 90%+ test coverage
- [ ] IoT device scanner integrated into network monitor
- [ ] All ADRs written (at minimum: database choice, auth system, network model, LLM provider)
- [ ] README.md in each service directory explains setup, run, and test commands
- [ ] Full end-to-end demo: webhook in → event processed → dashboard updates → alert fires → iOS notification

---

## What You Can Now Say in Every Interview

### Architecture question
"Nexus has 14 services across 8 languages. The design principle was separation of concerns with the right tool for each job. Go for high-frequency event ingestion because of goroutines. Scala for Spark because it's Spark's native language. C# for the notification hub because SignalR is the mature real-time push solution in the .NET ecosystem. C++ for the FAISS wrapper because SIMD-optimized vector operations run 3-5x faster than the Python bindings at volume."

### AI question
"I built two kinds of AI features. The RAG pipeline uses LangChain, FAISS, and local Llama for grounded question-answering over repository documentation — it can only answer from the actual docs, not from model memory. The agentic review system is different — it's a ReAct loop that autonomously decides which tools to call based on what it discovers in the PR diff. I also added Gemini as a second LLM provider so I can compare output quality. The prompt engineering work involved testing three system prompt variants empirically against real PRs."

### Security question
"The security model is zero-trust end to end. No service trusts another without verified identity. Keycloak issues JWT tokens, every service validates the signature, expiry, issuer, and audience independently. Services communicate only over Tailscale, so even if someone gets network access, they cannot reach a service without a valid Tailscale identity. I wrote a threat model using STRIDE before implementing auth, not after — and the threat model is checked into the repo as an ADR."

### Infrastructure question
"Terraform manages resources on all three clouds simultaneously. AWS is primary (EKS, RDS), Azure is the DR target (AKS), GCP hosts BigQuery and the Spark artifact store. State files are in S3 with locking. Every Kubernetes service has resource limits, health checks, and Prometheus scrape annotations. The CI/CD pipeline has two gates: automated (lint, tests, security scan, coverage) and manual approval for production promotion."

---

## Quick Reference: Which Phase Uses Which Skill

| Skill | Phase |
|---|---|
| Python, FastAPI, SQLAlchemy, Alembic, Pydantic, Pytest, PostgreSQL, SQL, Docker, Bash | 1 |
| Keycloak, OAuth 2.0, OIDC, RBAC, zero-trust, SSDLC | 2 |
| Go, MongoDB, Redis, WebSockets | 3 |
| TypeScript, Next.js 14, React, Jest, Playwright | 4 |
| Angular, Node.js, Express, JavaScript | 5 |
| LangChain, FAISS, Ollama, Llama, Gemini, RAG, Agent orchestration, Agentic workflow, Prompt engineering | 6 |
| PyTorch, TensorFlow, JAX, Scikit-learn, NumPy, Pandas, Flask | 7 |
| C#, Ruby, Spring Boot, C++, Scala, MySQL | 8 |
| Apache Spark (Scala), BigQuery, Google APIs, Puppeteer | 9 |
| Terraform, AWS, Azure, GCP, Kubernetes, RAID/mdadm, Linux/systemd | 10 |
| Prometheus, Grafana, GitHub Actions, CI/CD | 11 |
| Tailscale, Cloudflare Tunnel, TCP/IP, SDN, IoT, OpenFlow | 12 |
| Swift | 13 |
| SSDLC hardening, IoT monitoring, final coverage | 14 |
