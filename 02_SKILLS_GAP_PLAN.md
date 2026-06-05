# Skills-Gap Plan — Turn Resume Claims Into Real Experience

> **Read this first.** Your resume lists technologies that aren't yet on the `washgen` server.
> The goal of this file is **not** to help you fake them — interviewers for senior roles probe
> hard, and a hollow answer fails faster than an honest "I'm mid-implementation." The goal is to
> **actually implement each one in your real platform** so that what you say is *true*. You already
> own a perfect sandbox: 28 containers, real data, real users. Most of these are 1–3 days of work.
>
> For each skill: **(A)** what's missing, **(B)** the smallest real implementation that earns you an
> honest "I've used this in production," **(C)** the exact project to retrofit it into, and **(D)**
> how to talk about it — including honest language for the in-progress state.

Priority order is by **interview-probe frequency × ease-of-implementation**. Do them top-down.

---

## TIER 1 — Do these first (high probe rate, natural fit, 1–3 days each)

### 1. CI/CD with GitHub Actions  *(listed; not present)*
**A. Gap:** 17 git repos, but no automated pipelines — you build/deploy by hand.
**B. Real minimum:** add `.github/workflows/ci.yml` to 2–3 repos that (1) installs deps, (2) runs
**Pytest/Jest**, (3) runs a linter, (4) builds the Docker image. Then add a `deploy` job that SSHs
over Tailscale to the server and runs `docker compose up -d --build`. Now you have **CI** (test on
push) **and CD** (deploy on merge to `main`).
**C. Best targets:** `webhook-gw`, `WG_AI_Assistant`, `washgen-portal` (one Python, one Node).
**D. Talk track:** "Every service has a GitHub Actions pipeline — lint + test gate on PRs, and a
deploy job that builds the image and rolls it out to the server over a Tailscale-secured SSH step.
I gate merges on green tests and coverage." *(After implementing, this is 100% true.)*
**Key terms:** workflow, job, step, runner, matrix build, artifact, secrets, `on: push`, gate,
pipeline, blue-green (aspirational), self-hosted runner.
→ Tutorial: `tutorials/github-actions-cicd.md`

### 2. Redis  *(listed; not present)*
**A. Gap:** no caching/queue/session store.
**B. Real minimum (pick 2 of 3):**
- **Cache:** in the inventory API or RAG app, cache expensive results in Redis with a TTL
  (cache-aside pattern). Measure the latency drop.
- **Rate limiting:** put a Redis-backed rate limiter in front of `webhook-gw`.
- **Job queue:** use Redis as a broker (RQ or Celery) so the RAG ingestion / email processing runs
  as async background jobs instead of blocking the request.
**C. Best targets:** `webhook-gw` (rate limit), `WG_AI_Assistant` (cache embeddings/answers),
`Email_Reader` (background queue).
**D. Talk track:** "I use Redis as a cache-aside layer for hot reads and as the broker for
background jobs — webhook processing and email ingestion run off a Redis queue so the HTTP path
stays fast." **Key terms:** cache-aside, TTL, eviction policy (LRU), pub/sub, broker, RQ/Celery,
rate limiting, idempotency keys, `SETEX`, `INCR`.
→ Tutorial: `tutorials/redis.md`

### 3. Terraform (Infrastructure as Code)  *(listed; not present)*
**A. Gap:** infra is declarative *Compose* but no true IaC; Cloudflare DNS is clicked by hand.
**B. Real minimum:** manage your **Cloudflare DNS records + tunnel routes in Terraform** using the
`cloudflare` provider. `terraform plan` / `apply` your `app.`/`auth.`/`webhooks.` records and
SPF/DKIM/DMARC TXT records. State file checked into a private repo (or local backend to start).
This is genuine, production IaC you can demo.
**C. Best target:** a new `infra/` repo describing Cloudflare. (Optionally: a `docker_container`
provider config for a couple services.)
**D. Talk track:** "My DNS and tunnel config are managed in Terraform against the Cloudflare
provider — records, email-auth TXT entries, and tunnel routes are version-controlled and applied via
`plan`/`apply`, so infra changes are reviewable and reproducible." **Key terms:** IaC, provider,
resource, state, plan/apply, drift, module, `terraform.tfstate`, idempotent, declarative.
→ Tutorial: `tutorials/terraform.md`

