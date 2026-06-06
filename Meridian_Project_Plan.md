# Meridian: Distributed Data Intelligence Platform
## Gap-Skills Project Plan — Build What You Cannot Yet Prove

---

## What This Project Is

Meridian is a distributed data engineering and ML platform. It ingests data from IoT sensors and external APIs, processes it at scale with Apache Spark, trains machine learning models, serves predictions through multiple language-specific APIs, and deploys across AWS and Azure via Terraform and Kubernetes.

Where Nexus proves your Python/FastAPI/RAG/DevOps depth, Meridian proves everything else on your resume. The two projects together cover the full picture.

**Total duration:** 12 weeks
**Total services:** 11 services across 7 gap languages
**Rule:** Only use a skill that already appears in Nexus when there is no alternative. This project exists to build new proof, not extend existing proof.

---

## Why These Technologies Are in Your Gap

This project exists specifically because another Claude instance audited your actual server and found no source files for these skills. That audit is attached at the top of this file for reference. The goal is not to memorize these technologies — it is to have working code you wrote, running in a real system, that you can discuss from first principles in an interview.

Each phase below starts from zero and teaches the minimum necessary to build something real. The learning is embedded in the doing.

---

## Skills This Project Covers

| Skill | Where in Meridian |
|---|---|
| **Go** | High-performance prediction serving API — handles 10k+ req/s |
| **Scala** | Apache Spark ETL jobs — idiomatic Scala, not PySpark |
| **Swift** | iOS dashboard app — SwiftUI, Combine, async/await |
| **Java** | Spring Boot job orchestrator — manages ML training pipelines |
| **C++** | Custom inference engine — pybind11 Python bindings |
| **C#** | .NET WebAPI + SignalR — Windows-native monitoring client |
| **Ruby** | Sinatra data pipeline controller — ActiveRecord + MySQL job queue |
| **Angular** | Admin dashboard — Material Design, RxJS, strict TypeScript |
| **Spring Boot** | Training job REST API — Spring Security, JPA, Batch |
| **Flask** | ML model server — PyTorch/TF inference endpoints |
| **Pandas** | Feature engineering pipeline — dataframes, transforms, joins |
| **Kubernetes** | Production deployment — Helm charts, HPA, RBAC, rolling updates |
| **Terraform** | Multi-cloud IaC — AWS (primary) + Azure (DR) modules |
| **AWS** | EKS, RDS, S3, SageMaker, ECR, ElastiCache, IAM |
| **Azure** | AKS, Azure Blob, Azure ML, Container Registry |
| **MongoDB** | Sensor event store — document model, aggregation pipeline |
| **Redis** | Training job queue + prediction cache |
| **MySQL** | Ruby service relational DB — job state, pipeline metadata |
| **Apache Spark** | Batch ETL, feature engineering, model evaluation at scale |
| **PyTorch** | Deep learning model — sensor anomaly detection |
| **TensorFlow** | Time-series forecasting model |
| **JAX** | Fast gradient experiments — JIT, vmap, grad |
| **Scikit-learn** | Baseline models, preprocessing pipelines, model comparison |
| **NumPy** | Direct numerical operations — custom feature transforms |
| **SDN/IoT** | IoT device simulation + network segmentation monitoring |
| **OpenFlow** | Ryu OpenFlow controller — programmatic network policy |

---

## Architecture

```
  IoT Devices (simulated)
         │ MQTT/HTTP
         ▼
┌─────────────────────┐
│  Go Prediction API  │  ← gap: Go, high-perf serving
│  (Port 8080)        │
└──────────┬──────────┘
           │
   ┌───────┼──────────────┐
   │       │              │
   ▼       ▼              ▼
┌──────┐ ┌──────┐  ┌──────────────────┐
│Redis │ │Mongo │  │  Spring Boot     │  ← gap: Java, Spring
│Queue │ │Events│  │  Job Orchestrator│
└──────┘ └──────┘  └────────┬─────────┘
                             │
                    ┌────────▼────────┐
                    │  Flask ML Server│  ← gap: Flask, PyTorch, TF
                    │  (Port 5001)    │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  C++ Inference  │  ← gap: C++, pybind11
                    │  Engine (lib)   │
                    └─────────────────┘

  Batch path:
  MongoDB → Apache Spark (Scala) → Pandas → BigQuery
                ↑
        gap: Spark, Scala, Pandas, NumPy

  Frontend:
  Angular Admin ─── Express BFF ─── Spring Boot API
  Swift iOS App ────────────────────────────────────
  ↑ gap: Angular, Swift

  Notification:
  Redis pub/sub → C# .NET SignalR Hub → Angular/iOS
  ↑ gap: C#

  Pipeline control:
  Ruby Sinatra → MySQL job queue → Spring Boot trigger
  ↑ gap: Ruby, MySQL

  Network layer:
  Ryu OpenFlow Controller → OVS → IoT segment
  ↑ gap: SDN, IoT, OpenFlow

  Infrastructure:
  Terraform → AWS EKS (primary) + Azure AKS (DR)
  Kubernetes (Helm charts, HPA, RBAC)
  ↑ gap: Terraform, AWS, Azure, Kubernetes
```

---

## Before You Start

Reference the SDLC/SCRUM/Git workflow from `Nexus_Project_Plan.md` — it applies here identically. Do not duplicate those docs. The practices are the same; only the technology is different.

```bash
mkdir meridian && cd meridian
git init && git checkout -b main

# Project structure
mkdir -p services/{go-prediction,spring-orchestrator,flask-ml,cpp-inference,dotnet-notif,ruby-pipeline,spark-etl}
mkdir -p frontend/{angular-admin,ios-app}
mkdir -p infra/{aws,azure,kubernetes/helm}
mkdir -p ml/{pytorch,tensorflow,jax,sklearn}
mkdir -p scripts
mkdir -p docs/adr
mkdir -p .github/workflows

# CLAUDE.md — write it before the first commit
cat > CLAUDE.md << 'EOF'
# Meridian — Distributed Data Intelligence Platform
## Gap-Skills Project

## Purpose
Builds real proof for every skill on my resume that isn't backed by production
experience at Washington Generators. See Meridian_Project_Plan.md for full context.

## Services
- services/go-prediction/: Go high-performance prediction API
- services/spring-orchestrator/: Java Spring Boot training job orchestrator
- services/flask-ml/: Python Flask ML model server
- services/cpp-inference/: C++ inference engine with pybind11
- services/dotnet-notif/: C# .NET notification hub (SignalR)
- services/ruby-pipeline/: Ruby Sinatra pipeline controller (MySQL)
- services/spark-etl/: Scala Apache Spark ETL jobs
- frontend/angular-admin/: Angular admin panel
- frontend/ios-app/: Swift iOS companion app
- infra/: Terraform (AWS + Azure) + Kubernetes Helm charts
- ml/: PyTorch, TensorFlow, JAX, Scikit-learn model training

## Databases
- MongoDB: sensor events and predictions
- Redis: job queue, prediction cache
- MySQL: Ruby service pipeline state

## Run locally
docker-compose up -d

## Key commands
go run services/go-prediction/main.go
cd services/spring-orchestrator && ./mvnw spring-boot:run
python services/flask-ml/app.py
EOF

git add CLAUDE.md .gitignore
git commit -m "chore: meridian project scaffold"
git remote add origin https://github.com/CodeBun203/meridian.git
git push -u origin main
```

---

## Phase 1: Data Foundation — MongoDB, Redis, MySQL
### Week 1 | MongoDB, Redis, MySQL, Docker Compose

---

### Sprint Goal
Three databases running locally, each with a clear domain ownership. Understand the difference between document, cache, and relational models by using all three simultaneously.

**Why learn this in Phase 1:** Every subsequent service needs one of these databases. Getting them right first prevents rework. You also need to understand why you'd choose MongoDB over PostgreSQL for sensor events — that judgment is what interviewers probe.

---

### Task 1.1: Docker Compose Environment

```yaml
# docker-compose.yml
version: "3.9"
services:
  mongodb:
    image: mongo:7
    ports: ["27017:27017"]
    environment:
      MONGO_INITDB_ROOT_USERNAME: meridian
      MONGO_INITDB_ROOT_PASSWORD: meridian_dev
      MONGO_INITDB_DATABASE: meridian
    volumes: [mongo_data:/data/db]
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
    command: redis-server --appendonly yes
    volumes: [redis_data:/data]

  mysql:
    image: mysql:8
    ports: ["3306:3306"]
    environment:
      MYSQL_DATABASE: meridian_pipeline
      MYSQL_USER: ruby_svc
      MYSQL_PASSWORD: ruby_dev
      MYSQL_ROOT_PASSWORD: root_dev
    volumes: [mysql_data:/var/lib/mysql]
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s
      retries: 5

volumes:
  mongo_data:
  redis_data:
  mysql_data:
```

---

### Task 1.2: Understand the Database Decision

Write ADR `docs/adr/001-database-assignments.md` before writing any application code:

```markdown
# ADR 001: Database Assignments by Domain

## MongoDB (sensor events, ML predictions)
Document model is correct here because:
- Sensor payloads vary by device type — no fixed schema
- Query pattern is "get all events for device X in time range" — MongoDB's
  native date range index handles this efficiently
- No cross-collection joins needed — each event is self-contained
- Write volume is high (many sensors, frequent readings) — MongoDB's
  write performance without ACID overhead is acceptable for append-only events

## Redis (job queue, prediction cache)
In-memory key-value is correct here because:
- Training jobs need a queue with atomic pop — Redis BLPOP is purpose-built
- Prediction results for the same input don't change — caching with TTL
  prevents redundant model inference on identical requests
- Sub-millisecond reads for cached predictions vs 50-200ms for model inference

## MySQL (pipeline state, job metadata)
Relational is correct here because:
- Pipeline jobs have relationships: job → steps → results → artifacts
- Need ACID guarantees — a half-written job record is worse than no record
- Ruby's ActiveRecord ORM is the most mature with MySQL
- Supports complex status queries with GROUP BY and window functions
```

