# SWE Interview Mastery + Cybersecurity Transition Roadmap

> Based on analysis of 648 real job descriptions (Seattle/remote SWE roles).
> Commitment: **4 hrs/day weekdays, 6 hrs/day weekends**
> Timeline: **interview-ready Month 5, cybersecurity-ready Month 9-12**

---

## Skill Priority (from 648 job descriptions)

| Rank | Skill | Jobs Requiring It |
|------|-------|-------------------|
| 1 | Go/Golang | 516 |
| 2 | SQL | 489 |
| 3 | AWS | 484 |
| 4 | Python, Java, JavaScript, Linux | 481-482 |
| 5 | Docker, Kubernetes, CI/CD | 481 |
| 6 | React, Node.js, TypeScript | 481 |
| 7 | PostgreSQL, Redis, MongoDB | 481 |
| 8 | Terraform, Prometheus, Grafana | 481 |
| 9 | AI/LLM | 281 |
| 10 | Security (general) | 196 |

> **Key insight:** Go has the highest single-skill frequency of any language (516/648 jobs). Most candidates prep Python + Java. Go is also the primary language for cloud infrastructure and network security tooling — it serves both your SWE and security goals simultaneously.

---

## Weekly Schedule Template

**Weekdays (4 hours):**
- `6:00–7:00 AM` — LeetCode (2-3 problems, timed — do this before your brain is polluted by other tasks)
- `7:00–8:00 AM` — Conceptual reading (book or docs for current phase)
- `7:00–9:00 PM` — Hands-on building (projects, labs, infrastructure)

**Weekends (6 hours/day):**
- **Saturday:** 3h system design study + 1 mock interview (Pramp) + 2h project work
- **Sunday:** 2h algorithm review (reattempt problems you got wrong this week) + 4h deep project/lab work

**Weekly review (Sunday, 30 min):**
Count problems solved, new concepts learned, project progress. Identify 3 weakest areas. Adjust next week accordingly.

---

## Phase 0: Baseline Triage (Week 1)

**Day 1-2: Self-assessment**
- Attempt 10 LeetCode Easy problems cold. Note which categories you struggle with (arrays, strings, trees, graphs).
- Rate yourself 1-5 on every Tier 1 skill from the table above. Be brutal — "used it" means you could implement it without documentation.

**Day 3-4: Environment setup**
Install everything before Phase 1 starts. You do not want to be debugging tooling when you should be coding.
```
WSL2 + Ubuntu
Docker Desktop
nvm → Node.js LTS
Python 3.12
Go 1.22
Java 21 (Temurin)
VS Code + extensions (Go, Python, ESLint, Prettier, Docker, GitLens)
```
Create a GitHub repo called `interview-prep` with subdirectories per language and concept area. This is your living portfolio of practice.

**Day 5-7: Meta-reading**
- *Cracking the Coding Interview* — Chapter 1 (the interview process, not the problems)
- *System Design Interview* by Alex Xu — Chapter 1
- Blind 75 list on LeetCode — read every problem title, categorize by pattern. Do NOT solve yet. Just build the mental map.

---

## Phase 1: Languages + Algorithms (Weeks 2–6)

Python is fastest for coding interviews. Go has the highest job frequency of any language and bridges directly into cloud/security tooling. Run algorithms as a daily parallel track throughout — do not treat it as a separate phase.

---

### Python (Weeks 2–3)

**Resources:**
- *Fluent Python* by Ramalho — Chapters 1-4, 7, 14, 16
- Real Python (realpython.com) — "Python Data Structures" and "Decorators" tutorials
- *Python Cookbook* by Beazley — skim for patterns, use as reference

**Daily structure (4 hours):**
- Hour 1: Study the concept
- Hour 2: Implement it from scratch without copying
- Hours 3-4: 3-4 LeetCode problems in that concept area

**Focus areas:**
- List/dict/set comprehensions, generators, context managers, decorators
- `collections` module: Counter, defaultdict, deque — appear in 30%+ of optimal interview solutions
- Type hints (Python 3.10+ syntax) — expected in all serious job postings
- `async`/`await` basics — needed for FastAPI and modern Python backends