### 4. AWS (specifically: S3 off-site backup)  *(listed; only GCP is real)*
**A. Gap:** resume claims AWS; you use GCP. AND your own docs flag the missing off-site backup.
**Two birds:** solve the 3-2-1 gap *with* AWS.
**B. Real minimum:** create an S3 bucket with versioning + lifecycle rules; extend your nightly
`pg_dump` cron to push encrypted dumps to S3 via `boto3` (already installed) or the AWS CLI. Add IAM
user with least-privilege policy. Now you have **real, defensible AWS (S3 + IAM)** experience *and*
a true 3-2-1 backup story.
**C. Best target:** `applications/backups/postgres-backup.sh`.
**D. Talk track:** "Backups follow 3-2-1 — local RAID, plus encrypted nightly dumps to versioned S3
with lifecycle expiry, written by a least-privilege IAM user." **Key terms:** S3, bucket, object,
versioning, lifecycle policy, IAM (user/role/policy), least privilege, KMS/SSE encryption, boto3,
`aws s3 cp`, regions.
→ Tutorial: `tutorials/aws.md`

---

## TIER 2 — Strong differentiators (3–7 days each)

### 5. Kubernetes  *(listed; you run Compose, not K8s — the #1 probed gap)*
**A. Gap:** Compose ≠ Kubernetes. This is the most likely "tell me more" trap on your resume.
**B. Real minimum:** install **k3s** (lightweight K8s, perfect for one node) on the server or a VM.
Migrate **2–3 services** (Portal + one FastAPI app + Postgres) from Compose to real K8s manifests:
`Deployment`, `Service`, `ConfigMap`, `Secret`, `Ingress`, `PersistentVolumeClaim`, plus a
`livenessProbe`/`readinessProbe` and resource `requests/limits`. Optionally wrap them in a **Helm**
chart. Run it for real behind the tunnel.
**C. Best target:** new `k8s/` repo; start with the stateless Portal, then a stateful Postgres
(StatefulSet + PVC) to show you understand stateful workloads.
**D. Honest talk track if NOT yet done:** "In production I orchestrate with Docker Compose because
it's right for a single-node platform. I'm migrating the stateless tier to k3s — Deployments,
Services, Ingress, probes, and PVCs for state — to get rolling updates and self-healing." After you
do it: drop "I'm migrating" → "I run." **Key terms:** pod, Deployment, ReplicaSet, Service
(ClusterIP/NodePort/LoadBalancer), Ingress, ConfigMap, Secret, PV/PVC, StatefulSet, DaemonSet,
namespace, liveness/readiness probe, requests/limits, HPA (autoscaler), kubelet, control plane,
`kubectl`, Helm chart, rolling update, self-healing, declarative reconciliation.
→ Tutorial: `tutorials/kubernetes.md`

### 6. Go (Golang)  *(listed; zero Go on the box)*
**A. Gap:** no Go anywhere.
**B. Real minimum:** **rewrite one small service in Go.** The `webhook-gw` is the ideal candidate —
it's a thin reverse-proxy/router, exactly what Go excels at (`net/http`, goroutines, channels). Or
write a Go CLI that does the S3 backup upload. Ship it as a container in your real platform.
**C. Best target:** `webhook-gw-go` (parallel implementation) or a Go-based health-check sidecar.
**D. Talk track:** "I rewrote our webhook gateway in Go — `net/http` with a goroutine per request,
config-driven routing, HMAC verification — to get a single static binary and lower memory than the
Python version." **Key terms:** goroutine, channel, `go` keyword, `net/http`, struct, interface,
`defer`, error-as-value, slice/map, `go test`, static binary, `context.Context`, mutex.
→ Tutorial: `tutorials/go.md`