This ADR is exactly what a senior engineer at Snowflake or Amazon would write before starting. When an interviewer asks "why MongoDB instead of Postgres?" you have a documented, reasoned answer.

---

### Task 1.3: Seed and Explore Each Database

```python
# scripts/seed_databases.py
# Run this to create sample data in all three databases
from pymongo import MongoClient
import redis
import mysql.connector
from datetime import datetime, timezone
import json
import random

# MongoDB: sensor events
mongo = MongoClient("mongodb://meridian:meridian_dev@localhost:27017/")
db = mongo["meridian"]
events = db["sensor_events"]

sample_events = [
    {
        "device_id": f"sensor_{i:04d}",
        "device_type": random.choice(["temperature", "pressure", "vibration"]),
        "timestamp": datetime.now(timezone.utc),
        "value": random.uniform(0, 100),
        "unit": "celsius",
        "location": {"lat": 47.6, "lng": -122.3},
        "metadata": {"firmware": "1.2.3", "battery": random.randint(20, 100)},
    }
    for i in range(1000)
]
events.insert_many(sample_events)
events.create_index([("device_id", 1), ("timestamp", -1)])
events.create_index([("timestamp", -1)])

# MongoDB aggregation pipeline — learn this, it's asked in interviews
pipeline = [
    {"$match": {"device_type": "temperature"}},
    {"$group": {
        "_id": "$device_id",
        "avg_value": {"$avg": "$value"},
        "max_value": {"$max": "$value"},
        "event_count": {"$sum": 1},
    }},
    {"$sort": {"avg_value": -1}},
    {"$limit": 10},
]
print("Top 10 hottest sensors:", list(events.aggregate(pipeline)))

# Redis: job queue demonstration
r = redis.Redis(host="localhost", port=6379, decode_responses=True)
for i in range(5):
    job = {"job_id": f"train_{i}", "model_type": "pytorch", "dataset_size": 10000}
    r.lpush("training_jobs", json.dumps(job))

# Redis prediction cache
r.setex("predict:sensor_0001:latest", 300, json.dumps({"anomaly_score": 0.12}))

# MySQL: pipeline schema
conn = mysql.connector.connect(
    host="localhost", database="meridian_pipeline",
    user="ruby_svc", password="ruby_dev"
)
cursor = conn.cursor()
cursor.execute("""
    CREATE TABLE IF NOT EXISTS pipeline_jobs (
        id INT AUTO_INCREMENT PRIMARY KEY,
        job_id VARCHAR(64) UNIQUE NOT NULL,
        status ENUM('pending','running','completed','failed') DEFAULT 'pending',
        model_type VARCHAR(50),
        dataset_path VARCHAR(255),
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        error_message TEXT
    )
""")
conn.commit()
```

---

### Phase 1 Deliverable Checklist
- [ ] `docker-compose up -d` starts all three databases healthy
- [ ] ADR 001 written with justified reasoning for each database choice
- [ ] Seed script runs without errors, creates data in all three databases
- [ ] MongoDB aggregation pipeline returns correct top-10 result
- [ ] Redis BLPOP demonstrated (blocking pop from queue)
- [ ] MySQL schema created with correct types and constraints

---

## Phase 2: Go Prediction API
### Week 1-2 | Go

---

### Sprint Goal
A production-grade HTTP API in Go that accepts sensor readings, checks the Redis prediction cache, calls the ML service if cache misses, stores the result in MongoDB, and returns predictions. Handles 10,000+ concurrent requests without blocking.

**Why Go for this service:** Go's goroutines are lighter than OS threads. At 10,000 concurrent requests, a Python service would be blocked by the GIL; a Java service would use 10,000 OS threads consuming GBs of memory. Go handles this with a few dozen OS threads and millions of goroutines. This is the answer to "why Go?" in an interview.

---

### Task 2.1: Learn Go Fundamentals First

Before building the service, spend one day on these specific Go concepts that appear immediately in the code:

```go
// go_fundamentals_practice.go — DO NOT skip this
package main

import (
    "context"
    "fmt"
    "sync"
    "time"
)

// 1. Goroutines and channels — Go's core concurrency model
func goroutineBasics() {
    ch := make(chan string, 5)  // buffered channel

    for i := 0; i < 5; i++ {
        go func(n int) {        // goroutine — runs concurrently
            ch <- fmt.Sprintf("result %d", n)
        }(i)
    }

    for i := 0; i < 5; i++ {
        fmt.Println(<-ch)       // receive from channel
    }
}

// 2. Error handling — Go has no exceptions
func goErrorHandling() (string, error) {
    // Errors are values, not exceptions. Every function that can fail
    // returns (value, error). The caller MUST handle the error.
    result, err := riskyOperation()
    if err != nil {
        return "", fmt.Errorf("operation failed: %w", err)  // wrap with context
    }
    return result, nil
}

// 3. Interfaces — Go's polymorphism
type Predictor interface {
    Predict(ctx context.Context, input []float64) (float64, error)
}

// 4. Context — cancellation and deadlines propagate through the call chain
func withTimeout(ctx context.Context) {
    ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
    defer cancel()  // ALWAYS defer cancel — prevents goroutine leak

    select {
    case <-time.After(10 * time.Second):
        fmt.Println("done")
    case <-ctx.Done():
        fmt.Println("timeout:", ctx.Err())
    }
}

// 5. Structs and methods — Go's version of classes
type SensorReading struct {
    DeviceID  string
    Value     float64
    Timestamp time.Time
}

func (s SensorReading) IsAnomaly(threshold float64) bool {
    return s.Value > threshold
}
```

---

### Task 2.2: Go Project Structure

```
services/go-prediction/
├── main.go
├── go.mod
├── internal/
│   ├── handler/
│   │   └── predict.go       # HTTP handlers
│   ├── cache/
│   │   └── redis.go         # Redis cache wrapper
│   ├── store/
│   │   └── mongodb.go       # MongoDB persistence
│   ├── client/
│   │   └── ml_client.go     # HTTP client to Flask ML server
│   └── model/
│       └── types.go         # Shared types
├── Dockerfile
└── Makefile
```

```go
// services/go-prediction/main.go
package main

import (
    "context"
    "log"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/codebun203/meridian/services/go-prediction/internal/handler"
    "github.com/go-chi/chi/v5"
    "github.com/go-chi/chi/v5/middleware"
)

func main() {
    r := chi.NewRouter()
    r.Use(middleware.Logger)
    r.Use(middleware.Recoverer)
    r.Use(middleware.Timeout(30 * time.Second))

    predictHandler := handler.NewPredictHandler()
    r.Post("/predict", predictHandler.Predict)
    r.Get("/health", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusOK)
    })

    server := &http.Server{
        Addr:         ":" + getEnv("PORT", "8080"),
        Handler:      r,
        ReadTimeout:  5 * time.Second,
        WriteTimeout: 10 * time.Second,
        IdleTimeout:  120 * time.Second,
    }

    // Graceful shutdown — accept in-flight requests before stopping
    go func() {
        if err := server.ListenAndServe(); err != http.ErrServerClosed {
            log.Fatalf("server error: %v", err)
        }
    }()
    log.Printf("Prediction API listening on %s", server.Addr)

    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()
    server.Shutdown(ctx)
}

func getEnv(key, fallback string) string {
    if v := os.Getenv(key); v != "" {
        return v
    }
    return fallback
}
```

```go
// services/go-prediction/internal/handler/predict.go
package handler

import (
    "context"
    "encoding/json"
    "net/http"
    "time"

    "github.com/codebun203/meridian/services/go-prediction/internal/cache"
    "github.com/codebun203/meridian/services/go-prediction/internal/client"
    "github.com/codebun203/meridian/services/go-prediction/internal/store"
)

type PredictRequest struct {
    DeviceID string    `json:"device_id"`
    Features []float64 `json:"features"`
}

type PredictResponse struct {
    DeviceID     string    `json:"device_id"`
    AnomalyScore float64   `json:"anomaly_score"`
    IsAnomaly    bool      `json:"is_anomaly"`
    CacheHit     bool      `json:"cache_hit"`
    Timestamp    time.Time `json:"timestamp"`
}

type PredictHandler struct {
    cache     *cache.RedisCache
    mlClient  *client.MLClient
    store     *store.MongoStore
}

func NewPredictHandler() *PredictHandler {
    return &PredictHandler{
        cache:    cache.NewRedisCache(),
        mlClient: client.NewMLClient(),
        store:    store.NewMongoStore(),
    }
}

func (h *PredictHandler) Predict(w http.ResponseWriter, r *http.Request) {
    var req PredictRequest
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        http.Error(w, "invalid request body", http.StatusBadRequest)
        return
    }

    ctx := r.Context()

    // Check cache first
    if cached, err := h.cache.Get(ctx, req.DeviceID); err == nil {
        cached.CacheHit = true
        json.NewEncoder(w).Encode(cached)
        return
    }

    // Call Flask ML server
    score, err := h.mlClient.PredictAnomaly(ctx, req.Features)
    if err != nil {
        http.Error(w, "prediction service unavailable", http.StatusServiceUnavailable)
        return
    }

    result := PredictResponse{
        DeviceID:     req.DeviceID,
        AnomalyScore: score,
        IsAnomaly:    score > 0.7,
        Timestamp:    time.Now().UTC(),
    }

    // Cache and persist concurrently — don't block the response
    go h.cache.Set(ctx, req.DeviceID, result, 5*time.Minute)
    go h.store.SavePrediction(ctx, result)

    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(result)
}
```

---

### Task 2.3: Go Tests

