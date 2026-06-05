# Nexus: AI-Native Developer Intelligence Platform
## Full Production Project Plan for Interview Preparation

---

## What This Project Is

Nexus is a full-stack internal developer tooling platform. It connects to GitHub repositories, monitors code quality and deployment health, surfaces AI-powered insights, and gives engineering teams a unified dashboard for their systems.

This is not a toy project. Every architectural decision, technology choice, and implementation pattern should be made the same way a professional engineer at Apple, Amazon, or Snowflake would make it.

By the time this project is complete, you will have a production-grade system you can demo, discuss in depth, and point to as evidence of your capabilities in any interview.

---

## Why This Project

Every company you are interviewing with will recognize this domain:
- **Apple SDET:** You are building exactly what the ASE Media Platform Quality team builds: internal full-stack tooling with AI integration
- **Amazon SDE:** Distributed systems, event-driven architecture, observability, CI/CD
- **Snowflake:** Data pipelines, distributed infrastructure, AI-native tooling

It also covers every skill on your resume in a natural, non-forced way.

---

## Skills Coverage Map

| Skill | Where Used in Nexus |
|---|---|
| Python | FastAPI backend, data pipelines, Spark jobs, tests |
| TypeScript | Next.js 14 frontend, Node.js services |
| JavaScript | Express middleware, GitHub webhook handler |
| SQL | PostgreSQL schema design, complex queries |
| Go | High-performance event ingestion service |
| Java | Apache Spark analytics job |
| Bash | Deployment scripts, CI pipeline steps |
| FastAPI | Primary backend framework |
| Next.js 14 | Frontend dashboard |
| React | Component library |
| Node.js | Webhook receiver service |
| Express | Node.js routing |
| SQLAlchemy | ORM for PostgreSQL |
| Alembic | Database migrations |
| Pydantic | Request/response schemas |
| LangChain | RAG pipeline for AI assistant |
| Pytest | Full backend test suite |
| Jest | Frontend test suite |
| Pandas | Data processing in analytics pipeline |
| Docker | Every service containerized |
| Keycloak | OAuth 2.0/OIDC/RBAC identity provider |
| Prometheus | Metrics from every service |
| Grafana | Dashboards and alerting |
| Kubernetes | Production deployment |
| Terraform | Infrastructure as code on AWS |
| AWS | EKS, RDS, ElastiCache, S3 |
| PostgreSQL | Primary relational database |
| MongoDB | Event log and document storage |
| Redis | Caching and pub/sub |
| BigQuery | Long-term analytics data warehouse |
| Apache Spark | Batch analytics processing |
| OAuth 2.0 / OIDC | Auth via Keycloak |
| RBAC | Role-based access control |
| Zero-trust | Network architecture |
| Ollama / Llama | Local LLM for AI features |
| FAISS | Vector search for RAG |
| RAG pipelines | AI assistant over repository docs |
| Agent orchestration | AI-powered code review suggestions |
| GitHub Actions | CI/CD pipeline |
| WebSockets | Real-time dashboard updates |
| Google APIs | BigQuery OAuth integration |
| Puppeteer / Playwright | E2E test suite |

---

## Project Architecture Overview

```
                    ┌─────────────────┐
                    │   Next.js 14    │  ← TypeScript, React, Jest
                    │   (Frontend)    │
                    └────────┬────────┘
                             │ HTTPS / WebSocket
                    ┌────────▼────────┐
                    │   API Gateway   │  ← OAuth token validation
                    │   (FastAPI)     │
                    └────┬───┬───┬───┘
           ┌─────────────┘   │   └──────────────┐
  ┌────────▼───┐    ┌────────▼───┐    ┌─────────▼──┐
  │  Users /   │    │  Repos /   │    │    AI       │
  │  Auth      │    │  Events    │    │  Service    │
  │  Service   │    │  Service   │    │  (LangChain)│
  └────┬───────┘    └─────┬──────┘    └────────────┘
       │                  │
┌──────▼──┐    ┌──────────▼─────────┐    ┌──────────┐
│Keycloak │    │ Go Event Ingestor  │    │  Ollama  │
│(OAuth)  │    │ (GitHub Webhooks)  │    │  (LLM)   │
└─────────┘    └──────────┬─────────┘    └──────────┘
                          │
          ┌───────────────┼───────────────┐
   ┌──────▼──┐    ┌───────▼───┐    ┌──────▼──┐
   │Postgres │    │  MongoDB  │    │  Redis  │
   │(primary)│    │  (events) │    │ (cache) │
   └─────────┘    └───────────┘    └─────────┘
                          │
              ┌───────────▼───────────┐
              │  Apache Spark + BigQ  │
              │  (Analytics Pipeline) │
              └───────────────────────┘
                          │
              ┌───────────▼───────────┐
              │   Prometheus/Grafana  │
              │   (Observability)     │
              └───────────────────────┘
```

---

## Before You Write Any Code: Setup Checklist

Read these files first. Do not skip this step.

- [ ] `Git_Version_Control_Complete_Guide.md` — sections 2, 3, 5, 10, 11, 17
- [ ] `Professional_Coding_Standards.md` — full file
- [ ] `AI_Engineering_Best_Practices.md` — full file

Then complete this setup:

