# Database

## Choose Databases by Access Pattern

Pick the model that matches how you read, write, and relate data. Below are detailed explanations with real-world examples and consistent schema/structure definitions.

## Relational (SQL)

Structured tables with rows and columns, strict schemas, joins, and ACID transactions.

- Strengths: Strong consistency, constraints, multi-row transactions, joins, mature tooling.
- Real-world example: E-commerce order management and payments where correctness is critical.
- Typical fit: Orders, payments, users, inventory, financial ledgers.

```sql
-- PostgreSQL schema
CREATE TABLE users(
  id SERIAL PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE orders(
  id SERIAL PRIMARY KEY,
  user_id INT NOT NULL REFERENCES users(id),
  total NUMERIC(12,2) CHECK (total >= 0),
  status TEXT CHECK (status IN ('PENDING','PAID','CANCELLED')),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Query
UPDATE orders SET status = 'PAID' WHERE id = 101 AND status = 'PENDING';

SELECT u.email, COUNT(*) AS orders, SUM(o.total) AS revenue
FROM users u JOIN orders o ON o.user_id = u.id
WHERE o.status = 'PAID'
GROUP BY u.email;
```

## Document (JSON)

Stores JSON-like documents with flexible schemas for heterogeneous records.

- Strengths: Flexible attributes per item, natural nesting, selective indexing, quick iteration.
- Real-world example: Product catalog with variable attributes across categories.
- Typical fit: Catalogs, CMS, user profiles, event payload stores.

```javascript
// MongoDB schema and indexes
db.createCollection("products", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["sku", "name"],
      properties: {
        sku: { bsonType: "string" },
        name: { bsonType: "string" },
        variants: { bsonType: "array" },
        tags: { bsonType: "array" }
      }
    }
  }
});

db.products.createIndex({ "variants.size": 1, tags: 1 });
db.products.createIndex({ sku: 1 }, { unique: true });

// Insert and query
db.products.insertOne({
  sku: "P-100",
  name: "Running Shoes",
  variants: [
    { size: 9, color: "black", stock: 12 },
    { size: 10, color: "white", stock: 5 }
  ],
  tags: ["sport","footwear"]
});

db.products.find(
  { tags: "sport", "variants.size": 9 },
  { name: 1, sku: 1 }
);
```

## Key-Value and In-Memory (Redis)

Ultra-low latency key → value with rich data types, TTLs, pub/sub, and streams.

- Strengths: Millisecond reads/writes, TTL-based caching, atomic operations, realtime messaging.
- Real-world example: Checkout service caches product prices; notifications via pub/sub; durable event pipeline via Streams.
- Typical fit: Caching, sessions, rate limits, leaderboards, realtime fan-out, background queueing.

```bash
# Data structure setup
# String with TTL for cache
SET price:P-100 "59.99" EX 300

# Hash for session data
HSET session:abc123 userId 42 role "admin" cartItems 3
EXPIRE session:abc123 3600

# Sorted set for leaderboards
ZADD leaderboard:daily 1500 "user:42" 1200 "user:99"

# Rate limiting with counter
INCR rate:ip:1.2.3.4
EXPIRE rate:ip:1.2.3.4 60

# Pub/Sub channel setup
SUBSCRIBE order_events
PUBLISH order_events "order:101:status=PAID"

# Stream setup and consumer group
XADD orders * orderId 101 status PAID total 59.99
XGROUP CREATE orders order-workers $ MKSTREAM
XREADGROUP GROUP order-workers c1 COUNT 10 STREAMS orders >
```

## Column-Family (Wide-Column)

Partitioned rows with clustered columns. Designed for high write throughput and predictable, time-ordered reads.

- Strengths: Linear horizontal scaling, write-heavy workloads, time-ordered access by key, tunable consistency.
- Real-world example: IoT platform storing sensor readings per device for millions of devices.
- Typical fit: Device events, clickstreams, messaging metadata, audit logs per key.

```sql
-- Cassandra schema
CREATE KEYSPACE iot WITH replication = {
  'class': 'NetworkTopologyStrategy',
  'dc1': 3
};

CREATE TABLE iot.device_events (
  device_id TEXT,
  ts TIMESTAMP,
  metric TEXT,
  value DOUBLE,
  PRIMARY KEY (device_id, ts)
) WITH CLUSTERING ORDER BY (ts DESC)
  AND compaction = {'class': 'TimeWindowCompactionStrategy'}
  AND default_time_to_live = 2592000;

-- Query by partition key
SELECT * FROM iot.device_events
WHERE device_id = 'dev-42'
LIMIT 100;
```

## Time-Series

Purpose-built for timestamped data with retention, downsampling, and time-window queries.

- Strengths: Compression, retention policies, continuous aggregates, fast time-bucket queries.
- Real-world example: Production monitoring collects CPU, latency, and error rates for dashboards and alerts.
- Typical fit: Metrics, IoT sensors, SRE observability, financial ticks.