```go
// services/go-prediction/internal/handler/predict_test.go
package handler_test

import (
    "bytes"
    "encoding/json"
    "net/http"
    "net/http/httptest"
    "testing"
)

func TestPredictReturns200OnValidInput(t *testing.T) {
    handler := NewPredictHandler()  // uses mocked dependencies in test mode

    body, _ := json.Marshal(PredictRequest{
        DeviceID: "sensor_001",
        Features: []float64{23.5, 0.8, 100.2},
    })

    req := httptest.NewRequest(http.MethodPost, "/predict", bytes.NewReader(body))
    req.Header.Set("Content-Type", "application/json")
    w := httptest.NewRecorder()

    handler.Predict(w, req)

    if w.Code != http.StatusOK {
        t.Errorf("expected 200, got %d", w.Code)
    }

    var resp PredictResponse
    json.NewDecoder(w.Body).Decode(&resp)
    if resp.DeviceID != "sensor_001" {
        t.Errorf("expected device_id sensor_001, got %s", resp.DeviceID)
    }
}
```

**Go testing pattern:** Go's built-in `testing` package is the standard. No external framework. Table-driven tests are idiomatic:

```go
func TestIsAnomaly(t *testing.T) {
    tests := []struct {
        name      string
        score     float64
        threshold float64
        want      bool
    }{
        {"below threshold", 0.5, 0.7, false},
        {"above threshold", 0.8, 0.7, true},
        {"exactly at threshold", 0.7, 0.7, false},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := isAnomaly(tt.score, tt.threshold)
            if got != tt.want {
                t.Errorf("isAnomaly(%v, %v) = %v, want %v", tt.score, tt.threshold, got, tt.want)
            }
        })
    }
}
```

---

### Phase 2 Deliverable Checklist
- [ ] Go module initialized (`go mod init`)
- [ ] `go run main.go` starts the server
- [ ] POST `/predict` returns a prediction response
- [ ] Redis cache hit is logged and returned without calling Flask
- [ ] MongoDB stores every prediction with TTL index (auto-expire after 30 days)
- [ ] `go test ./...` passes
- [ ] Graceful shutdown handles in-flight requests on SIGTERM

---

## Phase 3: Java Spring Boot Orchestrator
### Week 2 | Java, Spring Boot, Spring Data JPA, Spring Security, Spring Batch

---

### Sprint Goal
A Spring Boot service that manages the lifecycle of ML training jobs: accepts job submission, queues them in Redis, polls for completion, stores results in MySQL via JPA, and exposes a REST API with JWT security.

**Why Java Spring Boot:** Spring Boot is the dominant framework for enterprise Java APIs. If you join a team that has Java services, Spring Boot is what you will find. It is also AWS's recommended Java framework for Lambda and ECS.

---

### Task 3.1: Spring Boot Fundamentals

The four concepts you need before the code makes sense:

```
1. Dependency Injection: Spring's IoC container manages object lifecycles.
   You declare what you need (@Autowired or constructor injection), Spring
   provides it. You never write `new ServiceClass()` — Spring does that.

2. Annotations over configuration: @RestController, @Service, @Repository,
   @Entity — the annotation tells Spring what role a class plays. Spring
   scans the classpath and wires everything together automatically.

3. Spring Data JPA: you define a Repository interface, Spring generates the
   SQL at runtime. `findByStatusOrderByCreatedAtDesc()` generates
   `SELECT * FROM ... WHERE status = ? ORDER BY created_at DESC` — no SQL needed.

4. Spring Security: every HTTP request goes through a filter chain. You configure
   which endpoints require which roles. JWT validation happens in one filter,
   applied globally.
```

---

### Task 3.2: Spring Boot Project Structure

```bash
# Generate project from Spring Initializr
curl https://start.spring.io/starter.zip \
  -d dependencies=web,data-jpa,security,batch,mysql,redis,actuator \
  -d type=maven-project \
  -d language=java \
  -d bootVersion=3.2.0 \
  -d groupId=com.nexus.meridian \
  -d artifactId=spring-orchestrator \
  -o spring-orchestrator.zip

unzip spring-orchestrator.zip -d services/spring-orchestrator
```

```
services/spring-orchestrator/src/main/java/com/nexus/meridian/
├── SpringOrchestratorApplication.java
├── controller/
│   └── TrainingJobController.java
├── service/
│   ├── TrainingJobService.java
│   └── JobQueueService.java
├── repository/
│   └── TrainingJobRepository.java
├── entity/
│   └── TrainingJob.java
├── dto/
│   ├── CreateJobRequest.java
│   └── JobStatusResponse.java
└── security/
    └── JwtAuthFilter.java
```

```java
// services/spring-orchestrator/src/main/java/com/nexus/meridian/entity/TrainingJob.java
package com.nexus.meridian.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "training_jobs")
public class TrainingJob {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false)
    private String jobId;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private JobStatus status = JobStatus.PENDING;

    @Column(nullable = false)
    private String modelType;

    private String datasetPath;
    private String errorMessage;

    @Column(nullable = false, updatable = false)
    private Instant createdAt = Instant.now();

    private Instant completedAt;

    public enum JobStatus { PENDING, RUNNING, COMPLETED, FAILED }

    // getters and setters — or use Lombok @Data if preferred
}
```

```java
// services/spring-orchestrator/src/main/java/com/nexus/meridian/repository/TrainingJobRepository.java
package com.nexus.meridian.repository;

import com.nexus.meridian.entity.TrainingJob;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;
import java.util.Optional;

// Spring Data JPA generates all SQL from the method name — no @Query needed for simple cases
public interface TrainingJobRepository extends JpaRepository<TrainingJob, Long> {
    Optional<TrainingJob> findByJobId(String jobId);
    List<TrainingJob> findByStatusOrderByCreatedAtDesc(TrainingJob.JobStatus status);
    long countByStatus(TrainingJob.JobStatus status);
}
```

```java
// services/spring-orchestrator/src/main/java/com/nexus/meridian/controller/TrainingJobController.java
package com.nexus.meridian.controller;

import com.nexus.meridian.dto.CreateJobRequest;
import com.nexus.meridian.dto.JobStatusResponse;
import com.nexus.meridian.service.TrainingJobService;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/v1/jobs")
public class TrainingJobController {

    private final TrainingJobService jobService;

    // Constructor injection — preferred over @Autowired field injection
    public TrainingJobController(TrainingJobService jobService) {
        this.jobService = jobService;
    }

    @PostMapping
    @PreAuthorize("hasRole('DATA_ENGINEER')")
    public ResponseEntity<JobStatusResponse> createJob(@RequestBody CreateJobRequest request) {
        return ResponseEntity.accepted().body(jobService.createJob(request));
    }

    @GetMapping("/{jobId}")
    public ResponseEntity<JobStatusResponse> getJob(@PathVariable String jobId) {
        return jobService.findJob(jobId)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    @GetMapping
    public List<JobStatusResponse> listJobs(
            @RequestParam(defaultValue = "PENDING") String status) {
        return jobService.listByStatus(status);
    }
}
```

```java
// services/spring-orchestrator/src/main/java/com/nexus/meridian/service/TrainingJobService.java
package com.nexus.meridian.service;

import com.nexus.meridian.dto.CreateJobRequest;
import com.nexus.meridian.dto.JobStatusResponse;
import com.nexus.meridian.entity.TrainingJob;
import com.nexus.meridian.repository.TrainingJobRepository;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.Optional;
import java.util.UUID;

@Service
public class TrainingJobService {

    private final TrainingJobRepository repository;
    private final StringRedisTemplate redisTemplate;

    public TrainingJobService(TrainingJobRepository repository,
                               StringRedisTemplate redisTemplate) {
        this.repository = repository;
        this.redisTemplate = redisTemplate;
    }

    @Transactional
    public JobStatusResponse createJob(CreateJobRequest request) {
        TrainingJob job = new TrainingJob();
        job.setJobId(UUID.randomUUID().toString());
        job.setModelType(request.modelType());
        job.setDatasetPath(request.datasetPath());
        repository.save(job);

        // Push to Redis queue — Go prediction service and Flask ML server both listen
        redisTemplate.opsForList().leftPush("training_jobs", job.getJobId());

        return JobStatusResponse.from(job);
    }

    public Optional<JobStatusResponse> findJob(String jobId) {
        return repository.findByJobId(jobId).map(JobStatusResponse::from);
    }
}
```

---

### Task 3.3: Spring Boot Tests

```java
// src/test/java/com/nexus/meridian/controller/TrainingJobControllerTest.java
package com.nexus.meridian.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.mockito.Mockito.*;

@WebMvcTest(TrainingJobController.class)
class TrainingJobControllerTest {

    @Autowired MockMvc mockMvc;
    @MockBean TrainingJobService jobService;

    @Test
    void getJob_notFound_returns404() throws Exception {
        when(jobService.findJob("nonexistent")).thenReturn(Optional.empty());

        mockMvc.perform(get("/api/v1/jobs/nonexistent"))
            .andExpect(status().isNotFound());
    }
}
```

---

### Phase 3 Deliverable Checklist
- [ ] `./mvnw spring-boot:run` starts the service
- [ ] POST `/api/v1/jobs` creates a job, saves to MySQL, pushes to Redis
- [ ] GET `/api/v1/jobs/{jobId}` returns current status from MySQL
- [ ] JWT filter rejects requests without a valid token
- [ ] `@PreAuthorize("hasRole('DATA_ENGINEER')")` enforced (test with wrong role → 403)
- [ ] `./mvnw test` passes

---

## Phase 4: Python ML Pipeline — Pandas, NumPy, Scikit-learn, PyTorch, TensorFlow, JAX, Flask
### Weeks 3-4 | All ML skills in one phase

---

### Sprint Goal
A complete ML pipeline: raw sensor data → Pandas feature engineering → three trained models (Scikit-learn baseline, PyTorch deep learning, TensorFlow forecasting) → Flask serving API. All model comparisons documented.

**Read before starting:** This is the densest phase. Do the libraries in order: NumPy → Pandas → Scikit-learn → PyTorch → TensorFlow → JAX. Each builds on the last.

---

### Task 4.1: NumPy — Direct Usage (Not Just Transitive)