```bash
# 1. Create the project directory
mkdir nexus
cd nexus
git init

# 2. Create CLAUDE.md immediately
# (See template at end of this file)

# 3. Set up .cursor/rules/
# (See templates at end of this file)

# 4. Create the initial project structure
mkdir -p {backend,frontend,infra,services/go-ingestor,services/spark,docs,scripts,.github/workflows}

# 5. Create a comprehensive .gitignore before adding any files
# (Python + Node + Java + Go + secrets)

# 6. Make your first commit
git add .gitignore CLAUDE.md
git commit -m "chore: project scaffold with CLAUDE.md and gitignore"

# 7. Push to GitHub
git remote add origin https://github.com/YOUR_USERNAME/nexus.git
git push -u origin main
```

---

## Phase 1: Foundation and Data Layer
### Weeks 1-2 | Python, SQL, Docker, PostgreSQL, SQLAlchemy, Alembic, Pydantic, Pytest

---

### What you are learning in this phase

Before writing code, read:
- `Python3_Complete_Guide.md` sections 3, 8, 10, 11, 12, 14, 20
- `Pytest_Complete_Guide.md` sections 1-9

The concepts introduced: FastAPI application structure, SQLAlchemy 2.0 ORM patterns, database migrations with Alembic, Pydantic schema validation, and test-driven development from day one.

---

### Phase 1 Tasks

#### Task 1.1: Project scaffold and Docker environment

Create `docker-compose.yml` in the root:

```yaml
version: "3.9"
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: nexus
      POSTGRES_USER: nexus
      POSTGRES_PASSWORD: nexus_dev
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  mongodb:
    image: mongo:7
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  postgres_data:
  mongo_data:
```

Start it: `docker-compose up -d`

**Why Docker first:** Every service in your stack should be containerized from day one. Starting databases locally without Docker creates environment drift. Your production and development environments should be as similar as possible.

---

#### Task 1.2: FastAPI backend scaffold

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py          # FastAPI app instance and router registration
│   ├── config.py        # Settings via pydantic-settings
│   ├── database.py      # SQLAlchemy engine and session
│   ├── models/          # SQLAlchemy ORM models
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── organization.py
│   │   └── repository.py
│   ├── schemas/         # Pydantic request/response schemas
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── repository.py
│   ├── api/             # FastAPI route handlers
│   │   ├── __init__.py
│   │   ├── users.py
│   │   └── repositories.py
│   ├── services/        # Business logic (no direct DB access)
│   │   ├── __init__.py
│   │   └── user_service.py
│   └── dependencies.py  # FastAPI dependency injection
├── tests/
│   ├── conftest.py
│   ├── unit/
│   └── integration/
├── alembic/
│   ├── env.py
│   └── versions/
├── alembic.ini
├── requirements.txt
├── requirements-dev.txt
└── Dockerfile
```

**Teaching note — why this structure matters:**
The separation between `models/` (database structure), `schemas/` (API contract), and `services/` (business logic) is the pattern used at every serious company. Models define what is in the database. Schemas define what the API accepts and returns. Services contain logic. Handlers in `api/` are thin: they validate input, call a service, return the result. This makes testing easier (you can test service logic without HTTP) and keeps each layer focused.

---

#### Task 1.3: Database models

Build these SQLAlchemy 2.0 models with proper relationships:

```python
# app/models/organization.py
from sqlalchemy import String, DateTime
from sqlalchemy.orm import Mapped, mapped_column, relationship
from datetime import datetime, timezone
from app.database import Base

class Organization(Base):
    __tablename__ = "organizations"

    id: Mapped[str] = mapped_column(String(36), primary_key=True)
    name: Mapped[str] = mapped_column(String(255), nullable=False)
    slug: Mapped[str] = mapped_column(String(100), unique=True, nullable=False)
    created_at: Mapped[datetime] = mapped_column(
        DateTime(timezone=True),
        default=lambda: datetime.now(timezone.utc)
    )
    repositories: Mapped[list["Repository"]] = relationship(back_populates="organization")
    members: Mapped[list["OrganizationMember"]] = relationship(back_populates="organization")
```

**Teaching note — SQLAlchemy 2.0 syntax:**
The `Mapped[]` type annotation syntax is SQLAlchemy 2.0. It is significantly different from 1.x. Always use 2.0 syntax in new code. Interviewers who write Python professionally will notice if you mix old and new patterns.

---

#### Task 1.4: Alembic migrations

```bash
cd backend
alembic init alembic
alembic revision --autogenerate -m "create initial schema"
alembic upgrade head
```

Practice the expand-contract pattern from `Professional_Coding_Standards.md`. When you add a column to an existing table, do it in two migrations: first add it as nullable, then make it non-nullable after all rows are populated.

---

#### Task 1.5: Pydantic schemas and FastAPI endpoints

Write the full CRUD API for organizations and repositories. Every endpoint must have:
- Pydantic request schema with field validation
- Pydantic response schema
- Type hints
- A one-line docstring if non-obvious

```python
# app/schemas/repository.py
from pydantic import BaseModel, Field, HttpUrl
from datetime import datetime

class RepositoryCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=255)
    url: HttpUrl
    default_branch: str = Field(default="main", max_length=100)
    organization_id: str

class RepositoryResponse(BaseModel):
    id: str
    name: str
    url: str
    default_branch: str
    organization_id: str
    created_at: datetime

    model_config = {"from_attributes": True}