**Checkpoint:** Solve 15 Easy and 10 Medium LeetCode problems in Python without hints. Target: under 20 min per Easy, under 35 min per Medium.

---

### Go (Weeks 4–5)

**Resources:**
- *The Go Programming Language* by Donovan & Kernighan — Chapters 1-5, 7-8 (the canonical text)
- tour.golang.org — complete it in 2 days
- gobyexample.com — reference every pattern as you need it

**Focus areas:**
- **Goroutines and channels** — spend 2 full days here. This is what separates Go developers from tourists.
- **Interfaces** — Go's type system is fundamentally different from Java/Python. Understand implicit interface satisfaction.
- **Error handling patterns** — Go's explicit error returns are a culture, not just syntax.
- Standard library: `net/http`, `encoding/json`, `sync`, `context`

**Checkpoint:** Build a concurrent web scraper that:
- Fetches 50 URLs in parallel using goroutines
- Respects rate limits with a semaphore
- Writes structured JSON output to a file
If you can build this without significant help, you understand Go.

---

### Algorithms (Weeks 2–6, daily parallel track)

**Platforms:**
- LeetCode (primary)
- NeetCode.io (free video explanations for every major pattern)

**Daily targets:**
- Weeks 2-3: 3 Easy + 1 Medium per day
- Weeks 4-5: 2 Medium + 1 Hard attempt per day
- Week 6: 3 Medium per day + full timed mock sessions on weekends

**Pattern order — do not skip ahead:**
1. Arrays and hashing, sliding window (Two Sum pattern) — Week 2
2. Two pointers and binary search — Week 2
3. Linked lists, stack/queue — Week 3
4. Trees (BFS, DFS, traversals) — Week 3
5. Graphs (adjacency list, BFS/DFS, union-find) — Week 4
6. **Dynamic programming** (1D → 2D, memoization vs tabulation) — Weeks 4-5. This is where most candidates fail.
7. Backtracking — Week 5
8. Heaps and greedy — Week 5
9. Tries, Dijkstra, topological sort — Week 6

**Books:**
- *Algorithm Design Manual* by Skiena — reference when a pattern confuses you. The "war stories" sections give you interview talking points.
- *Elements of Programming Interviews in Python* (EPI) — 2-3 problems per chapter as supplemental practice.

**Checkpoint (End of Week 6):** Solve 10 LeetCode Mediums in a row in under 30 minutes each. If you cannot, spend one more week on algorithms before moving to Phase 2.

---

## Phase 2: Backend Engineering Depth (Weeks 7–10)

**Goal:** Build production-quality REST APIs with auth, databases, and tests. This is what 80% of job descriptions actually test in practice — and it directly bridges to security (understanding how APIs work is prerequisite to understanding how they break).

---

### Week 7: Databases

**SQL (489/648 jobs — non-negotiable):**
- Mode Analytics SQL Tutorial — mode.com/sql-tutorial (free, covers window functions, CTEs, aggregations)
- *Learning SQL* by Beaulieu — Chapters 1-8 for fundamentals, 10-11 for subqueries and joins
- SQLZoo (sqlzoo.net) — complete "SELECT within SELECT" and "Window Functions" sections