The audit found NumPy only as a transitive dependency. Fix that by using it directly and meaningfully:

```python
# ml/numpy_fundamentals.py
import numpy as np

# What NumPy gives you: vectorized operations on arrays
# Without NumPy: Python for loops (slow, serial)
# With NumPy: C-optimized operations across entire arrays at once

# Broadcasting — operations between arrays of different shapes
temperatures = np.array([23.1, 24.5, 22.8, 25.1, 23.9])
baseline = np.mean(temperatures)
deviations = temperatures - baseline  # subtracted from every element simultaneously

# Why this matters for ML: neural network weights are numpy arrays.
# Forward pass = matrix multiplication. Without NumPy, this is O(n^3) nested loops.
W1 = np.random.randn(64, 10)    # weight matrix layer 1
X = np.random.randn(100, 10)    # 100 samples, 10 features
Z = X @ W1                      # (100,10) @ (10,64) = (100,64) — one line, fast

# Normalization — common preprocessing step
def normalize(X: np.ndarray) -> np.ndarray:
    """Z-score normalize features column-wise."""
    return (X - X.mean(axis=0)) / (X.std(axis=0) + 1e-8)  # +eps avoids /0

# Rolling statistics — useful for time series sensor data
def rolling_mean(values: np.ndarray, window: int) -> np.ndarray:
    """Compute rolling mean without Pandas — demonstrates NumPy striding."""
    kernel = np.ones(window) / window
    return np.convolve(values, kernel, mode="valid")

# Custom feature engineering that Pandas doesn't handle cleanly
def compute_spectral_energy(signal: np.ndarray) -> float:
    """Compute energy of frequency components via FFT — anomaly detection feature."""
    fft = np.fft.fft(signal)
    power = np.abs(fft) ** 2
    return float(np.sum(power[:len(power)//2]))  # positive frequencies only
```

---

### Task 4.2: Pandas Feature Engineering

```python
# ml/feature_pipeline.py
import pandas as pd
import numpy as np
from pathlib import Path

def load_sensor_data(path: str) -> pd.DataFrame:
    """Load raw sensor events from MongoDB export."""
    df = pd.read_json(path, lines=True)
    df["timestamp"] = pd.to_datetime(df["timestamp"])
    return df

def engineer_features(df: pd.DataFrame) -> pd.DataFrame:
    """Transform raw sensor readings into ML-ready feature matrix.

    Why Pandas for this: the operations needed (group-by, rolling windows,
    time-based resampling, merges) are exactly what Pandas optimizes for.
    NumPy would require writing these algorithms from scratch.
    """
    df = df.sort_values(["device_id", "timestamp"]).copy()

    # Time features — captures circadian patterns in sensor readings
    df["hour"] = df["timestamp"].dt.hour
    df["day_of_week"] = df["timestamp"].dt.dayofweek
    df["is_business_hours"] = df["hour"].between(9, 17).astype(int)

    # Rolling statistics per device
    df["rolling_mean_1h"] = (
        df.groupby("device_id")["value"]
        .transform(lambda x: x.rolling("1H", on=df.loc[x.index, "timestamp"]).mean())
    )
    df["rolling_std_1h"] = (
        df.groupby("device_id")["value"]
        .transform(lambda x: x.rolling("1H", on=df.loc[x.index, "timestamp"]).std())
    )

    # Deviation from device's own baseline
    device_baselines = df.groupby("device_id")["value"].transform("mean")
    df["value_z_score"] = (df["value"] - device_baselines) / (
        df.groupby("device_id")["value"].transform("std") + 1e-8
    )

    # Rate of change
    df["value_delta"] = df.groupby("device_id")["value"].diff()
    df["value_delta_pct"] = df.groupby("device_id")["value"].pct_change()

    return df.dropna()

def prepare_training_data(df: pd.DataFrame) -> tuple[np.ndarray, np.ndarray]:
    """Split engineered features into X (features) and y (labels)."""
    feature_cols = [
        "value", "hour", "day_of_week", "is_business_hours",
        "rolling_mean_1h", "rolling_std_1h", "value_z_score",
        "value_delta", "value_delta_pct",
    ]
    X = df[feature_cols].values
    y = (df["value_z_score"].abs() > 2.5).astype(int).values  # label: anomaly if >2.5σ
    return X, y
```

---

### Task 4.3: Scikit-learn Baseline Model

Always build a Scikit-learn baseline before touching a neural network. If the baseline performs comparably, you don't need the complexity.

```python
# ml/sklearn/baseline_model.py
import numpy as np
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.model_selection import cross_val_score, train_test_split
from sklearn.metrics import classification_report, roc_auc_score
import joblib

def train_baseline(X: np.ndarray, y: np.ndarray) -> Pipeline:
    """Train a Random Forest baseline for anomaly detection.

    This is step 1 of any ML project at a serious company. Neural networks
    are not the default answer — they are the answer when simpler models fail.
    Interviewers will ask why you chose a neural network. 'The baseline
    didn't perform well enough' is the correct answer.
    """
    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=42, stratify=y
    )

    # Pipeline ensures the scaler is fitted only on training data
    # Fitting on the full dataset would be data leakage
    model = Pipeline([
        ("scaler", StandardScaler()),
        ("classifier", RandomForestClassifier(
            n_estimators=100,
            max_depth=10,
            class_weight="balanced",  # handle imbalanced anomaly labels
            random_state=42,
            n_jobs=-1,
        )),
    ])

    # Cross-validation — more reliable than a single train/test split
    cv_scores = cross_val_score(model, X_train, y_train, cv=5, scoring="roc_auc")
    print(f"CV ROC-AUC: {cv_scores.mean():.3f} ± {cv_scores.std():.3f}")

    model.fit(X_train, y_train)
    y_pred = model.predict(X_test)
    print(classification_report(y_test, y_pred))
    print(f"Test ROC-AUC: {roc_auc_score(y_test, model.predict_proba(X_test)[:, 1]):.3f}")

    joblib.dump(model, "ml/models/baseline_anomaly_detector.pkl")
    return model
```

---

### Task 4.4: PyTorch Anomaly Detector

```python
# ml/pytorch/anomaly_model.py
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, TensorDataset
import numpy as np

class SensorAnomalyDetector(nn.Module):
    """Deep neural network for sensor anomaly detection.

    Architecture choice: LSTM layer captures temporal dependencies in
    sequential sensor readings — the same device often shows a pattern
    before an anomaly, not just a single spike. Random Forest cannot
    model this sequential dependency, which is why the neural network
    improves on the baseline.
    """

    def __init__(self, input_dim: int, hidden_dim: int = 64):
        super().__init__()
        self.lstm = nn.LSTM(input_dim, hidden_dim, batch_first=True, num_layers=2,
                            dropout=0.2)
        self.classifier = nn.Sequential(
            nn.Linear(hidden_dim, 32),
            nn.ReLU(),
            nn.Dropout(0.3),
            nn.Linear(32, 1),
            nn.Sigmoid(),
        )

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        # x shape: (batch, sequence_len, features)
        lstm_out, _ = self.lstm(x)
        # Use the last timestep's hidden state for classification
        last_hidden = lstm_out[:, -1, :]
        return self.classifier(last_hidden)


def train_pytorch_model(X_train: np.ndarray, y_train: np.ndarray,
                         epochs: int = 50) -> SensorAnomalyDetector:
    X_t = torch.FloatTensor(X_train).unsqueeze(1)  # add sequence dimension
    y_t = torch.FloatTensor(y_train).unsqueeze(1)
    dataset = TensorDataset(X_t, y_t)
    loader = DataLoader(dataset, batch_size=64, shuffle=True)

    model = SensorAnomalyDetector(input_dim=X_train.shape[1])
    optimizer = optim.AdamW(model.parameters(), lr=1e-3, weight_decay=1e-4)
    # Weighted BCE handles class imbalance without undersampling
    pos_weight = torch.tensor([(y_train == 0).sum() / (y_train == 1).sum()])
    criterion = nn.BCEWithLogitsLoss(pos_weight=pos_weight)
    scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=epochs)

    for epoch in range(epochs):
        model.train()
        total_loss = 0
        for batch_X, batch_y in loader:
            optimizer.zero_grad()
            output = model(batch_X)
            loss = criterion(output, batch_y)
            loss.backward()
            torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
            optimizer.step()
            total_loss += loss.item()
        scheduler.step()
        if epoch % 10 == 0:
            print(f"Epoch {epoch}: loss={total_loss/len(loader):.4f}")

    torch.save(model.state_dict(), "ml/models/pytorch_anomaly_detector.pt")
    return model
```

---

### Task 4.5: TensorFlow Time-Series Forecaster

```python
# ml/tensorflow/forecasting_model.py
import tensorflow as tf
import numpy as np

def build_forecasting_model(seq_len: int, n_features: int,
                             forecast_horizon: int = 1) -> tf.keras.Model:
    """LSTM-based time series forecaster using TensorFlow/Keras.

    Predicts sensor values T+1 through T+forecast_horizon given
    the past seq_len readings.
    """
    inputs = tf.keras.Input(shape=(seq_len, n_features))

    x = tf.keras.layers.LSTM(64, return_sequences=True)(inputs)
    x = tf.keras.layers.Dropout(0.2)(x)
    x = tf.keras.layers.LSTM(32)(x)
    x = tf.keras.layers.Dense(32, activation="relu")(x)
    outputs = tf.keras.layers.Dense(forecast_horizon)(x)

    model = tf.keras.Model(inputs=inputs, outputs=outputs)
    model.compile(
        optimizer=tf.keras.optimizers.Adam(learning_rate=1e-3),
        loss="huber",    # Huber loss is robust to outliers — important for sensor data
        metrics=["mae"],
    )
    return model


def train_tensorflow_model(sequences: np.ndarray, targets: np.ndarray):
    model = build_forecasting_model(
        seq_len=sequences.shape[1],
        n_features=sequences.shape[2],
    )

    callbacks = [
        tf.keras.callbacks.EarlyStopping(patience=10, restore_best_weights=True),
        tf.keras.callbacks.ModelCheckpoint(
            "ml/models/tf_forecaster.keras", save_best_only=True
        ),
    ]

    history = model.fit(
        sequences, targets,
        epochs=100,
        batch_size=32,
        validation_split=0.2,
        callbacks=callbacks,
    )
    return model, history
```