```

---

#### Task 1.6: Write tests from day one

Target: 80%+ coverage by end of Phase 1.

Write tests as you build each piece, not after. Use the patterns from `Pytest_Complete_Guide.md`.

Required test types:
- Unit tests for all service functions
- Integration tests for all API endpoints (using FastAPI TestClient)
- Fixtures in `conftest.py` for database session and test data
- Parametrized tests for validation edge cases

```bash
# Run with coverage
pytest tests/ --cov=app --cov-report=term-missing
```

---

#### Phase 1 Deliverable Checklist

- [ ] Docker Compose runs all three databases locally
- [ ] FastAPI app starts with `uvicorn app.main:app --reload`
- [ ] All five models created with proper SQLAlchemy 2.0 syntax
- [ ] Alembic migrations create the schema cleanly from scratch
- [ ] Full CRUD API for organizations and repositories
- [ ] Every endpoint has a Pydantic request and response schema
- [ ] 80%+ test coverage
- [ ] All tests pass in GitHub Actions on every push
- [ ] Clean commit history following Conventional Commits format

---

## Phase 2: Authentication and Security
### Week 3 | OAuth 2.0, OIDC, Keycloak, RBAC, JWT, Zero-Trust

---

### What you are learning

Read: `Snowflake_Interview_Prep.md` Q13, Q14, Q15 (auth and security section)

The concepts: OAuth 2.0 authorization code flow, OIDC identity layer, JWT validation, RBAC enforcement, and zero-trust network design.

---

### Phase 2 Tasks

#### Task 2.1: Keycloak setup

Add Keycloak to `docker-compose.yml`:

```yaml
  keycloak:
    image: quay.io/keycloak/keycloak:24.0
    command: start-dev
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    ports:
      - "8080:8080"
```

Configure via the Keycloak admin UI at `http://localhost:8080`:
1. Create a realm called `nexus`
2. Create a client called `nexus-backend` with client credentials flow
3. Create a client called `nexus-frontend` with authorization code + PKCE flow
4. Create roles: `org:admin`, `org:developer`, `org:viewer`
5. Create two test users with different roles

**Teaching note — why Keycloak:**
Every enterprise uses an identity provider. Keycloak is the open-source standard. The patterns you learn here (JWT validation, JWKS endpoint, role claims) are identical to what Azure AD, Okta, and AWS Cognito use. Understanding one means understanding all of them.

---

#### Task 2.2: JWT validation middleware

Write FastAPI middleware that:
1. Extracts the Bearer token from the Authorization header
2. Fetches the JWKS (public keys) from Keycloak's JWKS endpoint
3. Validates the signature, expiration, issuer, and audience
4. Extracts role claims and attaches them to the request

```python
# app/middleware/auth.py
from fastapi import Request, HTTPException
from jose import jwt, JWTError
import httpx

JWKS_URL = "http://keycloak:8080/realms/nexus/protocol/openid-connect/certs"

async def get_current_user(request: Request) -> dict:
    """Validate JWT and return decoded claims."""
    token = _extract_bearer_token(request)
    jwks = await _fetch_jwks()
    try:
        claims = jwt.decode(
            token,
            jwks,
            algorithms=["RS256"],
            audience="nexus-backend",
        )
        return claims
    except JWTError as e:
        raise HTTPException(status_code=401, detail=f"Invalid token: {e}")
```

**CRITICAL:** Verify that `exp` claim validation is enabled (it is by default in `python-jose`, but explicitly test for it). See the AI failure story in `AI_Engineering_Best_Practices.md`.

---

#### Task 2.3: RBAC dependency

```python
# app/dependencies.py
from fastapi import Depends, HTTPException
from app.middleware.auth import get_current_user

def require_role(required_role: str):
    async def check_role(claims: dict = Depends(get_current_user)):
        roles = claims.get("realm_access", {}).get("roles", [])
        if required_role not in roles:
            raise HTTPException(status_code=403, detail="Insufficient permissions")
        return claims
    return check_role

# Usage in a handler:
@router.delete("/repositories/{repo_id}")
async def delete_repository(
    repo_id: str,
    claims: dict = Depends(require_role("org:admin")),
):
```

---

#### Task 2.4: Test the auth layer

This is where most engineers under-test. Required test cases:
- Valid token passes validation
- Expired token returns 401
- Token with wrong audience returns 401
- Token with wrong issuer returns 401
- Token missing required role returns 403
- Missing Authorization header returns 401
- Malformed Bearer token returns 401

Use `unittest.mock` to mock the JWKS fetch so tests do not need a running Keycloak.

---

#### Phase 2 Deliverable Checklist

- [ ] Keycloak running in Docker with realm, clients, and roles configured
- [ ] JWT validation middleware that checks signature, exp, iss, aud
- [ ] RBAC dependency that enforces role requirements per endpoint
- [ ] All auth endpoints return correct HTTP status codes
- [ ] Auth middleware unit tested with mocked JWKS
- [ ] No endpoint is reachable without a valid token

---

## Phase 3: Event Ingestion Service
### Week 4 | Go, WebSockets, MongoDB, Redis Pub/Sub, Node.js

---

### What you are learning

This phase introduces Go for a performance-critical service and MongoDB for event storage. It also adds Redis pub/sub for real-time communication.