```sql
-- TimescaleDB schema
CREATE TABLE metrics(
  ts TIMESTAMPTZ NOT NULL,
  service TEXT NOT NULL,
  latency_ms DOUBLE PRECISION,
  error_count INT DEFAULT 0
);

SELECT create_hypertable('metrics', 'ts');

CREATE INDEX idx_service ON metrics (service, ts DESC);

-- Retention policy
SELECT add_retention_policy('metrics', INTERVAL '90 days');

-- Continuous aggregate for rollups
CREATE MATERIALIZED VIEW metrics_hourly
WITH (timescaledb.continuous) AS
SELECT time_bucket('1 hour', ts) AS hour,
       service,
       percentile_cont(0.5) WITHIN GROUP (ORDER BY latency_ms) AS p50,
       percentile_cont(0.95) WITHIN GROUP (ORDER BY latency_ms) AS p95
FROM metrics
GROUP BY hour, service;

-- Query
SELECT date_trunc('minute', ts) AS t,
       percentile_cont(0.5) WITHIN GROUP (ORDER BY latency_ms) AS p50
FROM metrics
WHERE service = 'checkout'
  AND ts >= now() - interval '1 hour'
GROUP BY t
ORDER BY t;
```

## Vector Databases

Store dense embeddings to enable kNN similarity search for semantic retrieval.

- Strengths: ANN indexes (HNSW/IVF), cosine/L2/IP distance, metadata filters, hybrid search.
- Real-world example: Customer support portal retrieves similar answers to a user's question for RAG.
- Typical fit: Semantic search, recommendations, deduplication, clustering.

```sql
-- PostgreSQL + pgvector schema
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE docs(
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  body TEXT,
  category TEXT,
  emb VECTOR(768),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- HNSW index for fast similarity search
CREATE INDEX ON docs USING hnsw (emb vector_cosine_ops);

CREATE INDEX ON docs(category);

-- Query by embedding vector (cosine distance)
SELECT id, title, category
FROM docs
WHERE category = 'support'
ORDER BY emb <=> '[0.12, 0.03, -0.08, ...]'
LIMIT 5;
```

## Search Engines (Full-Text)

Inverted indexes with analyzers, stemming, scoring, facets, and aggregations.

- Strengths: Relevance ranking (BM25), typo tolerance, phrase queries, faceted navigation, analytics.
- Real-world example: E-commerce site search with filters by brand, price, and ratings.
- Typical fit: App/site search, help centers, log analytics, product discovery.

```json
// Elasticsearch index mapping
PUT /products
{
  "mappings": {
    "properties": {
      "name": { "type": "text", "analyzer": "standard" },
      "brand": { "type": "keyword" },
      "desc": { "type": "text" },
      "price": { "type": "integer" },
      "rating": { "type": "float" },
      "created_at": { "type": "date" }
    }
  },
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1
  }
}

// Index document
POST /products/_doc
{
  "name": "Wireless Mouse",
  "brand": "Acme",
  "desc": "Ergonomic, silent clicks",
  "price": 1299,
  "rating": 4.5
}

// Search with aggregations
POST /products/_search
{
  "query": {
    "multi_match": {
      "query": "ergonomic mouse",
      "fields": ["name^2", "desc"]
    }
  },
  "aggs": {
    "brands": { "terms": { "field": "brand" } },
    "price_ranges": {
      "range": {
        "field": "price",
        "ranges": [
          { "to": 1000 },
          { "from": 1000, "to": 2000 },
          { "from": 2000 }
        ]
      }
    }
  },
  "sort": [{ "_score": "desc" }, { "price": "asc" }]
}
```

## Graph Databases

Nodes and edges with native traversals to model and query complex relationships.

- Strengths: Multi-hop traversals, path queries, pattern matching, flexible schemas.
- Real-world example: Fraud detection finds suspicious payment rings across shared devices and cards.
- Typical fit: Social graphs, recommendations, knowledge graphs, fraud rings, network topology.

```cypher
// Neo4j schema: constraints and indexes
CREATE CONSTRAINT user_id IF NOT EXISTS FOR (u:User) REQUIRE u.id IS UNIQUE;
CREATE CONSTRAINT device_id IF NOT EXISTS FOR (d:Device) REQUIRE d.id IS UNIQUE;
CREATE CONSTRAINT card_number IF NOT EXISTS FOR (c:Card) REQUIRE c.number IS UNIQUE;

CREATE INDEX user_email IF NOT EXISTS FOR (u:User) ON (u.email);
CREATE INDEX card_last4 IF NOT EXISTS FOR (c:Card) ON (c.last4);

// Create nodes and relationships
CREATE (u1:User {id: 1, email: "user1@example.com"})
CREATE (u2:User {id: 2, email: "user2@example.com"})
CREATE (d:Device {id: "dev-abc", fingerprint: "xyz123"})
CREATE (c:Card {number: "4532...", last4: "1234"})

CREATE (u1)-[:USES]->(d)
CREATE (u2)-[:USES]->(d)
CREATE (u1)-[:PAYS_WITH]->(c)
CREATE (u2)-[:PAYS_WITH]->(c)

// Fraud detection query
MATCH (u1:User)-[:USES]->(:Device)<-[:USES]-(u2:User),
      (u2)-[:PAYS_WITH]->(:Card)<-[:PAYS_WITH]-(u3:User)
WHERE u1 <> u3
  AND u1.created_at > datetime() - duration('P30D')
RETURN DISTINCT u1.email, u3.email, count(*) AS connections
ORDER BY connections DESC
LIMIT 50;
```