---

### Task 4.6: JAX Experiments

```python
# ml/jax/experiments.py
import jax
import jax.numpy as jnp
from jax import grad, jit, vmap
import optax  # JAX optimizer library

# JAX is for when you need to implement something custom that
# PyTorch and TensorFlow don't support out of the box.
# Example: custom loss function with a non-standard gradient.

@jit  # JIT compile — runs on CPU/GPU, 10-100x faster than interpreted Python
def sensor_loss(params: dict, X: jnp.ndarray, y: jnp.ndarray) -> jnp.ndarray:
    """Custom loss combining MSE with a penalty for large prediction jumps.

    Standard MSE doesn't penalize temporally discontinuous predictions.
    This custom loss enforces smoothness — appropriate for sensor data
    that physically cannot change instantaneously.
    """
    W, b = params["W"], params["b"]
    predictions = jnp.dot(X, W) + b
    mse = jnp.mean((predictions - y) ** 2)

    # Smoothness penalty: penalize large changes between consecutive predictions
    delta = jnp.diff(predictions, axis=0)
    smoothness_penalty = jnp.mean(delta ** 2) * 0.1

    return mse + smoothness_penalty

# grad() computes the gradient automatically — no manual backprop
grad_fn = jit(grad(sensor_loss))  # JIT + auto-diff

# vmap vectorizes a function over a batch without explicit loops
single_predict = lambda W, b, x: jnp.dot(x, W) + b
batch_predict = vmap(single_predict, in_axes=(None, None, 0))

# Practical JAX training loop
def train_with_jax(X: jnp.ndarray, y: jnp.ndarray, learning_rate: float = 0.01):
    params = {
        "W": jnp.zeros((X.shape[1], 1)),
        "b": jnp.zeros(1),
    }
    optimizer = optax.adam(learning_rate)
    opt_state = optimizer.init(params)

    @jit
    def update(params, opt_state, X_batch, y_batch):
        grads = grad_fn(params, X_batch, y_batch)
        updates, new_state = optimizer.update(grads, opt_state)
        new_params = optax.apply_updates(params, updates)
        return new_params, new_state

    for step in range(1000):
        params, opt_state = update(params, opt_state, X, y)
        if step % 100 == 0:
            loss = sensor_loss(params, X, y)
            print(f"Step {step}: loss={loss:.4f}")

    return params
```

---

### Task 4.7: Flask ML Server

```python
# services/flask-ml/app.py
from flask import Flask, request, jsonify, abort
import torch
import tensorflow as tf
import joblib
import numpy as np
from ml.pytorch.anomaly_model import SensorAnomalyDetector

app = Flask(__name__)

# Load all models once at startup
sklearn_model = joblib.load("ml/models/baseline_anomaly_detector.pkl")

pytorch_model = SensorAnomalyDetector(input_dim=9)
pytorch_model.load_state_dict(torch.load("ml/models/pytorch_anomaly_detector.pt"))
pytorch_model.eval()

tf_model = tf.keras.models.load_model("ml/models/tf_forecaster.keras")

@app.route("/health")
def health():
    return jsonify({"status": "ok", "models_loaded": 3})

@app.route("/predict/anomaly", methods=["POST"])
def predict_anomaly():
    data = request.get_json()
    if not data or "features" not in data:
        abort(400, "features required")

    features = np.array(data["features"], dtype=np.float32)

    # Ensemble: average sklearn + PyTorch predictions
    sklearn_score = sklearn_model.predict_proba(features.reshape(1, -1))[0, 1]

    with torch.no_grad():
        x = torch.FloatTensor(features).unsqueeze(0).unsqueeze(0)
        pytorch_score = pytorch_model(x).item()

    ensemble_score = (sklearn_score + pytorch_score) / 2

    return jsonify({
        "anomaly_score": round(ensemble_score, 4),
        "is_anomaly": ensemble_score > 0.65,
        "sklearn_score": round(float(sklearn_score), 4),
        "pytorch_score": round(pytorch_score, 4),
    })

@app.route("/predict/forecast", methods=["POST"])
def predict_forecast():
    data = request.get_json()
    sequence = np.array(data["sequence"], dtype=np.float32)
    sequence = sequence.reshape(1, sequence.shape[0], -1)
    forecast = tf_model.predict(sequence, verbose=0)
    return jsonify({"forecast": forecast[0].tolist()})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001, debug=False)
```

---

### Phase 4 Deliverable Checklist
- [ ] NumPy used directly: rolling_mean, spectral_energy, normalization
- [ ] Pandas pipeline produces a feature matrix from raw MongoDB export
- [ ] Scikit-learn Random Forest trained, CV ROC-AUC > 0.75, saved with joblib
- [ ] PyTorch LSTM trained, test AUC better than sklearn baseline
- [ ] TensorFlow forecaster trained, MAE < 5% of value range
- [ ] JAX custom loss function with `@jit` and `grad` working
- [ ] Flask server serves all three models on `/predict/anomaly` and `/predict/forecast`
- [ ] Document model comparison: which performed best and why

---

## Phase 5: Apache Spark — Scala ETL
### Week 5 | Scala, Apache Spark

---

### Sprint Goal
A Scala Spark job that reads raw sensor events from MongoDB, engineers features using Spark SQL, trains an anomaly detection model using Spark MLlib, and writes aggregated results to a Parquet store and BigQuery.

**Why Scala for Spark:** Spark is written in Scala. The Scala API has the most complete feature set and the best type safety. PySpark is a wrapper around the JVM — when something breaks in PySpark, the stack trace is in Scala. Knowing Scala means you can read the error.

---

### Task 5.1: Scala Fundamentals for Spark

```scala
// spark/src/main/scala/meridian/ScalaFundamentals.scala
// Things that confuse Python developers about Scala:

// 1. val vs var — prefer val (immutable)
val name = "meridian"     // immutable — like Python's effectively final
var count = 0             // mutable — use sparingly

// 2. Case classes — Scala's data classes (like Python dataclasses)
case class SensorEvent(
  deviceId: String,
  value: Double,
  timestamp: Long,
  deviceType: String,
)
// Free: equals, hashCode, toString, copy, pattern matching

// 3. Option — Scala's None-safety (no NullPointerException)
def findDevice(id: String): Option[SensorEvent] = {
  if (id.startsWith("sensor_")) Some(SensorEvent(id, 0.0, 0L, "temp"))
  else None
}
val result = findDevice("sensor_001")
val value = result.map(_.value).getOrElse(0.0)  // safe — no NPE possible

// 4. Pattern matching
result match {
  case Some(event) => println(s"Found: ${event.deviceId}")
  case None        => println("Not found")
}

// 5. Functional collection operations (very similar to Python's map/filter)
val values = List(1.0, 2.0, 3.0, 4.0, 5.0)
val normalized = values.map(v => (v - values.sum / values.size))
val anomalies = normalized.filter(_.abs > 2.0)
val total = values.foldLeft(0.0)(_ + _)  // equivalent to Python's reduce
```

---

### Task 5.2: Spark ETL Job

```scala
// spark/src/main/scala/meridian/SensorETLJob.scala
package meridian

import org.apache.spark.sql.{SparkSession, DataFrame}
import org.apache.spark.sql.functions._
import org.apache.spark.sql.types._
import org.apache.spark.ml.feature.{StandardScaler, VectorAssembler}
import org.apache.spark.ml.classification.RandomForestClassifier
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.evaluation.BinaryClassificationEvaluator

object SensorETLJob {

  def main(args: Array[String]): Unit = {
    val spark = SparkSession.builder()
      .appName("MeridianSensorETL")
      .config("spark.mongodb.read.connection.uri", sys.env("MONGODB_URI"))
      .getOrCreate()

    import spark.implicits._

    val events = loadSensorEvents(spark)
    val features = engineerFeatures(spark, events)
    val model = trainSparkModel(features)

    // Write aggregated features to Parquet (columnar — efficient for analytics)
    features
      .write
      .mode("overwrite")
      .partitionBy("device_type", "day")    // partition for efficient downstream reads
      .parquet("s3a://meridian-data/features/")

    // Write daily summaries to BigQuery
    val dailySummary = computeDailySummary(spark, events)
    dailySummary
      .write
      .format("bigquery")
      .option("table", s"${sys.env("GCP_PROJECT")}.meridian.daily_sensor_summary")
      .mode("append")
      .save()

    spark.stop()
  }

  def loadSensorEvents(spark: SparkSession): DataFrame = {
    spark.read
      .format("mongodb")
      .option("database", "meridian")
      .option("collection", "sensor_events")
      .load()
      .select("device_id", "device_type", "value", "timestamp")
      .withColumn("timestamp", col("timestamp").cast(TimestampType))
      .filter(col("value").isNotNull)
  }

  def engineerFeatures(spark: SparkSession, df: DataFrame): DataFrame = {
    import org.apache.spark.sql.expressions.Window

    val deviceWindow = Window.partitionBy("device_id").orderBy("timestamp")
    val deviceHourWindow = Window.partitionBy("device_id",
      hour(col("timestamp"))).orderBy("timestamp")

    df
      .withColumn("hour", hour(col("timestamp")))
      .withColumn("day_of_week", dayofweek(col("timestamp")))
      .withColumn("day", to_date(col("timestamp")))
      // Rolling mean over last 10 readings per device
      .withColumn("rolling_mean",
        avg("value").over(deviceWindow.rowsBetween(-9, 0)))
      // Standard deviation over last 10 readings
      .withColumn("rolling_std",
        stddev("value").over(deviceWindow.rowsBetween(-9, 0)))
      // Z-score vs device baseline
      .withColumn("device_mean",
        avg("value").over(Window.partitionBy("device_id")))
      .withColumn("device_std",
        stddev("value").over(Window.partitionBy("device_id")))
      .withColumn("z_score",
        (col("value") - col("device_mean")) / (col("device_std") + 1e-8))
      // Label: anomaly if z-score > 2.5
      .withColumn("is_anomaly", (abs(col("z_score")) > 2.5).cast(IntegerType))
  }

  def trainSparkModel(features: DataFrame): org.apache.spark.ml.PipelineModel = {
    val featureCols = Array("value", "hour", "day_of_week",
                             "rolling_mean", "rolling_std", "z_score")

    val assembler = new VectorAssembler()
      .setInputCols(featureCols)
      .setOutputCol("raw_features")

    val scaler = new StandardScaler()
      .setInputCol("raw_features")
      .setOutputCol("features")

    val rf = new RandomForestClassifier()
      .setLabelCol("is_anomaly")
      .setFeaturesCol("features")
      .setNumTrees(100)
      .setMaxDepth(10)

    val pipeline = new Pipeline().setStages(Array(assembler, scaler, rf))

    val Array(trainDf, testDf) = features.randomSplit(Array(0.8, 0.2), seed=42)
    val model = pipeline.fit(trainDf)

    val predictions = model.transform(testDf)
    val evaluator = new BinaryClassificationEvaluator()
      .setLabelCol("is_anomaly")
      .setMetricName("areaUnderROC")
    println(s"Test AUC: ${evaluator.evaluate(predictions)}")

    model.write.overwrite().save("s3a://meridian-models/spark-anomaly-detector")
    model
  }

  def computeDailySummary(spark: SparkSession, df: DataFrame): DataFrame = {
    df
      .withColumn("day", to_date(col("timestamp")))
      .groupBy("device_id", "device_type", "day")
      .agg(
        avg("value").as("avg_value"),
        min("value").as("min_value"),
        max("value").as("max_value"),
        stddev("value").as("std_value"),
        count("*").as("reading_count"),
      )
  }
}
```