---

### Phase 3 Tasks

#### Task 3.1: Go event ingestor

This service receives GitHub webhooks and writes events to MongoDB. It is written in Go because webhook ingestion is I/O-heavy and high-frequency. Go's concurrency model handles this efficiently.

```
services/go-ingestor/
├── main.go
├── handlers/
│   ├── webhook.go       # HTTP handler for GitHub webhooks
│   └── health.go
├── models/
│   └── event.go         # MongoDB document structure
├── storage/
│   └── mongo.go         # MongoDB client and write operations
├── publisher/
│   └── redis.go         # Redis pub/sub publisher
├── Dockerfile
└── go.mod
```

The ingestor does three things when a webhook arrives:
1. Validates the GitHub webhook signature (HMAC-SHA256)
2. Writes the event to MongoDB
3. Publishes a message to a Redis channel so the FastAPI backend can push it to connected WebSocket clients

**Teaching note — why Go here:**
Your resume lists Go. This is where you demonstrate it. The service is simple enough to build in a week while showing you understand Go's concurrency model, error handling (no exceptions, return values), and the standard library. This also demonstrates polyglot architecture, which is a real pattern at FAANG companies.

---

#### Task 3.2: MongoDB event schema

```go
// models/event.go
type GitHubEvent struct {
    ID           primitive.ObjectID `bson:"_id,omitempty"`
    EventType    string             `bson:"event_type"`
    RepositoryID string             `bson:"repository_id"`
    Payload      bson.M             `bson:"payload"`
    ReceivedAt   time.Time          `bson:"received_at"`
    Processed    bool               `bson:"processed"`
}
```

**Why MongoDB for events:** Events have irregular structures. A push event looks different from a pull request event from a deployment event. MongoDB's flexible schema is appropriate here. PostgreSQL is better for structured relational data (users, organizations). This is exactly the tradeoff you will be asked about in interviews.

---

#### Task 3.3: Redis pub/sub

When the Go ingestor receives a push event for repository X, it publishes to Redis channel `repo:X:events`. The FastAPI backend subscribes to those channels and forwards messages to WebSocket clients watching that repository.

```python
# app/websocket/manager.py
import redis.asyncio as redis

class ConnectionManager:
    def __init__(self):
        self.active_connections: dict[str, list[WebSocket]] = {}
        self.redis = redis.from_url("redis://redis:6379")

    async def subscribe_to_repo(self, repo_id: str, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.setdefault(repo_id, []).append(websocket)
        pubsub = self.redis.pubsub()
        await pubsub.subscribe(f"repo:{repo_id}:events")
        async for message in pubsub.listen():
            if message["type"] == "message":
                await websocket.send_text(message["data"])
```

---

#### Phase 3 Deliverable Checklist

- [ ] Go ingestor accepts GitHub webhooks and validates signatures
- [ ] Events written to MongoDB with correct schema
- [ ] Redis pub/sub publishes events from Go, consumed by Python
- [ ] FastAPI WebSocket endpoint streams events to connected clients
- [ ] Health check endpoint on Go service returns 200
- [ ] Go service has a Dockerfile

---

## Phase 4: Frontend Dashboard
### Weeks 5-6 | Next.js 14, React, TypeScript, Jest, Playwright

---

### What you are learning

Server Components vs Client Components in Next.js 14, TypeScript type safety, React state management, and end-to-end testing with Playwright.

---

### Phase 4 Tasks

#### Task 4.1: Next.js 14 project scaffold

```bash
cd frontend
npx create-next-app@latest . --typescript --tailwind --app
```

Structure:
```
frontend/
├── app/
│   ├── layout.tsx           # root layout with auth provider
│   ├── page.tsx             # landing page
│   ├── dashboard/
│   │   ├── page.tsx         # main dashboard (Server Component)
│   │   └── loading.tsx
│   └── repositories/
│       ├── page.tsx
│       └── [id]/
│           └── page.tsx     # repository detail page
├── components/
│   ├── ui/                  # generic reusable components
│   └── repository/          # repository-specific components
├── lib/
│   ├── api.ts               # API client functions
│   └── auth.ts              # NextAuth configuration
├── types/
│   └── index.ts             # TypeScript type definitions
└── __tests__/
```

**Teaching note — Server vs Client Components:**
Next.js 14 App Router distinguishes between Server Components (render on the server, no client-side JavaScript, can access databases directly) and Client Components (`"use client"` directive, run in the browser, can use hooks and event handlers). Default to Server Components. Only add `"use client"` when you need interactivity. This is important to understand before any React interview.

---

#### Task 4.2: Real-time dashboard

Build a repository dashboard that shows live events as they come in via WebSocket:

```typescript
// components/repository/LiveEventFeed.tsx
"use client"

import { useEffect, useState } from "react"

interface Event {
  type: string
  repository: string
  timestamp: string
  message: string
}

export function LiveEventFeed({ repositoryId }: { repositoryId: string }) {
  const [events, setEvents] = useState<Event[]>([])

  useEffect(() => {
    const ws = new WebSocket(
      `${process.env.NEXT_PUBLIC_WS_URL}/ws/repos/${repositoryId}`
    )
    ws.onmessage = (msg) => {
      const event = JSON.parse(msg.data) as Event
      setEvents(prev => [event, ...prev].slice(0, 50))
    }
    return () => ws.close()
  }, [repositoryId])

  return (
    <ul>
      {events.map((event, i) => (
        <li key={i}>{event.timestamp}: {event.message}</li>
      ))}
    </ul>
  )
}
```