**PostgreSQL:**
- PostgreSQL official docs — read the Tutorial section and Chapter 11 (indexes). Understanding B-tree indexes is an interview staple.
- *PostgreSQL: Up and Running* (O'Reilly) — Chapters 1-6
- Run PostgreSQL in Docker locally. Practice: create a database, design a schema with foreign keys, write complex queries, run EXPLAIN ANALYZE.

**Redis:**
- Redis University (university.redis.com) — free courses (use the Python track)
- Key concepts: data structures (strings, hashes, sorted sets, lists), TTL, pub/sub, session store and cache patterns

**MongoDB:**
- MongoDB University (university.mongodb.com) — "MongoDB Basics" + "MongoDB for Python Developers" (both free with certificates)

**Checkpoint:** Write a SQL query that uses window functions, a CTE, and a subquery on a 100k+ row dataset. Use EXPLAIN to analyze it.

---

### Week 8: TypeScript/Node.js API

**Resources:**
- *Node.js Design Patterns* by Casciaro — Chapters 1-5
- TypeScript Handbook (typescriptlang.org/docs/handbook) — complete it
- NestJS official docs — build the sample app

**Build:** A TypeScript/Node.js REST API with:
- JWT authentication (access + refresh token pattern)
- PostgreSQL via Prisma ORM
- Redis for rate limiting and session caching
- Docker Compose to spin up the full stack
- Input validation with Zod
- Structured logging with Pino

This single project hits: Node.js, TypeScript, PostgreSQL, Redis, Docker, JWT — and demonstrates security consciousness.

---

### Week 9: Python/FastAPI API

**Resources:**
- FastAPI official docs (fastapi.tiangolo.com) — read start to finish (genuinely excellent docs)
- *Architecture Patterns with Python* by Percival & Gregory — Chapters 1-6 (free at cosmicpython.com). Best book on Python backend architecture that exists.

**Build:** Rebuild or extend the Week 8 API in FastAPI with:
- Async endpoints using `async`/`await` throughout
- Pydantic models with strict validation
- SQLAlchemy 2.0 with async session management
- Background tasks for async processing
- OpenAPI/Swagger docs auto-generated

---

### Week 10: Testing

**Resources:**
- *Test-Driven Development with Python* by Percival — Chapters 1-7 (free at obeythetestinggoat.com)
- Pytest official docs — fixtures, parametrize, mocking
- *Unit Testing Principles, Practices, and Patterns* by Khorikov — Chapters 1-5

**Targets:**
- 80%+ coverage on both APIs without gaming the metric
- Mock database calls, external HTTP calls, and time-dependent functions
- Integration tests using Docker Compose for test isolation
- GitHub Actions CI pipeline running on both repos

**Checkpoint:** A stranger can clone either repo, run `docker-compose up`, and the tests pass.

---

## Phase 3: Cloud, DevOps, System Design (Weeks 11–15)

---

### Weeks 11–12: Docker, Kubernetes, Terraform

**Docker:**
- *Docker Deep Dive* by Nigel Poulton — read in 2-3 days (best intro text)
- Official docs: multi-stage builds, networking, volumes

**Kubernetes:**
- *Kubernetes in Action* by Luksa — Chapters 1-8 (canonical K8s learning text)
- KillerCoda (killercoda.com) — free browser-based K8s labs, no local cluster required
- Minikube locally for hands-on practice

Must know from memory (can write YAML without looking):
- Pod, Deployment, Service, Ingress, ConfigMap, Secret
- Horizontal Pod Autoscaler
- Rolling updates and rollback strategies
- Persistent Volumes
- **RBAC** — understand service accounts and role bindings. This bridges directly to security.

**Terraform:**
- HashiCorp official tutorials — AWS track at developer.hashicorp.com/terraform/tutorials (free)
- *Terraform: Up and Running* by Brikman — Chapters 1-5

---

### Week 13: AWS

**Approach:** Certifications are not the goal — knowledge is. Use the AWS Developer Associate study material as a proxy for the skills that matter.

**Resources:**
- Stephane Maarek's "Ultimate AWS Certified Developer Associate" (Udemy — buy when on sale for ~$15, it goes on sale constantly). Covers: EC2, S3, Lambda, API Gateway, RDS, ElastiCache, SQS/SNS, IAM, VPC, CloudFormation.
- *Amazon Web Services in Action* by Wittig — Chapters 1-6 for architecture fundamentals
- AWS Free Tier — spin up real resources. Delete them immediately after.

**Non-negotiables for interviews:**
- IAM roles, policies, and the principle of least privilege (bridges to security)
- VPC design: public/private subnets, security groups, NACLs, NAT gateways
- Lambda cold starts, concurrency limits, timeout configuration
- S3 bucket policies vs. ACLs — common security misconfiguration question
- RDS vs. Aurora vs. DynamoDB — when to use each

---

### Week 14: CI/CD + Monitoring

**CI/CD:**
- GitHub Actions official docs — build full pipelines for both API projects: lint → test → Docker build → push to registry → deploy to staging
- Jenkins: install via Docker, build one pipeline. You don't need to master it — you need to have used it.

**Monitoring:**
- *Prometheus: Up and Running* (O'Reilly) — Chapters 1-5
- Grafana tutorials (grafana.com/tutorials) — complete "Grafana Fundamentals"
- Add Prometheus metrics and a Grafana dashboard to one of your API projects. Concrete portfolio differentiator.

---

### Week 15: System Design

**Books (in order of importance):**
1. *System Design Interview* by Alex Xu Vol. 1 — cover to cover. Re-read Chapters 6 (key-value store), 7 (unique ID), 8 (URL shortener), 10 (notification system), 13 (search autocomplete).
2. *Designing Data-Intensive Applications* by Kleppmann — Chapters 1-3, 5-6. The most important technical book in software engineering. Take notes. This is the "why" behind every system design decision.

**Sites:**
- ByteByteGo YouTube (free) — watch video summaries of DDIA concepts
- System Design Primer on GitHub (github.com/donnemartin/system-design-primer) — comprehensive reference

**Practice framework for every problem:**
1. Clarify requirements (functional + non-functional) — 5 min
2. Estimate scale (QPS, storage, bandwidth) — 5 min
3. High-level design (components, data flow) — 10 min
4. Deep dive on 2-3 components — 20 min
5. Identify bottlenecks and mitigations — 5 min

**Practice problems (in order):**
1. URL shortener
2. Rate limiter
3. Notification system
4. Ride-sharing system
5. Distributed cache
6. Video streaming service

**Checkpoint:** Record yourself solving "Design a URL shortener" for 45 minutes. You should be talking 70% of the time, drawing while talking, never silent for more than 15 seconds. If you are uncomfortable watching the recording, the interviewer will be uncomfortable watching you.

---

## Phase 4: Interview Integration (Weeks 16–20)

---

### LeetCode Targets by Week 16
| Difficulty | Target |
|-----------|--------|
| Easy | 80 solved |
| Medium | 100 solved |
| Hard | 15 attempted, 8 solved |

Company tags to grind: Amazon, Microsoft, Google, Meta, Stripe, Cloudflare, Databricks

---

### Mock Interviews

- **Pramp (pramp.com)** — free peer mock interviews. Do 2 per week starting Week 16.
- **Interviewing.io** — paid, worth 1-2 sessions for real feedback from engineers at top companies.
- **LeetCode mock interview mode** — timed, no hints.

**Target pass rate by Week 20:** 3 out of 5 mock interviews.

---

### Behavioral Prep (STAR format)

Every answer maps to: **Situation → Task → Action → Result**

Prepare 8-10 stories covering:
1. Most technically complex project you worked on
2. Time you disagreed with a team decision and how you handled it
3. Time you failed and what you learned
4. Time you had to learn something fast under pressure
5. Time you improved a process or made a system more efficient
6. Most impactful thing you built at your current/past job

Each story: 90-120 seconds, quantified impact ("reduced latency by 40%", "saved 6 hours per week").

**Amazon Leadership Principles** — even if not targeting Amazon. Know all 14. Map your stories to them. Many Seattle-area companies use the same framework.

---

### Resume Rules

- One page. No exceptions at 2 years of experience.
- Every bullet: **action verb + technology + quantified result**

| Bad | Good |
|-----|------|
| "Worked on backend API development." | "Built FastAPI service handling 50k daily requests, reducing p99 latency from 800ms to 120ms through Redis caching and query optimization." |

---

### Portfolio Projects

All three projects go on GitHub with architecture READMEs explaining every design decision.

**Project 1: Secure API Gateway**
Stack: Go, Docker, Kubernetes, JWT, Redis, PostgreSQL, Prometheus, GitHub Actions
- Go API gateway handling auth, rate limiting, request routing to backend microservices
- Full CI/CD pipeline, K8s deployment manifests, Grafana dashboard
- Security note: a recruiter for a security engineering role at Cloudflare sees "built API gateway with rate limiting" and understands your security mindset

**Project 2: Distributed Task Queue**
Stack: Python, FastAPI, Redis, PostgreSQL, Celery, Docker
- Async job processing with retry logic, dead letter queues, job status tracking
- Full test suite with integration tests

**Project 3: Infrastructure as Code Portfolio**
Stack: Terraform, AWS, Docker, Kubernetes
- Terraform modules provisioning a production-like AWS environment
- VPC with proper network segmentation
- Documentation explaining every security decision
- This doubles as security portfolio material

---

## Phase 5: Cybersecurity Transition (Months 5–12, overlapping with Phase 4)

**Start Month 5. Do not wait until the SWE job search is over.**

---

### How Your SWE Skills Bridge to Security

| SWE Skill | Security Domain |
|-----------|----------------|
| Go/Python | Security tooling development, exploit writing |
| API design + JWT | Application security, OAuth attacks, API pentesting |
| Docker/Kubernetes | Container security, cloud-native security |
| AWS IAM + VPC | Cloud security engineering |
| HTTP/TCP knowledge | Network security, traffic analysis |
| SQL + databases | SQL injection, data exfiltration techniques |
| CI/CD + GitHub Actions | DevSecOps, supply chain security |

**Fastest path for a SWE into security:** Security Engineering or DevSecOps — not pentesting or SOC analyst. Those require different foundations. Security Engineering roles want engineers who write code AND think about security. You are building exactly that profile.

---

### Month 5–6: Networking Fundamentals

**Books:**
- *Computer Networks: A Systems Approach* by Peterson & Davie — Chapters 1-3 (free at book.systemsapproach.org)
- *TCP/IP Illustrated, Volume 1* by W. Richard Stevens — Chapters 1-4, 8, 10-11, 17-18 (HTTP). The networking bible. This makes Wireshark make sense.

**Video:**
- Professor Messer's CompTIA Network+ (professormesser.com) — free, 12 hours, watch at 1.5x speed

**Tools to get hands-on with now:**
- **Wireshark** — capture your own traffic while your APIs run. Understand what goes over the wire for JWT auth and TLS handshakes.
- **nmap** — scan your local Docker network. Understand what port scanning reveals.
- **Burp Suite Community Edition** — intercept HTTP traffic from your own APIs. Find your own vulnerabilities.

**Certification:** CompTIA Security+ in Month 6. Professor Messer's free study guide is sufficient to pass.

---

### Month 6–7: Web Application Security

**Platform:** PortSwigger Web Security Academy (portswigger.net/web-security) — entirely free, the best web security learning resource that exists.

Complete ALL labs for:
- SQL injection
- XSS (reflected, stored, DOM)
- CSRF
- Authentication vulnerabilities
- **JWT attacks** (directly leverages your API knowledge)
- **OAuth vulnerabilities** (directly leverages your API knowledge)
- SSRF
- XXE

**Reading:**
- OWASP Top 10 (current version) — know each vulnerability, how to exploit it, and how to fix it in code
- *The Web Application Hacker's Handbook* by Stuttard — skim for techniques, use as reference

---

### Month 7–8: Network + Cloud Attack/Defense

**Platforms:**
- **TryHackMe** (tryhackme.com) — ~$14/month. Complete "Pre-Security" then "Jr Penetration Tester" paths.
- **Hack The Box** — start "Starting Point" machines. Document each in a writeup.

**Book:** *Hacking: The Art of Exploitation* by Erickson — Chapters 1-3. Understanding memory and exploitation at a low level distinguishes security engineers from security analysts.

**Cloud:**
- **CloudGoat** by Rhino Security Labs — deliberately vulnerable AWS environment. Find and exploit the vulnerabilities, then remediate them. This becomes a portfolio project.
- hackingthe.cloud — free resource focused on AWS/GCP/Azure attack techniques

---

### Month 8–10: Specialization

Pick one based on which job postings excite you most.

---

**Option A: Application Security Engineer** (most accessible from SWE background)
- Focus: code review, SAST/DAST tools, security champions programs
- Add to portfolio: integrate Semgrep + Bandit into your CI/CD pipeline, write custom Semgrep rules, document findings
- Target companies: Stripe, Cloudflare, GitHub, any major tech company with a product security team

**Option B: Cloud Security Engineer**
- Focus: AWS/GCP security posture, IAM analysis, threat detection
- Tools: AWS GuardDuty, Security Hub, AWS Config, Checkov (Terraform scanning)
- Add to portfolio: Terraform-based security posture management system
- Cert: AWS Certified Security - Specialty (Month 9-10)
- Target companies: AWS, cloud-native startups, financial services

**Option C: Detection/Security Tooling Engineer**
- Focus: writing detection rules, building security infrastructure
- Tools: ELK stack (SIEM), Sigma rules, YARA rules
- Languages: Python and Go are the primary languages here — you are already positioned
- Target companies: CrowdStrike, Palo Alto, Wiz, large tech company internal security teams

---

### Security Portfolio Projects

**Security Project 1 (Month 7): Vulnerability Scanner**
Build a Python/Go tool that:
- Scans a target URL for common misconfigurations (open redirects, missing security headers, weak TLS)
- Checks exposed API endpoints for authentication bypass
- Generates a structured report
Demonstrates offensive knowledge AND defensive awareness.

**Security Project 2 (Month 8): Secure-by-Default API Template**
Rebuild your Phase 2 API with security as the feature:
- Mutual TLS between services
- OAuth2 + PKCE flow (not just JWT)
- Rate limiting with progressive backoff
- Input sanitization and validation as middleware
- Security headers (HSTS, CSP, X-Frame-Options)
- Audit logging for all authenticated actions
- Dependency scanning in CI

**Security Project 3 (Month 9): CloudGoat Remediation Writeup**
After exploiting CloudGoat:
- Write a detailed writeup of each attack path found
- Implement the Terraform fixes
- Document before/after IAM policies
This writeup is your security interview portfolio.

---

## Milestone Checkpoints

| Milestone | Criteria |
|-----------|---------|
| End Week 6 | 10 LeetCode Mediums in a row, under 30 min each. If not: spend one more week on algorithms. |
| End Week 10 | Both API projects running in Docker Compose with passing test suites and GitHub Actions CI. |
| End Week 15 | 45-min recorded system design session. Comfortable, no long silences, talking 70% of the time. |
| End Week 20 | 3/5 mock interview pass rate (Pramp + Interviewing.io). |
| Month 6 | CompTIA Security+ passed. |
| Month 8 | 50 PortSwigger labs complete. Finding vulnerabilities faster than reading the hints. |
| Month 10 | 3 HackTheBox machines compromised independently, each documented in a writeup. |

---

## Target Job Titles (12-Month Horizon)

In order of accessibility from your background:

1. **Software Engineer** (backend/full-stack) — immediate target
2. **Security Engineer** — builds security systems, writes code, 60-70% engineering work. SWE background is a direct advantage.
3. **DevSecOps Engineer** — integrates security into CI/CD, IaC, cloud. Phase 3 work positions you directly.
4. **Application Security Engineer** — code review, vuln research in products. Requires PortSwigger + OWASP depth.
5. **Cloud Security Engineer** — AWS/GCP security posture. Requires cloud depth plus security fundamentals.
6. **Detection Engineer** — writes detection logic, builds SIEM infrastructure. Strong Python/Go + ELK.

**Avoid targeting:** SOC Analyst (career regression from SWE), Penetration Tester (requires significantly more offensive depth than 6 months provides).

---

## Quick Reference: Free Resources

| Resource | What For | URL |
|----------|---------|-----|
| NeetCode.io | Algorithm video explanations | neetcode.io |
| tour.golang.org | Learn Go | tour.golang.org |
| gobyexample.com | Go patterns reference | gobyexample.com |
| cosmicpython.com | Python architecture patterns | cosmicpython.com |
| obeythetestinggoat.com | TDD with Python | obeythetestinggoat.com |
| book.systemsapproach.org | Computer networking | book.systemsapproach.org |
| killercoda.com | Kubernetes browser labs | killercoda.com |
| portswigger.net/web-security | Web security labs | portswigger.net/web-security |
| tryhackme.com | Guided security labs | tryhackme.com (~$14/mo) |
| hackingthe.cloud | Cloud attack techniques | hackingthe.cloud |
| professormesser.com | Network+ and Security+ | professormesser.com |
| university.redis.com | Redis | university.redis.com |
| university.mongodb.com | MongoDB | university.mongodb.com |
| mode.com/sql-tutorial | SQL | mode.com/sql-tutorial |
| bytebytego.com | System design videos | bytebytego.com |
| github.com/donnemartin/system-design-primer | System design reference | (GitHub) |

---

*Start today. Schedule your first three LeetCode sessions before closing this file. The plan means nothing until the first problem is solved.*