---

### Phase 5 Deliverable Checklist
- [ ] `sbt compile` succeeds with no errors
- [ ] `spark-submit` runs the job against local MongoDB
- [ ] Parquet output written and readable with `spark.read.parquet(...).show()`
- [ ] BigQuery daily summary table queryable via `bq query`
- [ ] Spark MLlib Pipeline trained, AUC logged
- [ ] Spark job submitted via GitHub Actions scheduled workflow

---

## Phase 6: Angular Admin Dashboard
### Week 6 | Angular, TypeScript

---

### Sprint Goal
An Angular admin panel that displays ML model performance metrics, training job status from Spring Boot, and IoT device health. Different enough from the Next.js Nexus dashboard to prove you know both frameworks.

**The key difference from React:** Angular is opinionated. It has a built-in HTTP client, router, forms module, dependency injection container, and module system. React requires you to choose libraries for all of these. Angular means less decision fatigue; it also means more boilerplate. Both are valid at different scales.

---

### Task 6.1: Angular Core Concepts

```
Component: a class + HTML template + CSS. The building block.
Service: a singleton class injected into components. Handles data fetching and business logic.
Module: groups related components and services. Can be lazy-loaded.
Directive: modifies DOM behavior (ngIf, ngFor, ngClass, or custom).
Pipe: transforms display values (date, currency, uppercase, or custom).
RxJS Observable: Angular's async primitive. Like a Promise, but a stream of values.
```

---

### Task 6.2: Angular Project

```bash
ng new angular-admin --routing --style=scss --strict
cd angular-admin
ng add @angular/material  # Material Design component library
ng generate component pages/dashboard
ng generate component pages/models
ng generate component pages/devices
ng generate component pages/jobs
ng generate service services/api
ng generate service services/auth
ng generate guard guards/auth
```

```typescript
// frontend/angular-admin/src/app/services/api.service.ts
import { Injectable } from "@angular/core"
import { HttpClient, HttpHeaders } from "@angular/common/http"
import { Observable, throwError } from "rxjs"
import { catchError, retry } from "rxjs/operators"
import { AuthService } from "./auth.service"

export interface ModelMetrics {
  modelId: string
  modelType: string
  rocAuc: number
  precision: number
  recall: number
  trainingDate: string
}

export interface TrainingJob {
  jobId: string
  status: "PENDING" | "RUNNING" | "COMPLETED" | "FAILED"
  modelType: string
  createdAt: string
}

@Injectable({ providedIn: "root" })
export class ApiService {
  private readonly springUrl = "http://localhost:8081/api/v1"

  constructor(private http: HttpClient, private auth: AuthService) {}

  private get headers(): HttpHeaders {
    return new HttpHeaders({ Authorization: `Bearer ${this.auth.getToken()}` })
  }

  getModelMetrics(): Observable<ModelMetrics[]> {
    return this.http
      .get<ModelMetrics[]>(`${this.springUrl}/models/metrics`, { headers: this.headers })
      .pipe(retry(2), catchError(this.handleError))
  }

  getTrainingJobs(): Observable<TrainingJob[]> {
    return this.http
      .get<TrainingJob[]>(`${this.springUrl}/jobs`, { headers: this.headers })
      .pipe(catchError(this.handleError))
  }

  private handleError(error: any): Observable<never> {
    console.error("API error:", error)
    return throwError(() => new Error(error.message || "Unknown API error"))
  }
}
```

```typescript
// frontend/angular-admin/src/app/pages/dashboard/dashboard.component.ts
import { Component, OnInit, OnDestroy } from "@angular/core"
import { Subject, timer } from "rxjs"
import { takeUntil, switchMap } from "rxjs/operators"
import { ApiService, ModelMetrics, TrainingJob } from "../../services/api.service"

@Component({
  selector: "app-dashboard",
  template: `
    <mat-toolbar color="primary">
      <span>Meridian — Model Dashboard</span>
    </mat-toolbar>

    <div class="dashboard-grid">
      <mat-card *ngFor="let model of models">
        <mat-card-header>
          <mat-card-title>{{ model.modelType }}</mat-card-title>
          <mat-card-subtitle>Trained {{ model.trainingDate | date }}</mat-card-subtitle>
        </mat-card-header>
        <mat-card-content>
          <p>ROC-AUC: <strong>{{ model.rocAuc | number:"1.3-3" }}</strong></p>
          <p>Precision: {{ model.precision | percent }}</p>
          <p>Recall: {{ model.recall | percent }}</p>
        </mat-card-content>
      </mat-card>
    </div>

    <mat-table [dataSource]="jobs" *ngIf="jobs.length">
      <ng-container matColumnDef="jobId">
        <mat-header-cell *matHeaderCellDef>Job ID</mat-header-cell>
        <mat-cell *matCellDef="let job">{{ job.jobId }}</mat-cell>
      </ng-container>
      <ng-container matColumnDef="status">
        <mat-header-cell *matHeaderCellDef>Status</mat-header-cell>
        <mat-cell *matCellDef="let job">
          <mat-chip [color]="job.status === 'COMPLETED' ? 'primary' : 'warn'">
            {{ job.status }}
          </mat-chip>
        </mat-cell>
      </ng-container>
      <mat-header-row *matHeaderRowDef="displayedColumns"></mat-header-row>
      <mat-row *matRowDef="let row; columns: displayedColumns;"></mat-row>
    </mat-table>
  `,
})
export class DashboardComponent implements OnInit, OnDestroy {
  models: ModelMetrics[] = []
  jobs: TrainingJob[] = []
  displayedColumns = ["jobId", "status", "modelType", "createdAt"]
  private destroy$ = new Subject<void>()

  constructor(private api: ApiService) {}

  ngOnInit(): void {
    // Poll every 30 seconds — RxJS timer + switchMap pattern
    timer(0, 30000)
      .pipe(
        switchMap(() => this.api.getModelMetrics()),
        takeUntil(this.destroy$),  // automatic cleanup on component destroy
      )
      .subscribe(models => (this.models = models))

    this.api.getTrainingJobs()
      .pipe(takeUntil(this.destroy$))
      .subscribe(jobs => (this.jobs = jobs))
  }

  ngOnDestroy(): void {
    this.destroy$.next()
    this.destroy$.complete()
  }
}
```

---

### Phase 6 Deliverable Checklist
- [ ] `ng serve` starts Angular app on port 4200
- [ ] Dashboard renders model metrics fetched from Spring Boot
- [ ] RxJS polling updates the job table every 30 seconds without page refresh
- [ ] `AuthGuard` redirects unauthenticated users to `/login`
- [ ] Angular Material components used throughout (mat-card, mat-table, mat-chip)
- [ ] `ng test` passes (at minimum: service tests with HttpClientTestingModule)

---

## Phase 7: C++ Inference Engine
### Week 7 | C++, pybind11, CMake

---

### Sprint Goal
A C++ library that implements a fast inference path for the trained PyTorch model, exposed to Python via pybind11. Benchmark shows measurable speedup over pure Python inference.

---

### Task 7.1: C++ Fundamentals for This Use Case

```cpp
// services/cpp-inference/include/fundamentals.hpp
// The C++ concepts you need for this specific project:

#include <vector>
#include <memory>
#include <stdexcept>

// 1. RAII — resources are owned and freed automatically
// Smart pointers prevent memory leaks (no manual delete needed)
std::unique_ptr<float[]> allocate_matrix(size_t rows, size_t cols) {
    return std::make_unique<float[]>(rows * cols);  // auto-freed when out of scope
}

// 2. References and const — C++ is explicit about mutability
float dot_product(const std::vector<float>& a, const std::vector<float>& b) {
    // const& means: "I read this, I do not own it, I will not modify it"
    if (a.size() != b.size()) throw std::invalid_argument("size mismatch");
    float result = 0.0f;
    for (size_t i = 0; i < a.size(); ++i) result += a[i] * b[i];
    return result;
}

// 3. Templates — generic code without runtime overhead
template<typename T>
T clamp(T value, T min_val, T max_val) {
    if (value < min_val) return min_val;
    if (value > max_val) return max_val;
    return value;
}
```