---

#### Task 4.3: Frontend tests

```bash
# Unit tests with Jest
npm test

# E2E tests with Playwright
npx playwright test
```

Write:
- Jest unit tests for all utility functions in `lib/`
- Jest component tests for the most complex components
- Playwright E2E tests for the critical user flows:
  - Login and reach the dashboard
  - Add a repository
  - View live events

**Teaching note — Playwright vs Puppeteer:**
Playwright is the modern standard. It supports all browsers, has better async handling, and is actively maintained. Puppeteer is Chrome-only and shows its age in complex async scenarios. Your resume lists both. In interviews, explain that Playwright replaced Puppeteer as the standard for E2E testing.

---

#### Phase 4 Deliverable Checklist

- [ ] Next.js 14 app runs with `npm run dev`
- [ ] Authentication flow works end-to-end with Keycloak
- [ ] Repository list page fetches and displays data from the API
- [ ] Live event feed updates in real-time via WebSocket
- [ ] TypeScript strict mode enabled with no type errors
- [ ] Jest tests pass for all utility functions and components
- [ ] At least 3 Playwright E2E tests cover critical flows

---

## Phase 5: AI Features
### Weeks 7-8 | Ollama, Llama, LangChain, FAISS, RAG, Agent Orchestration, Prompt Engineering

---

### What you are learning

RAG pipeline architecture, vector embeddings, similarity search, LLM prompt engineering, and agentic workflows. These are your strongest differentiators for all three interviews.

---

### Phase 5 Tasks

#### Task 5.1: Ollama setup

Add Ollama to Docker Compose:
```yaml
  ollama:
    image: ollama/ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_models:/root/.ollama
```

Pull the model: `docker exec -it nexus-ollama-1 ollama pull llama3.1:8b`

---

#### Task 5.2: Document ingestion pipeline

This pipeline:
1. Accepts repository documentation files (README, wikis, runbooks)
2. Chunks them into overlapping segments
3. Embeds each chunk using a local embedding model
4. Stores vectors in FAISS

```python
# app/ai/ingestion.py
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.embeddings import OllamaEmbeddings
from langchain_community.vectorstores import FAISS
from langchain_core.documents import Document

class DocumentIngestionPipeline:
    def __init__(self):
        self.embeddings = OllamaEmbeddings(
            model="llama3.1:8b",
            base_url="http://ollama:11434"
        )
        self.splitter = RecursiveCharacterTextSplitter(
            chunk_size=500,
            chunk_overlap=50,
        )

    def ingest(self, content: str, metadata: dict) -> FAISS:
        """Chunk, embed, and index a document. Returns updated vector store."""
        docs = self.splitter.create_documents([content], metadatas=[metadata])
        return FAISS.from_documents(docs, self.embeddings)
```

**Teaching note — chunk size tradeoffs:**
Smaller chunks (200-500 tokens) give more precise retrieval but lose context. Larger chunks (1000+ tokens) preserve context but dilute relevance. Overlap (50-100 tokens between chunks) prevents information being cut at a boundary. These are prompt engineering decisions you should be able to explain in an interview.

---

#### Task 5.3: RAG query pipeline

```python
# app/ai/assistant.py
from langchain_community.llms import Ollama
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

class RepositoryAssistant:
    def __init__(self, vector_store: FAISS):
        self.retriever = vector_store.as_retriever(search_kwargs={"k": 4})
        self.llm = Ollama(model="llama3.1:8b", base_url="http://ollama:11434")

    async def answer(self, question: str, repo_context: dict) -> str:
        """Answer a question using retrieved documentation context."""
        docs = self.retriever.invoke(question)
        context = "\n\n".join(doc.page_content for doc in docs)

        prompt = ChatPromptTemplate.from_template("""
You are a helpful assistant with expertise in the {repo_name} repository.
Use only the following documentation to answer the question.
If the answer is not in the documentation, say so clearly.

Documentation:
{context}

Question: {question}

Answer:""")

        chain = prompt | self.llm | StrOutputParser()
        return await chain.ainvoke({
            "repo_name": repo_context["name"],
            "context": context,
            "question": question,
        })
```

**Teaching note — why the prompt says "Use only the following documentation":**
Without this constraint, the LLM will blend retrieved context with training data and hallucinate confidently. Grounding instructions are the single most important element of a RAG prompt.

---

#### Task 5.4: AI-powered code review agent

Build an agent that:
1. Receives a diff from a GitHub pull request event
2. Analyzes the diff for common issues (missing error handling, SQL injection risks, missing tests)
3. Produces a structured review comment

```python
# app/ai/code_reviewer.py
from langchain_core.prompts import ChatPromptTemplate
from pydantic import BaseModel

class ReviewFinding(BaseModel):
    severity: str  # "error" | "warning" | "info"
    line: int | None
    message: str
    suggestion: str

class CodeReviewAgent:
    SYSTEM_PROMPT = """You are a senior software engineer conducting a code review.
    Analyze the provided diff for:
    1. Correctness bugs and logic errors
    2. Missing error handling
    3. Security vulnerabilities (injection, unvalidated input, exposed secrets)
    4. Missing tests for new code paths
    5. Performance concerns

    Return findings as a JSON array. Be specific. Point to line numbers.
    Only flag real issues. Do not flag style preferences."""

    async def review(self, diff: str) -> list[ReviewFinding]:
        """Analyze a git diff and return structured review findings."""
        ...
```