---

## TIER 3 — Cover conceptually; implement only if a JD demands it

These are real on the resume but lower-probability and/or weaker fit. Do a *tutorial* for each so you
can speak conceptually and honestly; implement only if a specific job targets them.

### 7. Java + Spring  / 8. C# / 9. Scala
**Honest minimum to claim "familiar":** build one small REST service in each from the tutorial,
with tests. Don't claim "expert" — claim "I've built with it; my production work is Python/TS."
- **Java/Spring:** a Spring Boot REST controller + JPA repository + JUnit tests. Natural framing:
  "Spring Boot is the JVM analog to what I do with FastAPI." → `tutorials/jvm-java-scala.md`
- **C#/.NET:** an ASP.NET Core minimal API + xUnit tests. You already have `.dotnet/` on the box.
  → `tutorials/csharp-dotnet.md`
- **Scala:** functional basics + a small Akka/HTTP or pure-FP service + ScalaTest. Tie to Spark.
  → `tutorials/jvm-java-scala.md`

### 10. Apache Spark + 11. MongoDB
- **Spark:** run PySpark locally over your BigQuery/CRM export data — one real `DataFrame`
  aggregation job. Framing: "I've used PySpark for batch aggregation over our lead data."
  → `tutorials/spark-mongodb.md`
- **MongoDB:** stand up a Mongo container; store the webhook event log (document-shaped data) in it
  instead of/alongside Postgres. Real, small, defensible. → `tutorials/spark-mongodb.md`

### 12. PyTorch / TensorFlow / JAX / Scikit-learn (the "AI" list)
**A. Gap:** you do *inference + RAG*, not model *training*. Don't claim you train deep nets.
**B. Real minimum to be honest-strong:** do one **scikit-learn** project on real data you have —
e.g., predict lead conversion from CRM features (train/test split, a `RandomForestClassifier`,
evaluate with precision/recall/AUC). That's genuine, defensible ML. For PyTorch/TF/JAX: do the
tutorial, then say "I've worked through PyTorch; my production AI work is LLM inference and RAG, not
training from scratch." **Honesty here is a strength — most "AI engineers" can't explain RAG as well
as you can, and overclaiming training experience is easy to expose.**
→ Tutorial: `tutorials/ml-sklearn-pytorch.md`

---

## The meta-rule: tier your language *in the interview*

Never claim uniform mastery. Use three honest tiers and interviewers will trust you more:
- **"I operate this in production":** Python, FastAPI, Next.js/React, PostgreSQL, Docker, Keycloak/
  OIDC, Cloudflare, Tailscale, Prometheus/Grafana, LangChain/Ollama/FAISS, Git.
- **"I've built with this":** (after you do Tier 1–2) GitHub Actions, Redis, Terraform, AWS S3/IAM,
  Kubernetes/k3s, Go.
- **"I'm familiar / I've studied it":** Java/Spring, C#, Scala, Spark, MongoDB, PyTorch/TF/JAX.

> If an interviewer asks about a Tier-3 item: "I've built small projects with it and I understand
> the model; it hasn't been my day-to-day. Here's how I'd apply it here…" — then pivot to the
> concept. That answer *wins*. Bluffing loses.

---

## Suggested 3-week schedule (if you have interviews soon)

| Week | Do (real implementation) | Result you can now claim truthfully |
|---|---|---|
| 1 | GitHub Actions on 2 repos · Redis cache+queue · S3 backup | CI/CD, Redis, AWS S3/IAM |
| 2 | Terraform for Cloudflare DNS · k3s migrate 2 services | IaC/Terraform, Kubernetes |
| 3 | Go rewrite of webhook-gw · scikit-learn lead model · tutorials for JVM/C#/Spark/Mongo | Go, real ML, conceptual fluency |

Even completing **Week 1** moves three resume bullets from "aspirational" to "true."
