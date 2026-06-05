# Tutorial: Apache Spark + MongoDB

> Goal: do ONE real, small thing with each on data you already have, so you can claim "I've used it"
> honestly. Spark via **PySpark** (your Python skills transfer); MongoDB as a document store for
> your webhook event log.

---

## PART 1 — APACHE SPARK (via PySpark)

### Mental model
Spark = a **distributed data-processing engine** for big data. Core abstraction: the **DataFrame**
(a distributed, partitioned table) you transform with a SQL-like API. **Lazy evaluation** —
transformations build a plan (a **DAG**); nothing runs until an **action** (`.show()`, `.count()`,
`.write`). Scales the same code from your laptop to a cluster. Written in Scala; **PySpark** is the
Python API.

### Step 1 — run locally (no cluster needed)
```bash
pip install pyspark
```
```python
from pyspark.sql import SparkSession
from pyspark.sql import functions as F

spark = SparkSession.builder.appName("leads").master("local[*]").getOrCreate()

# read real data you have (export your CRM/BigQuery leads to CSV/Parquet)
df = spark.read.option("header", True).csv("/mnt/raid1/exports/leads.csv")

# transformations (lazy — nothing runs yet)
by_source = (df
    .filter(F.col("status") == "won")
    .groupBy("lead_source")
    .agg(F.count("*").alias("wins"),
         F.avg("deal_value").alias("avg_value"))
    .orderBy(F.desc("wins")))

by_source.show()          # ACTION — now the DAG executes
by_source.write.mode("overwrite").parquet("/mnt/raid1/exports/wins_by_source")
```

### Key concepts to name
- **DataFrame / RDD** (RDD = lower-level), **transformation vs. action**, **lazy evaluation**,
  **DAG**, **partition**, **shuffle** (expensive cross-partition data movement — the thing you tune),
  **`groupBy`/`agg`/`join`**, **Catalyst optimizer**, **Parquet** (columnar format Spark loves),
  **driver vs. executors**, **cluster manager (YARN/Kubernetes/standalone)**.

### Spark SQL (same engine, SQL syntax)
```python
df.createOrReplaceTempView("leads")
spark.sql("SELECT lead_source, COUNT(*) FROM leads WHERE status='won' GROUP BY lead_source").show()
```

### Testing PySpark
```python
import pytest
from pyspark.sql import SparkSession

@pytest.fixture(scope="session")
def spark():
    return SparkSession.builder.master("local[1]").appName("test").getOrCreate()

def test_win_count(spark):
    df = spark.createDataFrame([("won", "costco"), ("lost", "lowes"), ("won", "costco")],
                               ["status", "lead_source"])
    wins = df.filter(df.status == "won").count()
    assert wins == 2
```
- Use a local `SparkSession` fixture; **`chispa`** library gives nice DataFrame-equality asserts.

### Common Spark libraries/modules
- `pyspark.sql` (DataFrames — 90% of work), `pyspark.sql.functions` (`F.*`), `pyspark.ml` (MLlib —
  distributed ML), Spark Structured Streaming (real-time). Formats: **Parquet**, Delta Lake.

### Honest soundbite
"I've used PySpark for batch aggregation over our lead/CRM data — DataFrame transforms, groupBy/agg,
writing Parquet. I understand lazy evaluation, the DAG, and that shuffles are the cost to watch. For
our data volume it's more capability than necessity, but I know the model."

---

## PART 2 — MONGODB

### Mental model
**Document database (NoSQL)** — stores **BSON** (binary JSON) **documents** in **collections** (vs.
rows in tables). Flexible schema. Great fit for **your webhook event log** — heterogeneous JSON
payloads from different sources, no fixed columns. Contrast with Postgres: "relational/normalized +
ACID vs. document/flexible-schema."

### Step 1 — run it (add to compose)
```yaml
  mongo:
    image: mongo:7
    ports: ["127.0.0.1:27017:27017"]
    volumes: ["mongo_data:/data/db"]
```

### Step 2 — CRUD with PyMongo (store webhook events)
```python
from pymongo import MongoClient
db = MongoClient("mongodb://mongo:27017").washgen

# insert a webhook event (any shape — that's the point)
db.events.insert_one({
    "source": "st-imessage",
    "received_at": "2026-06-04T17:00:00Z",
    "payload": {"job_id": 123, "status": "completed"},   # nested, schema-free
})

# query (find) — Mongo's filter syntax
recent = db.events.find({"source": "st-imessage"}).sort("received_at", -1).limit(10)

# aggregation pipeline (Mongo's groupBy)
pipeline = [
    {"$match": {"source": "st-imessage"}},
    {"$group": {"_id": "$payload.status", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}},
]
for row in db.events.aggregate(pipeline):
    print(row)

db.events.create_index([("source", 1), ("received_at", -1)])   # indexing matters in Mongo too
```

### Key concepts to name
- **Document, collection, BSON, `_id` (ObjectId), flexible schema, embedding vs. referencing,
  aggregation pipeline (`$match`/`$group`/`$sort`/`$lookup`), index, sharding (horizontal scale),
  replica set (HA), eventual consistency, CAP theorem.**
- **When to choose Mongo vs. Postgres:** "Document store for heterogeneous, evolving, read-mostly
  JSON (event logs, catalogs); relational for normalized, transactional, joined data. I run Postgres
  as my system of record and would use Mongo for the webhook event log."

### Testing MongoDB code
- **`mongomock`** — in-memory Mongo fake for unit tests:
```python
import mongomock
def test_insert():
    db = mongomock.MongoClient().db
    db.events.insert_one({"source": "x"})
    assert db.events.count_documents({"source": "x"}) == 1
```
- **`testcontainers[mongodb]`** for real integration tests.

### Common MongoDB libraries
| Language | Driver / ODM |
|---|---|
| Python | **PyMongo**, **Motor** (async), **Beanie**/**MongoEngine** (ODM) |
| Node | **mongodb** driver, **Mongoose** (ODM) |
| Testing | **mongomock**, testcontainers |

### Honest soundbite
"I'd use MongoDB for document-shaped data like our webhook event log — flexible schema, aggregation
pipelines, indexing. My transactional system of record stays in Postgres. I understand the
relational-vs-document tradeoff and can justify the choice."