## Analytical Columnar (OLAP)

Column-oriented storage for fast scans, aggregations, and compression over large datasets.

- Strengths: Vectorized execution, columnar compression, sub-second aggregations on billions of rows.
- Real-world example: Product analytics pipeline powering dashboards, funnels, and cohort reports.
- Typical fit: BI, event analytics, marketing attribution, cost reports.

```sql
-- ClickHouse schema
CREATE DATABASE analytics;

CREATE TABLE analytics.events (
  ts DateTime,
  user_id UInt64,
  session_id String,
  event String,
  country FixedString(2),
  device String,
  revenue Decimal(12,2) DEFAULT 0
) ENGINE = MergeTree
PARTITION BY toYYYYMM(ts)
ORDER BY (user_id, ts)
TTL ts + INTERVAL 1 YEAR;

CREATE MATERIALIZED VIEW analytics.events_hourly
ENGINE = SummingMergeTree
PARTITION BY toYYYYMM(hour)
ORDER BY (country, event, hour)
AS SELECT
  toStartOfHour(ts) AS hour,
  country,
  event,
  count() AS count,
  sum(revenue) AS total_revenue
FROM analytics.events
GROUP BY hour, country, event;

-- Query
SELECT country, event, count() AS c
FROM analytics.events
WHERE ts >= now() - INTERVAL 1 DAY
GROUP BY country, event
ORDER BY c DESC
LIMIT 10;
```

## Multi-Model

Single engine offering multiple models (document, graph, key-value) under one query layer.

- Strengths: Fewer moving parts, choose model per collection, simplify ops for mixed workloads.
- Real-world example: Content app stores articles as documents, tags as key-value, and related-article links as a graph in one system.
- Typical fit: Small-to-mid teams needing multiple access patterns without running many services.

```javascript
// ArangoDB schema
// Document collection
db._create("articles");
db.articles.ensureIndex({ type: "persistent", fields: ["topic"] });
db.articles.ensureIndex({ type: "fulltext", fields: ["content"] });

// Edge collection for graph relationships
db._createEdgeCollection("related_to");

// Key-value collection (document with single field)
db._create("settings");

// Insert documents
db.articles.insert({
  _key: "a1",
  title: "Intro to AI",
  topic: "ai",
  content: "Machine learning basics..."
});

db.related_to.insert({
  _from: "articles/a1",
  _to: "articles/a2",
  strength: 0.8
});

// AQL query: document + graph traversal
FOR a IN articles
  FILTER a.topic == "ai"
  LET related = (
    FOR v, e IN 1..2 OUTBOUND a related_to
      SORT e.strength DESC
      LIMIT 3
      RETURN { title: v.title, strength: e.strength }
  )
  RETURN { title: a.title, related: related }
```

## Putting It Together: Food Delivery System

- Relational (SQL): Users, restaurants, menus, orders, payments with transactions and foreign keys.
- Document (JSON): Menu item configurations, restaurant settings, promo rules with flexible fields.
- Redis: Cache menu pages, session tokens, rate limits; pub/sub to push live order status; Streams to fan out order events to fulfillment workers.
- Column-Family: High-volume courier location pings per courier for recent reads per user.
- Time-Series: Aggregated metrics like order latency, delivery times, service error rates for SRE dashboards.
- Search Engine: Full-text search for restaurants and dishes with filters by cuisine, price, rating.
- Graph: Courier-to-area assignments and user-friend recommendations; fraud detection across shared devices.
- Analytical Columnar: Business dashboards for orders per city, conversion funnels, A/B test results.
- Multi-Model: Optional consolidation for small teams if they prefer fewer systems at some scale.

## Choosing by Scenario

| Scenario | Recommended model | Why it fits |
|---|---|---|
| Orders and payments | Relational (SQL) | Transactions, constraints, joins |
| Product catalog | Document | Flexible attributes, nested variants |
| Caching and realtime updates | Redis | Millisecond latency, pub/sub, streams |
| Device or user event logs | Column-Family | Massive writes, time-ordered reads per key |
| Metrics and monitoring | Time-Series | Retention, downsampling, time buckets |
| Semantic support search | Vector | kNN similarity over embeddings |
| Site/app search with filters | Search Engine | Relevance scoring and facets |
| Fraud ring detection | Graph | Multi-hop relationship queries |
| Company-wide analytics | Analytical Columnar | Fast aggregations over large data |

## Practical Tips

- Model your data around your most frequent queries and latency SLOs.
- Keep OLTP and OLAP separate to avoid resource contention; sync via CDC or streams.
- Use Redis TTLs for caches and Streams with consumer groups for reliable background processing.
- In document and column-family stores, denormalize to match read patterns and avoid expensive joins.
- Combine systems pragmatically: SQL for core records, Redis for speed, Search for discoverability, OLAP for analytics.
