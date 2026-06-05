# Tutorial: Redis (caching, rate limiting, job queues)

> Goal: add Redis to a real FastAPI service. Follow along by adding a cache to `WG_AI_Assistant`
> and a rate limiter to `webhook-gw`.

## Mental model
Redis is an **in-memory key-value store** (data lives in RAM → microsecond reads). Common uses:
**cache**, **rate limiter**, **session store**, **pub/sub**, **job/message broker**. Single-threaded
command execution → atomic operations. Data is volatile by default but can persist (RDB snapshots /
AOF log).

## Step 0 — run it (add to your existing docker-compose)
```yaml
  redis:
    image: redis:7-alpine
    command: ["redis-server", "--maxmemory", "256mb", "--maxmemory-policy", "allkeys-lru"]
    ports: ["127.0.0.1:6379:6379"]   # loopback only, matching your hardening
    volumes: ["redis_data:/data"]
```
`maxmemory-policy allkeys-lru` = when full, **evict least-recently-used** keys (classic cache config).

## Core commands (try in `docker exec -it redis redis-cli`)
```
SET user:1 "Hayden"            # string
GET user:1
SETEX session:abc 3600 "data"  # value with 3600s TTL (expiry)
TTL session:abc                # seconds left
INCR rate:1.2.3.4              # atomic counter (rate limiting)
EXPIRE rate:1.2.3.4 60         # set TTL on existing key
HSET job:1 status queued       # hash (object)
LPUSH queue:emails "msg1"      # list (queue) — push
BRPOP queue:emails 5           # blocking pop (a worker waits for jobs)
PUBLISH events "new_lead"      # pub/sub
```
**Key types to name in interviews:** string, hash, list, set, sorted set (ZSET), stream.

## Pattern 1 — cache-aside (the most-asked pattern)
In `WG_AI_Assistant`, cache answers so repeated questions skip LLM inference:
```python
import redis, hashlib, json
r = redis.Redis(host="redis", port=6379, decode_responses=True)

def answer(question: str) -> str:
    key = "rag:" + hashlib.sha256(question.encode()).hexdigest()
    cached = r.get(key)            # 1. look in cache
    if cached:
        return json.loads(cached)  #    HIT → return fast
    result = run_rag_chain(question)   # 2. MISS → do the expensive work
    r.setex(key, 3600, json.dumps(result))  # 3. store with 1h TTL
    return result
```
**Cache-aside** = app checks cache, falls back to source, populates cache. Mention **TTL**,
**eviction policy**, **cache invalidation** ("the two hard problems").

## Pattern 2 — rate limiting (for `webhook-gw`)
```python
def allow(ip: str, limit=100, window=60) -> bool:
    key = f"rate:{ip}"
    count = r.incr(key)            # atomic
    if count == 1:
        r.expire(key, window)      # start the window on first hit
    return count <= limit          # fixed-window rate limit
```
Name the algorithm: **fixed-window** (simple), vs **sliding-window** / **token-bucket** (smoother).

## Pattern 3 — background job queue
Use **RQ** (Redis Queue) so email/RAG ingestion runs off the HTTP path:
```python
# producer (inside the API request)
from redis import Redis
from rq import Queue
q = Queue(connection=Redis(host="redis"))
q.enqueue(process_email, email_id)   # returns immediately; work happens in a worker

# worker process (separate container): `rq worker`
```
For heavier needs name **Celery** (Redis or RabbitMQ as broker). Terms: **broker**, **producer/
consumer**, **worker**, **task**, **idempotency**.

## Writing tests
- **`fakeredis`** — in-memory Redis fake, no server needed:
```python
import fakeredis, pytest

@pytest.fixture
def r():
    return fakeredis.FakeStrictRedis(decode_responses=True)

def test_cache_set_get(r):
    r.setex("k", 60, "v")
    assert r.get("k") == "v"
    assert r.ttl("k") <= 60
```
- For integration tests, spin a real `redis:7-alpine` via **testcontainers** or a CI service
  container (`services: redis:` in GitHub Actions).

## Most common libraries / clients
| Language | Client | Notes |
|---|---|---|
| Python | `redis` (redis-py) | sync + async; `redis.asyncio` for FastAPI |
| Python jobs | `rq`, `celery` | background workers |
| Node | `ioredis`, `node-redis` | `ioredis` is the common choice |
| Caching helper | `aiocache`, `cashews` (Py) | decorators over Redis |
| Testing | `fakeredis`, `testcontainers` | unit + integration |

## Interview soundbites
- "Redis as a cache-aside layer with TTL + LRU eviction for hot reads; as a fixed-window rate
  limiter on the webhook gateway; and as the broker for background jobs via RQ."
- Know: **in-memory, single-threaded/atomic, TTL, eviction (LRU), persistence (RDB/AOF), pub/sub,
  broker, cache-aside, cache invalidation.**