---

#### Task 5.5: Prompt engineering practice

For the Apple interview specifically, demonstrate these prompt engineering techniques:
- System prompt vs user prompt separation
- Few-shot examples for consistent output format
- Chain-of-thought prompting for complex reasoning
- Output format specification (JSON schema enforcement)
- Temperature settings for different tasks (0 for structured output, 0.7 for creative)

Document your prompt iterations in a `docs/ai-prompts.md` file. Explain what changed and why. This is your evidence of prompt engineering experience.

---

#### Phase 5 Deliverable Checklist

- [ ] Ollama running locally with Llama 3.1 8B
- [ ] Document ingestion pipeline processes README files and stores in FAISS
- [ ] RAG assistant answers questions about repository documentation
- [ ] Code review agent analyzes diffs and returns structured findings
- [ ] AI endpoints integrated into the FastAPI backend
- [ ] All AI service functions have unit tests with mocked LLM responses
- [ ] `docs/ai-prompts.md` documents prompt engineering decisions

---

## Phase 6: Observability and CI/CD
### Week 9 | Prometheus, Grafana, Alertmanager, GitHub Actions, Docker multi-stage

---

### What you are learning

Production observability: what metrics matter, how to instrument code, and how to build alerts that actually fire when something is wrong.

---

### Phase 6 Tasks

#### Task 6.1: Prometheus instrumentation

Every service must expose a `/metrics` endpoint. Add these metrics to the FastAPI backend:

```python
# app/metrics.py
from prometheus_client import Counter, Histogram, Gauge

REQUEST_COUNT = Counter(
    "nexus_http_requests_total",
    "Total HTTP requests",
    ["method", "endpoint", "status_code"]
)

REQUEST_DURATION = Histogram(
    "nexus_http_request_duration_seconds",
    "HTTP request duration",
    ["method", "endpoint"],
    buckets=[0.01, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0]
)

ACTIVE_WEBSOCKETS = Gauge(
    "nexus_active_websocket_connections",
    "Currently active WebSocket connections"
)

AI_QUERY_DURATION = Histogram(
    "nexus_ai_query_duration_seconds",
    "Time for AI assistant to respond",
    buckets=[1, 2, 5, 10, 30, 60]
)
```

**Teaching note — histogram buckets:**
Bucket boundaries matter. For HTTP request duration, you want fine-grained buckets in the fast range (10ms, 50ms, 100ms) because that is where most requests fall, and you want to detect when they start hitting the 500ms or 1s range. For AI query duration, the buckets are much wider because LLM inference takes seconds, not milliseconds.

---

#### Task 6.2: Grafana dashboards

Create dashboards for:
1. **Service Health:** Request rate, error rate, p50/p95/p99 latency for each service
2. **AI Performance:** Query volume, response time distribution, failure rate
3. **Event Pipeline:** Webhook ingestion rate, MongoDB write latency, Redis pub/sub lag
4. **Business Metrics:** Active users, repositories monitored, events processed per day

---