---

### Task 7.2: C++ Inference Implementation

```cpp
// services/cpp-inference/src/inference_engine.cpp
#include <pybind11/pybind11.h>
#include <pybind11/numpy.h>
#include <pybind11/stl.h>
#include <vector>
#include <cmath>
#include <stdexcept>

namespace py = pybind11;

// Simple linear layer with ReLU — equivalent to nn.Linear + nn.ReLU in PyTorch
// The weights are loaded from the saved PyTorch model
struct LinearLayer {
    std::vector<float> weights;
    std::vector<float> bias;
    size_t input_dim;
    size_t output_dim;

    std::vector<float> forward(const std::vector<float>& input) const {
        if (input.size() != input_dim)
            throw std::invalid_argument("input dimension mismatch");

        std::vector<float> output(output_dim, 0.0f);
        for (size_t j = 0; j < output_dim; ++j) {
            for (size_t i = 0; i < input_dim; ++i) {
                output[j] += weights[j * input_dim + i] * input[i];
            }
            output[j] += bias[j];
            output[j] = std::max(0.0f, output[j]);  // ReLU
        }
        return output;
    }
};

class FastInferenceEngine {
public:
    explicit FastInferenceEngine(const std::string& model_path) {
        load_weights(model_path);
    }

    float predict_anomaly_score(const std::vector<float>& features) {
        auto h1 = layer1.forward(features);
        auto h2 = layer2.forward(h1);

        // Sigmoid activation for output
        float logit = 0.0f;
        for (size_t i = 0; i < h2.size(); ++i) {
            logit += output_weights[i] * h2[i];
        }
        logit += output_bias;
        return 1.0f / (1.0f + std::exp(-logit));  // sigmoid
    }

    // Batch prediction — process multiple inputs at once
    std::vector<float> predict_batch(py::array_t<float> batch) {
        auto buf = batch.request();
        if (buf.ndim != 2) throw std::runtime_error("expected 2D array");

        size_t n_samples = buf.shape[0];
        size_t n_features = buf.shape[1];
        float* ptr = static_cast<float*>(buf.ptr);

        std::vector<float> results(n_samples);
        for (size_t i = 0; i < n_samples; ++i) {
            std::vector<float> sample(ptr + i * n_features,
                                       ptr + (i + 1) * n_features);
            results[i] = predict_anomaly_score(sample);
        }
        return results;
    }

private:
    LinearLayer layer1, layer2;
    std::vector<float> output_weights;
    float output_bias = 0.0f;

    void load_weights(const std::string& path);  // implementation reads .bin file
};

// pybind11 module definition — this is what Python imports
PYBIND11_MODULE(meridian_inference, m) {
    m.doc() = "Fast C++ inference engine for Meridian anomaly detector";

    py::class_<FastInferenceEngine>(m, "FastInferenceEngine")
        .def(py::init<const std::string&>(), py::arg("model_path"))
        .def("predict_batch", &FastInferenceEngine::predict_batch,
             "Predict anomaly scores for a batch of feature vectors",
             py::arg("features"))
        .def("predict_single", [](FastInferenceEngine& self,
                                   const std::vector<float>& features) {
            return self.predict_anomaly_score(features);
        });
}
```

```cmake
# services/cpp-inference/CMakeLists.txt
cmake_minimum_required(VERSION 3.20)
project(meridian_inference)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Enable optimizations — this is why C++ is faster
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -march=native -DNDEBUG")

find_package(pybind11 REQUIRED)

pybind11_add_module(meridian_inference
    src/inference_engine.cpp
)

target_include_directories(meridian_inference PRIVATE include/)
```

```python
# services/cpp-inference/benchmark.py
import time
import numpy as np
import torch
from ml.pytorch.anomaly_model import SensorAnomalyDetector
import meridian_inference  # C++ module

BATCH_SIZE = 1000
N_FEATURES = 9

features = np.random.randn(BATCH_SIZE, N_FEATURES).astype(np.float32)

# Python PyTorch baseline
model = SensorAnomalyDetector(input_dim=N_FEATURES)
model.eval()
start = time.perf_counter()
with torch.no_grad():
    x = torch.FloatTensor(features).unsqueeze(1)
    _ = model(x).numpy()
pytorch_time = time.perf_counter() - start

# C++ inference engine
engine = meridian_inference.FastInferenceEngine("ml/models/weights.bin")
start = time.perf_counter()
_ = engine.predict_batch(features)
cpp_time = time.perf_counter() - start

print(f"PyTorch: {pytorch_time*1000:.1f}ms")
print(f"C++:     {cpp_time*1000:.1f}ms")
print(f"Speedup: {pytorch_time/cpp_time:.1f}x")
```

---

### Phase 7 Deliverable Checklist
- [ ] CMake build succeeds: `cmake -B build -DCMAKE_BUILD_TYPE=Release && cmake --build build`
- [ ] Python can `import meridian_inference` after build
- [ ] Batch predictions match PyTorch output within floating point tolerance
- [ ] Benchmark shows measurable speedup (expected: 2-5x for small models)
- [ ] C++ unit tests written with Google Test or Catch2

---

## Phase 8: C# .NET and Ruby Services
### Week 8 | C#, Ruby, MySQL

---

### Sprint Goal
C# SignalR notification hub broadcasting model training events in real time. Ruby Sinatra service managing the ML pipeline trigger queue with MySQL.

These are covered in detail in Nexus Phase 8. Follow those tasks directly. The new element here is integration: the Ruby service triggers the Spring Boot orchestrator, which pushes to Redis, which the Go service reads.

**Additional C# task unique to Meridian:** an ASP.NET Core WebAPI endpoint that reads from MySQL and returns model run history — demonstrates Entity Framework Core distinct from the SignalR work in Nexus.

```csharp
// services/dotnet-notif/Controllers/ModelHistoryController.cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

[ApiController]
[Route("api/[controller]")]
public class ModelHistoryController : ControllerBase
{
    private readonly MeridianDbContext _context;

    public ModelHistoryController(MeridianDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<ModelRun>>> GetHistory(
        [FromQuery] string? modelType,
        [FromQuery] int limit = 50)
    {
        var query = _context.ModelRuns.AsQueryable();
        if (modelType != null)
            query = query.Where(r => r.ModelType == modelType);
        return await query
            .OrderByDescending(r => r.CompletedAt)
            .Take(limit)
            .ToListAsync();
    }
}
```

---

### Phase 8 Deliverable Checklist
- [ ] C# SignalR hub connects from Angular and pushes training events
- [ ] C# Entity Framework Core queries MySQL model run history
- [ ] Ruby Sinatra `/trigger` endpoint writes a job to MySQL and enqueues to Redis
- [ ] End-to-end: Ruby trigger → MySQL → Redis → Spring Boot → Flask ML → C# notification → Angular

---

## Phase 9: Kubernetes and Terraform — Multi-Cloud
### Weeks 9-10 | Kubernetes, Terraform, AWS, Azure

---

### Sprint Goal
Meridian deployed to AWS EKS via Terraform. Azure AKS configured as DR. Helm charts for every service. HPA autoscaling on the Go prediction API.

This phase follows the Nexus Phase 10 pattern. The key difference here is that Meridian adds Helm and Horizontal Pod Autoscaler, which are not in Nexus.

---

### Task 9.1: Helm Charts

```bash
# Create Helm chart for the Go prediction API
helm create charts/go-prediction
```

```yaml
# charts/go-prediction/values.yaml
replicaCount: 3

image:
  repository: ghcr.io/codebun203/meridian-go-prediction
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 8080

autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 20
  targetCPUUtilizationPercentage: 70
  # Scale on request rate, not just CPU
  targetMemoryUtilizationPercentage: 80

resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "500m"

env:
  REDIS_URL: redis://redis-master:6379
  MONGO_URL: mongodb://mongodb:27017
  ML_SERVICE_URL: http://flask-ml:5001
```

```yaml
# charts/go-prediction/templates/hpa.yaml
{{- if .Values.autoscaling.enabled }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: {{ include "go-prediction.fullname" . }}
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{ include "go-prediction.fullname" . }}
  minReplicas: {{ .Values.autoscaling.minReplicas }}
  maxReplicas: {{ .Values.autoscaling.maxReplicas }}
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: {{ .Values.autoscaling.targetCPUUtilizationPercentage }}
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: {{ .Values.autoscaling.targetMemoryUtilizationPercentage }}
{{- end }}
```

---

### Task 9.2: AWS Terraform with ECR

```hcl
# infra/aws/ecr.tf
resource "aws_ecr_repository" "services" {
  for_each = toset([
    "go-prediction",
    "spring-orchestrator",
    "flask-ml",
    "dotnet-notif",
    "ruby-pipeline",
  ])

  name                 = "meridian/${each.key}"
  image_tag_mutability = "MUTABLE"

  image_scanning_configuration {
    scan_on_push = true  # automatic vulnerability scanning on every push
  }
}

# ECR lifecycle policy — keep last 10 images, expire untagged after 7 days
resource "aws_ecr_lifecycle_policy" "cleanup" {
  for_each   = aws_ecr_repository.services
  repository = each.value.name

  policy = jsonencode({
    rules = [
      {
        rulePriority = 1
        selection = {
          tagStatus   = "untagged"
          countType   = "sinceImagePushed"
          countUnit   = "days"
          countNumber = 7
        }
        action = { type = "expire" }
      }
    ]
  })
}
```

```hcl
# infra/aws/iam.tf
# Principle of least privilege — each service gets only the permissions it needs
resource "aws_iam_role" "go_prediction" {
  name = "meridian-go-prediction"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect    = "Allow"
      Principal = { Service = "ec2.amazonaws.com" }
      Action    = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy" "go_prediction_s3" {
  role = aws_iam_role.go_prediction.id
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject"]         # read only
      Resource = "arn:aws:s3:::meridian-models/*"
    }]
  })
}
```

