# Tutorial: Go (Golang)

> Goal: learn Go by rewriting a real service — your `webhook-gw` reverse proxy is the perfect first
> Go project (HTTP, routing, concurrency, a single static binary). By the end you'll have a Go
> container running in your real platform.

## Why Go (the interview framing)
Compiled, statically typed, garbage-collected. Ships a **single static binary** (great for Docker —
`FROM scratch` images of a few MB). **Concurrency is built in** (goroutines + channels). Standard
library has a production HTTP server. Used for infra/networking tooling (Docker, Kubernetes,
Terraform are all Go).

## Step 1 — install & hello
```bash
sudo snap install go --classic   # or download from go.dev
go version
mkdir webhook-gw-go && cd $_ && go mod init github.com/CodeBun203/webhook-gw-go
```
`main.go`:
```go
package main

import "fmt"

func main() {
    fmt.Println("hello, washgen")
}
```
```bash
go run .          # compile + run
go build -o app . # produce a binary
```

## Step 2 — the language in one screen
```go
// variables & types
var x int = 5
y := "inferred"          // := short declaration
const Pi = 3.14

// functions return values AND errors (no exceptions)
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("divide by zero")   // errors are values
    }
    return a / b, nil
}

// the universal error pattern — you'll write this constantly
result, err := divide(10, 2)
if err != nil {
    log.Fatal(err)
}

// structs (no classes) + methods
type Route struct {
    Target string
    Secret string
}
func (r Route) IsProtected() bool { return r.Secret != "" }

// interfaces are implicit (duck typing) — implement methods, you satisfy the interface
type Handler interface { Handle(msg string) error }

// slices (dynamic arrays) and maps
nums := []int{1, 2, 3}
nums = append(nums, 4)
routes := map[string]Route{"st-imessage": {Target: "http://..."}}

defer file.Close()       // defer runs at function exit (cleanup)
```
Key idioms to name: **errors as values** (no try/catch), **`defer`**, **implicit interfaces**,
**zero values**, **`context.Context`** (cancellation/timeouts), **slices vs. arrays**.

## Step 3 — an HTTP server (rebuild webhook-gw's core)
```go
package main

import (
    "io"
    "log"
    "net/http"
    "time"
)

type Gateway struct {
    routes map[string]string   // path -> target URL
    client *http.Client
}

func (g *Gateway) handle(w http.ResponseWriter, r *http.Request) {
    target, ok := g.routes[r.URL.Path[1:]]   // strip leading /
    if !ok {
        http.Error(w, "unknown route", http.StatusNotFound)
        return
    }
    // forward the body to the internal target
    resp, err := g.client.Post(target, r.Header.Get("Content-Type"), r.Body)
    if err != nil {
        http.Error(w, "upstream error", http.StatusBadGateway)
        return
    }
    defer resp.Body.Close()
    w.WriteHeader(resp.StatusCode)
    io.Copy(w, resp.Body)
}

func main() {
    g := &Gateway{
        routes: map[string]string{"st-imessage": "http://st-imessage:8060/webhooks/servicetitan"},
        client: &http.Client{Timeout: 10 * time.Second},
    }
    http.HandleFunc("/", g.handle)
    log.Println("listening on :8070")
    log.Fatal(http.ListenAndServe(":8070", nil))
}
```

## Step 4 — concurrency (the Go superpower)
```go
// goroutine: `go` launches a lightweight thread
go processWebhook(payload)            // returns immediately, runs concurrently

// channels: typed pipes for communicating between goroutines
results := make(chan string)
go func() { results <- doWork() }()   // send
msg := <-results                       // receive (blocks until ready)

// fan-out a batch of jobs, collect results — classic worker pool
jobs := make(chan int, 100)
for w := 0; w < 5; w++ {              // 5 workers
    go func() { for j := range jobs { handle(j) } }()
}
```
Soundbite: "Go gives me a goroutine per request and channels to coordinate — cheap concurrency
without callback hell." Mention `sync.WaitGroup`, `sync.Mutex`, `context` for cancellation.

## Step 5 — testing (built into the toolchain — no framework needed)
`gateway_test.go` (test files end in `_test.go`, functions start with `Test`):
```go
package main

import (
    "net/http"
    "net/http/httptest"
    "testing"
)

func TestUnknownRoute(t *testing.T) {
    g := &Gateway{routes: map[string]string{}}
    req := httptest.NewRequest(http.MethodPost, "/nope", nil)
    rec := httptest.NewRecorder()

    g.handle(rec, req)

    if rec.Code != http.StatusNotFound {
        t.Errorf("got %d, want 404", rec.Code)   // table-driven style scales from here
    }
}
```
```bash
go test ./...                 # run all tests
go test -v -cover ./...       # verbose + coverage (built in!)
go test -race ./...           # the race detector — catches data races
```
- **`httptest`** (stdlib) mocks HTTP — no extra deps.
- **Table-driven tests** are the Go convention: a slice of `{input, want}` cases in a loop.
- Popular assertion lib: **`testify`** (`assert`/`require`/`mock`) if you want nicer asserts.

## Step 6 — tiny production Docker image
```dockerfile
FROM golang:1.22 AS build
WORKDIR /src
COPY . .
RUN CGO_ENABLED=0 go build -o /app .

FROM scratch                 # empty base — just your binary
COPY --from=build /app /app
ENTRYPOINT ["/app"]
```
Soundbite: "The Go gateway ships as a ~10MB `scratch` image vs. the ~150MB Python one."

## Most common libraries / packages
| Need | Package |
|---|---|
| HTTP server/client | stdlib **`net/http`** (often enough!) |
| Routing | **chi**, **gorilla/mux**, or stdlib `http.ServeMux` (1.22+) |
| Web framework | **Gin**, **Echo**, **Fiber** |
| JSON | stdlib **`encoding/json`** |
| SQL | stdlib **`database/sql`** + driver; **sqlx**, **GORM** (ORM) |
| Config/env | **viper**, `os.Getenv` |
| Testing | stdlib **`testing`** + **`httptest`**; **testify** |
| Logging | stdlib **`log/slog`** (structured) |
| Concurrency | stdlib `sync`, `context`, channels |

## Interview soundbites
- "I rewrote the webhook gateway in Go — `net/http`, a goroutine per request, channel-based worker
  pool, HMAC verification — for a single static binary and a fraction of the memory."
- Know: **goroutine/channel, errors-as-values, `defer`, implicit interfaces, `context`, slice/map,
  `go test`/race detector, static binary, GC.** Contrast with Python: "compiled + typed + cheap
  concurrency vs. Python's GIL for CPU-bound work."