#### Task 6.3: GitHub Actions CI/CD pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  backend-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install ruff mypy
      - run: ruff check backend/app/
      - run: mypy backend/app/ --ignore-missing-imports

  backend-test:
    needs: backend-lint
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: nexus_test
          POSTGRES_USER: nexus
          POSTGRES_PASSWORD: test
        ports: ["5432:5432"]
        options: --health-cmd pg_isready --health-interval 10s --health-retries 5
      redis:
        image: redis:7
        ports: ["6379:6379"]
    env:
      DATABASE_URL: postgresql://nexus:test@localhost:5432/nexus_test
      REDIS_URL: redis://localhost:6379
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install -r backend/requirements.txt -r backend/requirements-dev.txt
      - run: alembic upgrade head
        working-directory: backend
      - run: pytest backend/tests/ --cov=app --cov-fail-under=80

  frontend-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20" }
      - run: npm ci
        working-directory: frontend
      - run: npm run type-check
        working-directory: frontend
      - run: npm test -- --coverage
        working-directory: frontend

  docker-build:
    needs: [backend-test, frontend-test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker-compose build
```

---

#### Phase 6 Deliverable Checklist

- [ ] Prometheus scraping metrics from all services
- [ ] Grafana dashboards show request rate, error rate, and latency
- [ ] Alertmanager sends an alert when error rate exceeds 1%
- [ ] GitHub Actions runs on every PR and blocks merge on test failure
- [ ] Docker multi-stage builds produce minimal production images
- [ ] Coverage gate: CI fails if coverage drops below 80%

---

## Phase 7: Infrastructure as Code and Cloud Deployment
### Week 10 | Terraform, AWS, Kubernetes

---

### What you are learning

Treat infrastructure the same way you treat code: version-controlled, reviewed, and reproducible.

---

### Phase 7 Tasks

#### Task 7.1: Terraform for AWS

```
infra/
├── main.tf
├── variables.tf
├── outputs.tf
├── modules/
│   ├── vpc/
│   ├── eks/          # Kubernetes cluster
│   ├── rds/          # PostgreSQL on RDS
│   └── elasticache/  # Redis on ElastiCache
```

Resources to create:
- VPC with public and private subnets
- EKS cluster for Kubernetes
- RDS PostgreSQL instance in a private subnet
- ElastiCache Redis cluster
- S3 bucket for FAISS index storage
- IAM roles for service accounts

```bash
terraform init
terraform plan      # always review before applying
terraform apply
```

---

#### Task 7.2: Kubernetes manifests

```
infra/k8s/
├── namespace.yaml
├── backend/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── hpa.yaml           # Horizontal Pod Autoscaler
├── frontend/
│   ├── deployment.yaml
│   └── service.yaml
├── go-ingestor/
│   ├── deployment.yaml
│   └── service.yaml
└── monitoring/
    ├── prometheus.yaml
    └── grafana.yaml
```

**Teaching note — why Kubernetes matters for your interviews:**
Amazon Leo runs on Kubernetes. Snowflake's internal infrastructure runs on Kubernetes. Apple's services run on Kubernetes. You do not need to be a Kubernetes expert, but you need to understand Deployments (manages pod replicas), Services (network access to pods), HorizontalPodAutoscaler (auto-scales pods based on metrics), and ConfigMaps/Secrets (configuration and credentials).

---

#### Phase 7 Deliverable Checklist

- [ ] Terraform creates all AWS resources from scratch with `terraform apply`
- [ ] `terraform destroy` cleanly removes everything
- [ ] Kubernetes manifests deploy all services to EKS
- [ ] HPA configured for the backend and ingestor services
- [ ] All secrets stored in Kubernetes Secrets, not hardcoded in manifests
- [ ] Application accessible via a public URL

---

## Phase 8: Analytics Pipeline
### Week 11 | Apache Spark, BigQuery, Pandas, SQL

---

### What you are learning

Batch analytics processing and data warehousing: how you process large volumes of event data to produce insights.

---

### Phase 8 Tasks

#### Task 8.1: Apache Spark job in Java

Write a Spark job that reads events from MongoDB, aggregates them by repository and day, and writes summary data to BigQuery.

```java
// services/spark/src/main/java/com/nexus/analytics/EventAggregator.java
public class EventAggregator {
    public static void main(String[] args) {
        SparkSession spark = SparkSession.builder()
            .appName("NexusEventAggregator")
            .getOrCreate();

        // Read from MongoDB
        Dataset<Row> events = spark.read()
            .format("mongodb")
            .option("uri", System.getenv("MONGODB_URI"))
            .option("collection", "events")
            .load();

        // Aggregate by repository and day
        Dataset<Row> daily = events
            .withColumn("date", to_date(col("received_at")))
            .groupBy("repository_id", "date", "event_type")
            .agg(count("*").alias("event_count"));

        // Write to BigQuery
        daily.write()
            .format("bigquery")
            .option("table", "nexus.daily_event_counts")
            .mode(SaveMode.Append)
            .save();
    }
}
```

**Teaching note — why Spark and Java here:**
Your resume lists both Apache Spark and Java. This is the natural place they appear together. Spark jobs are commonly written in Scala or Java (its native languages), though PySpark exists. Using Java here demonstrates the claim on your resume and gives you a talking point about real Spark usage.

---

#### Task 8.2: BigQuery analytics

Create BigQuery tables and write SQL queries for:
- Deployment frequency per repository (DORA metric)
- Mean time to recovery after failed deployments
- Test coverage trend over time
- Most active repositories by commit volume

These become the data behind the analytics dashboard in the frontend.

---

#### Phase 8 Deliverable Checklist

- [ ] Spark job runs successfully and writes to BigQuery
- [ ] BigQuery tables populated with aggregated data
- [ ] SQL queries for all four analytics metrics written and tested
- [ ] Analytics dashboard in Next.js shows BigQuery data
- [ ] Spark job runs on a schedule via GitHub Actions (weekly batch)

---

## Phase 9: Hardening and Interview Polish
### Week 12 | Testing depth, security audit, performance, documentation

---

### Phase 9 Tasks

#### Task 9.1: Achieve 90%+ test coverage

Use the patterns from `Pytest_Complete_Guide.md` sections 12-16:
- Contract tests for all API response shapes
- Property-based tests with Hypothesis for validation logic
- Snapshot tests for AI response formats
- Performance assertions for critical endpoints

#### Task 9.2: Security audit

Work through `Apple_Find_The_Bug.md` and apply each category of bug to your own codebase:
- Are all API inputs validated?
- Are all JWT claims checked (including exp, iss, aud)?
- Are there any SQL injection vectors?
- Are secrets ever logged?
- Are there any exposed admin endpoints?

#### Task 9.3: README and architecture documentation

Write a `README.md` that:
- Explains what Nexus is in two sentences
- Shows the architecture diagram
- Gives exact setup instructions (`git clone` to running in under 10 minutes)
- Explains every non-obvious design decision

This becomes your demo script for interviews.

---

## Interview Talking Points Generated by This Project

When an interviewer asks "walk me through something you built," every phase of this project maps to a strong answer:

**Distributed systems (Snowflake, Amazon):**
"I designed a multi-service architecture where a Go event ingestor handles high-frequency webhook ingestion, writes events to MongoDB, and publishes to Redis. The Python backend subscribes and forwards over WebSockets. The services are decoupled so a spike in GitHub events does not affect the API response time for dashboard users."

**Security (all three companies):**
"I implemented a zero-trust security model using Keycloak as the identity provider. Every service validates OAuth 2.0 JWT tokens on every request. I validated every claim including expiration, issuer, and audience after discovering how easy it is for AI-generated auth code to silently disable expiration checks."

**AI integration (Apple, Snowflake):**
"I built a RAG pipeline that ingests repository documentation, chunks it, embeds it with a local Llama 3.1 model running via Ollama, stores vectors in FAISS, and answers natural language questions about the repository. I also built a code review agent that analyzes pull request diffs and produces structured findings. Both run fully on-premises with no external API cost."

**SDET / quality (Apple):**
"I built the project with test-driven development from the first commit. The test suite reaches 90%+ coverage using Pytest with fixtures, parametrize, and mocked external services. I use GitHub Actions to block merges if coverage drops below 80%, and Playwright for end-to-end tests of critical user flows."

**Data and analytics (Snowflake):**
"I built a batch analytics pipeline with Apache Spark that reads events from MongoDB, aggregates them, and writes to BigQuery. The pipeline computes DORA metrics, deployment frequency, and mean time to recovery. Those metrics feed a real-time analytics dashboard."

---

## CLAUDE.md Template for Nexus Project

Copy this into `nexus/CLAUDE.md` when you create the project:

```markdown
# Nexus — AI-Native Developer Intelligence Platform

## What this is
Internal developer tooling platform for monitoring repository health, ingesting
GitHub events, and surfacing AI-powered insights. Multi-service architecture.

## Architecture
- Backend: Python 3.12, FastAPI, SQLAlchemy 2.0, Alembic, Pydantic v2
- Frontend: Next.js 14 App Router, TypeScript strict mode, TailwindCSS
- Event ingestor: Go 1.22
- Analytics: Apache Spark (Java), BigQuery
- Identity: Keycloak (OAuth 2.0 / OIDC)
- Primary DB: PostgreSQL 15 (via SQLAlchemy)
- Event store: MongoDB 7
- Cache/Pub-Sub: Redis 7
- AI: Ollama + Llama 3.1 8B, LangChain, FAISS
- Infrastructure: Docker, Kubernetes (EKS), Terraform
- Observability: Prometheus, Grafana, Alertmanager
- CI/CD: GitHub Actions

## Project structure
backend/app/
  api/          # FastAPI route handlers (thin — call services)
  models/       # SQLAlchemy ORM models
  schemas/      # Pydantic request/response schemas
  services/     # Business logic (no direct DB in handlers)
  middleware/   # Auth, logging
  ai/           # LangChain pipelines, RAG, agents
  metrics.py    # Prometheus counters/histograms
frontend/app/   # Next.js 14 App Router pages
services/
  go-ingestor/  # Go webhook receiver
  spark/        # Java Spark analytics job

## Conventions
- Python: type hints always, Google-style docstrings, snake_case
- TypeScript: strict mode, no `any`, functional components
- Commit messages: Conventional Commits (feat/fix/chore/test/docs)
- All new code needs tests before merge
- No print() in production code — use structlog logger

## Commands
- Start all services: docker-compose up -d
- Backend dev server: cd backend && uvicorn app.main:app --reload
- Frontend dev server: cd frontend && npm run dev
- Run backend tests: cd backend && pytest tests/ --cov=app
- Run frontend tests: cd frontend && npm test
- Run E2E tests: cd frontend && npx playwright test
- Apply migrations: cd backend && alembic upgrade head
- Terraform plan: cd infra && terraform plan

## Never do
- Hardcode credentials, API keys, or passwords
- Skip input validation on any API endpoint
- Use sync requests in async FastAPI handlers — use httpx
- Commit directly to main — always use a branch and PR
- Merge your own PR without review (except emergencies)
- Use == to compare floats — use math.isclose() or pytest.approx()
```

---

## Cursor Rules Templates

Create these in `.cursor/rules/`:

**general.mdc:**
```markdown
---
description: General standards for Nexus project
globs: ["**/*.py", "**/*.ts", "**/*.tsx", "**/*.go", "**/*.java"]
---
- All Python functions must have type hints
- No `any` type in TypeScript
- No hardcoded credentials or secrets
- No print() in Python — use the logger
- Commit message format: type(scope): description
- Functions do one thing. If you say "and" describing it, split it.
```

**testing.mdc:**
```markdown
---
description: Testing standards
globs: ["**/test_*.py", "**/*.test.ts", "**/*.spec.ts"]
---
- Pytest fixtures for all shared state
- pytest.approx for all float assertions
- pytest.raises with match= for error message verification
- Mock external services (HTTP, AI, Redis) in unit tests
- Integration tests use real services via docker-compose
- Test names: test_[function]_[scenario]_[expected_outcome]
```

**api.mdc:**
```markdown
---
description: FastAPI handler standards
globs: ["**/api/**/*.py"]
---
- Handlers are thin: validate input, call service, return schema
- Never access database directly in a handler
- All endpoints need Pydantic request and response schemas
- All list endpoints support pagination (page, page_size)
- HTTP 422 for validation errors, 401 for auth, 403 for permissions
```