---

### Task 9.3: Azure Terraform

```hcl
# infra/azure/main.tf
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "meridian" {
  name     = "meridian-dr"
  location = "West US 2"
}

resource "azurerm_kubernetes_cluster" "dr" {
  name                = "meridian-aks-dr"
  location            = azurerm_resource_group.meridian.location
  resource_group_name = azurerm_resource_group.meridian.name
  dns_prefix          = "meridian-dr"

  default_node_pool {
    name       = "default"
    node_count = 2
    vm_size    = "Standard_D2_v2"
    # Enable autoscaling on the DR cluster too
    enable_auto_scaling = true
    min_count           = 1
    max_count           = 5
  }

  identity { type = "SystemAssigned" }

  tags = {
    Environment = "disaster-recovery"
    Project     = "Meridian"
  }
}

resource "azurerm_container_registry" "meridian" {
  name                = "meridianregistry"
  resource_group_name = azurerm_resource_group.meridian.name
  location            = azurerm_resource_group.meridian.location
  sku                 = "Basic"
  admin_enabled       = true
}
```

---

### Phase 9 Deliverable Checklist
- [ ] `helm install meridian-go-prediction charts/go-prediction` deploys to local cluster
- [ ] HPA scales Go prediction pods when CPU load exceeds 70%
- [ ] `terraform apply` in `infra/aws/` creates EKS cluster (or plan verified)
- [ ] `terraform apply` in `infra/azure/` creates AKS cluster
- [ ] ECR repositories created with lifecycle policies and scan-on-push
- [ ] IAM roles follow least-privilege principle (separate roles per service)

---

## Phase 10: SDN, IoT, and OpenFlow
### Week 11 | SDN, IoT, OpenFlow, TCP/IP

---

### Sprint Goal
Simulate an IoT sensor network, implement an OpenFlow controller to segment traffic between sensor groups, and add raw TCP/IP packet analysis for anomaly detection at the network layer.

Follow Nexus Phase 12 for the OpenFlow controller and TCP/IP monitor implementation. The new Meridian-specific addition is the IoT sensor simulator that feeds real data into the pipeline.

---

### Task 10.1: IoT Sensor Simulator

```python
# services/iot-simulator/src/simulator.py
import asyncio
import json
import random
import time
import httpx
from dataclasses import dataclass, asdict

@dataclass
class SensorReading:
    device_id: str
    device_type: str
    value: float
    unit: str
    latitude: float
    longitude: float
    firmware_version: str
    battery_level: int
    timestamp: float

DEVICE_TYPES = {
    "temperature": {"unit": "celsius", "range": (15.0, 35.0), "anomaly_range": (60.0, 80.0)},
    "pressure":    {"unit": "psi",     "range": (80.0, 120.0), "anomaly_range": (150.0, 200.0)},
    "vibration":   {"unit": "hz",      "range": (0.1, 2.0),   "anomaly_range": (8.0, 15.0)},
}

async def simulate_device(device_id: str, device_type: str,
                           ingest_url: str, anomaly_rate: float = 0.02):
    """Simulate a single IoT device sending readings at 1Hz.

    anomaly_rate: fraction of readings that are anomalous.
    This creates a realistic label distribution for training.
    """
    config = DEVICE_TYPES[device_type]
    async with httpx.AsyncClient() as client:
        while True:
            is_anomaly = random.random() < anomaly_rate
            value_range = config["anomaly_range"] if is_anomaly else config["range"]
            reading = SensorReading(
                device_id=device_id,
                device_type=device_type,
                value=random.uniform(*value_range),
                unit=config["unit"],
                latitude=47.6062 + random.uniform(-0.1, 0.1),
                longitude=-122.3321 + random.uniform(-0.1, 0.1),
                firmware_version="1.3.2",
                battery_level=random.randint(20, 100),
                timestamp=time.time(),
            )
            try:
                await client.post(ingest_url, json=asdict(reading), timeout=2.0)
            except httpx.RequestError:
                pass  # silently drop if ingestor is unavailable — real IoT behavior
            await asyncio.sleep(1.0)

async def run_fleet(n_devices: int = 50, ingest_url: str = "http://localhost:8080/ingest"):
    """Simulate a fleet of IoT sensors across all device types."""
    tasks = []
    for i in range(n_devices):
        device_type = list(DEVICE_TYPES.keys())[i % len(DEVICE_TYPES)]
        task = asyncio.create_task(
            simulate_device(f"sensor_{i:04d}", device_type, ingest_url)
        )
        tasks.append(task)
    await asyncio.gather(*tasks)

if __name__ == "__main__":
    asyncio.run(run_fleet(n_devices=50))
```

---

### Phase 10 Deliverable Checklist
- [ ] IoT simulator sends 50 simulated devices to Go ingestor at 1Hz
- [ ] MongoDB receives events from all devices — confirm with `mongosh`
- [ ] OpenFlow controller (from Nexus Phase 12) segments sensor traffic by device group
- [ ] TCP/IP packet monitor logs inter-service connections
- [ ] Anomaly rate in simulated data matches label distribution in training set

---

## Phase 11: Swift iOS App
### Week 12 | Swift, SwiftUI

---

### Sprint Goal
An iOS app that displays live anomaly scores from the Go prediction API, shows training job status from Spring Boot, and receives push notifications when anomaly scores exceed 0.9.

Follow Nexus Phase 13 for the full implementation. The Meridian-specific additions are:

1. A chart view showing anomaly score history using Swift Charts (native iOS framework, no third-party library)
2. Background fetch that polls the prediction API every 15 minutes even when the app is not active

```swift
// mobile/ios/Meridian/Views/AnomalyChartView.swift
import SwiftUI
import Charts

struct AnomalyChartView: View {
    let readings: [AnomalyReading]

    var body: some View {
        Chart(readings) { reading in
            LineMark(
                x: .value("Time", reading.timestamp),
                y: .value("Score", reading.anomalyScore),
            )
            .foregroundStyle(reading.isAnomaly ? .red : .blue)

            if reading.isAnomaly {
                PointMark(
                    x: .value("Time", reading.timestamp),
                    y: .value("Score", reading.anomalyScore),
                )
                .foregroundStyle(.red)
                .symbolSize(50)
            }
        }
        .chartYScale(domain: 0...1)
        .chartYAxis {
            AxisMarks(values: [0.0, 0.5, 0.65, 0.8, 1.0]) { value in
                AxisValueLabel()
                AxisGridLine()
                if value.as(Double.self) == 0.65 {
                    AxisGridLine().foregroundStyle(.red)
                }
            }
        }
        .frame(height: 200)
    }
}
```

---

### Phase 11 Deliverable Checklist
- [ ] iOS app builds in Xcode simulator
- [ ] Swift Charts anomaly score chart renders with red markers on anomalies
- [ ] Background fetch polls prediction API and updates badge count
- [ ] Spring Boot job status visible in a list with live status badges

---

## End State: What You Can Say

### On Go
"I built the prediction-serving layer in Go because it's the part of the system that handles the highest concurrency. When 50 IoT devices send readings per second each with its own response latency, Python's GIL becomes a bottleneck. Go's goroutine-per-request model handles this with a fraction of the memory overhead of thread-per-request. I benchmarked both — the Go service handles 10,000 concurrent connections with stable 5ms p99 latency where the Python equivalent started degrading at 500."

### On ML frameworks
"I trained three models and compared them deliberately. The Scikit-learn Random Forest was the baseline — it hit 0.81 AUC with no hyperparameter tuning. The PyTorch LSTM improved this to 0.87 because it captures the temporal dependency before an anomaly, not just the instantaneous reading. TensorFlow was for the forecasting task, not classification — Keras's fit/callback API is faster to iterate with for sequence-to-sequence architectures. JAX I used for a custom smoothness loss function that penalizes temporally discontinuous predictions — that isn't cleanly expressible in PyTorch without writing a custom autograd function."

### On Kubernetes
"Every service has a Helm chart with HPA configured. The Go prediction service scales from 2 to 20 pods based on CPU. I set the target utilization at 70% not 80% to give headroom before a traffic spike degrades latency. I learned the difference between Deployment and StatefulSet — MongoDB and Redis need StatefulSets because each pod needs a stable network identity and its own persistent volume claim. Deployments are for stateless services."

### On Terraform / multi-cloud
"Terraform state is in S3 with DynamoDB locking — without locking, two simultaneous `terraform apply` runs can corrupt the state file. I have separate state files for AWS and Azure. The module structure means if I want to add a GCP cluster, I duplicate the AKS module, change the provider, and the rest of the code is identical. I never run `terraform apply` without `terraform plan` first — same discipline as git diff before git commit."

### On Spark / Scala
"I wrote the ETL in Scala rather than PySpark because Spark is a JVM library. When a PySpark job fails, the error originates in Scala and surfaces through the Python wrapper — you get JVM stack traces that are unreadable if you don't know Scala. The Scala API also has type safety that PySpark doesn't — I can't accidentally pass the wrong column name to a transformation without a compile error. The Spark MLlib Pipeline I built is equivalent to Scikit-learn's Pipeline — it ensures the scaler fits only on training data and that inference applies the same transformation."

---

## Quick Reference: Gap Skills by Phase

| Gap Skill | Phase |
|---|---|
| MongoDB, Redis, MySQL | 1 |
| Go | 2 |
| Java, Spring Boot | 3 |
| Pandas, NumPy (direct), Scikit-learn, PyTorch, TensorFlow, JAX, Flask | 4 |
| Scala, Apache Spark | 5 |
| Angular | 6 |
| C++ | 7 |
| C#, Ruby | 8 |
| Kubernetes (Helm, HPA), Terraform, AWS, Azure | 9 |
| SDN/IoT, OpenFlow | 10 |
| Swift | 11 |
