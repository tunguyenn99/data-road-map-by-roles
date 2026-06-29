# 🍃 MongoDB — Learning Guide (Beginner → Advanced)

## Tổng quan

**MongoDB** là NoSQL document database phổ biến nhất thế giới. Thay vì rows & columns (RDBMS), MongoDB lưu data dạng flexible JSON-like documents (BSON). Phù hợp cho applications cần schema flexibility, horizontal scaling, và rapid development.

**Use cases:**
- Web/Mobile application backends
- Content management systems
- Real-time analytics
- IoT data storage
- Caching layer

**Ai dùng:** Backend Developers, Full-stack Developers, Data Engineers (source systems)

---

## Level 1: Beginner (1–2 tuần)

### Core Concepts

| Concept | Mô tả |
|---------|--------|
| **Document** | Một record (JSON-like object với fields) |
| **Collection** | Group of documents (like a table) |
| **Database** | Group of collections |
| **BSON** | Binary JSON — MongoDB's storage format |
| **ObjectId** | Auto-generated unique identifier (12 bytes) |
| **Field** | Key-value pair trong document |
| **Embedded document** | Nested object within document |

### Install / Atlas Free Tier

```bash
# Option 1: Local install (Docker)
docker run -d -p 27017:27017 --name mongodb \
  -v mongodb_data:/data/db \
  mongo:7

# Option 2: MongoDB Atlas (free tier — recommended for learning)
# 1. Go to https://www.mongodb.com/cloud/atlas
# 2. Create free cluster (M0 — 512MB)
# 3. Get connection string

# Install MongoDB Shell (mongosh)
# Download from https://www.mongodb.com/try/download/shell
mongosh "mongodb://localhost:27017"
# or
mongosh "mongodb+srv://user:pass@cluster.mongodb.net/mydb"
```

### CRUD Operations

```javascript
// Connect to database
use myapp

// CREATE — Insert documents
db.customers.insertOne({
  name: "Alice Nguyen",
  email: "alice@example.com",
  age: 28,
  address: {
    street: "123 Le Loi",
    city: "Ho Chi Minh",
    country: "VN"
  },
  tags: ["premium", "active"],
  created_at: new Date()
})

db.customers.insertMany([
  { name: "Bob Tran", email: "bob@example.com", age: 35, tags: ["standard"] },
  { name: "Charlie Le", email: "charlie@example.com", age: 42, tags: ["premium"] }
])

// READ — Find documents
db.customers.find()                           // all
db.customers.find({ age: { $gt: 30 } })       // age > 30
db.customers.find({ tags: "premium" })        // array contains
db.customers.find({ "address.city": "Ho Chi Minh" })  // nested field
db.customers.findOne({ email: "alice@example.com" })

// Projection (select fields)
db.customers.find(
  { age: { $gte: 25 } },
  { name: 1, email: 1, _id: 0 }
)

// UPDATE
db.customers.updateOne(
  { email: "alice@example.com" },
  { $set: { age: 29 }, $push: { tags: "vip" } }
)

db.customers.updateMany(
  { age: { $lt: 30 } },
  { $set: { segment: "young" } }
)

// DELETE
db.customers.deleteOne({ email: "bob@example.com" })
db.customers.deleteMany({ tags: "inactive" })
```

### Basic Queries

```javascript
// Comparison operators
db.products.find({ price: { $gt: 100, $lt: 500 } })  // 100 < price < 500
db.products.find({ category: { $in: ["Electronics", "Books"] } })
db.products.find({ discount: { $exists: true } })

// Logical operators
db.products.find({
  $and: [
    { price: { $lt: 100 } },
    { rating: { $gte: 4.5 } }
  ]
})

db.products.find({
  $or: [
    { category: "Electronics" },
    { price: { $gt: 1000 } }
  ]
})

// Sort, limit, skip
db.products.find().sort({ price: -1 }).limit(10)
db.products.find().skip(20).limit(10)  // pagination

// Count
db.orders.countDocuments({ status: "completed" })
```

### Bài tập

1. **Setup Atlas** — Tạo free cluster, kết nối bằng mongosh
2. **CRUD practice** — Insert 20 documents, query với filters, update, delete
3. **Nested documents** — Model user + addresses + orders (embedded)
4. **Python driver** — Dùng PyMongo thực hiện CRUD operations

### Resources

- 📖 [MongoDB Docs](https://www.mongodb.com/docs/manual/)
- 📖 [MongoDB University (free courses)](https://learn.mongodb.com/)
- 📖 [Atlas Getting Started](https://www.mongodb.com/docs/atlas/getting-started/)
- 💻 [MongoDB GitHub](https://github.com/mongodb/mongo)
- 📖 [PyMongo Docs](https://pymongo.readthedocs.io/)


---

## Level 2: Intermediate (2–4 tuần)

### Aggregation Pipeline

```javascript
// Aggregation = multi-stage data processing pipeline
db.orders.aggregate([
  // Stage 1: Filter
  { $match: { status: "completed", created_at: { $gte: ISODate("2024-01-01") } } },
  
  // Stage 2: Lookup (JOIN)
  { $lookup: {
    from: "customers",
    localField: "customer_id",
    foreignField: "_id",
    as: "customer"
  }},
  { $unwind: "$customer" },
  
  // Stage 3: Group & Aggregate
  { $group: {
    _id: "$customer.country",
    total_revenue: { $sum: "$amount" },
    order_count: { $sum: 1 },
    avg_order_value: { $avg: "$amount" },
    customers: { $addToSet: "$customer_id" }
  }},
  
  // Stage 4: Calculate
  { $addFields: {
    unique_customers: { $size: "$customers" },
    revenue_per_customer: { $divide: ["$total_revenue", { $size: "$customers" }] }
  }},
  
  // Stage 5: Sort
  { $sort: { total_revenue: -1 } },
  
  // Stage 6: Limit
  { $limit: 10 }
])

// Time-series aggregation
db.events.aggregate([
  { $match: { event_type: "purchase" } },
  { $group: {
    _id: {
      year: { $year: "$timestamp" },
      month: { $month: "$timestamp" },
      day: { $dayOfMonth: "$timestamp" }
    },
    daily_revenue: { $sum: "$amount" },
    transactions: { $sum: 1 }
  }},
  { $sort: { "_id.year": 1, "_id.month": 1, "_id.day": 1 } }
])
```

### Indexes

```javascript
// Single field index
db.customers.createIndex({ email: 1 })  // ascending
db.customers.createIndex({ created_at: -1 })  // descending

// Compound index
db.orders.createIndex({ customer_id: 1, created_at: -1 })

// Text index (full-text search)
db.products.createIndex({ name: "text", description: "text" })
db.products.find({ $text: { $search: "wireless bluetooth" } })

// Unique index
db.users.createIndex({ email: 1 }, { unique: true })

// TTL index (auto-delete after time)
db.sessions.createIndex({ created_at: 1 }, { expireAfterSeconds: 3600 })

// Check index usage
db.orders.find({ customer_id: ObjectId("...") }).explain("executionStats")

// List indexes
db.orders.getIndexes()
```

### Schema Validation

```javascript
// Add validation rules to collection
db.createCollection("products", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "price", "category"],
      properties: {
        name: {
          bsonType: "string",
          description: "must be a string and is required"
        },
        price: {
          bsonType: "number",
          minimum: 0,
          description: "must be a positive number"
        },
        category: {
          enum: ["Electronics", "Books", "Clothing", "Food"],
          description: "must be one of the listed categories"
        },
        tags: {
          bsonType: "array",
          items: { bsonType: "string" }
        }
      }
    }
  },
  validationAction: "error"  // or "warn"
})
```

### Atlas Search

```javascript
// Full-text search with Atlas Search (requires Atlas)
// First: create search index in Atlas UI

db.products.aggregate([
  { $search: {
    index: "product_search",
    compound: {
      must: [
        { text: { query: "laptop", path: "name" } }
      ],
      should: [
        { text: { query: "gaming", path: "description", score: { boost: { value: 2 } } } }
      ],
      filter: [
        { range: { path: "price", gte: 500, lte: 2000 } }
      ]
    }
  }},
  { $project: {
    name: 1,
    price: 1,
    score: { $meta: "searchScore" }
  }},
  { $limit: 10 }
])
```

### Bài tập

1. **Aggregation** — Viết 5 aggregation pipelines (group, lookup, unwind, bucket)
2. **Indexes** — Create indexes, dùng explain() đo performance improvement
3. **Schema validation** — Design validation rules cho e-commerce products
4. **Atlas Search** — Setup text search, implement autocomplete

### Resources

- 📖 [Aggregation Pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/)
- 📖 [Indexes Guide](https://www.mongodb.com/docs/manual/indexes/)
- 📖 [Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)
- 📖 [Atlas Search](https://www.mongodb.com/docs/atlas/atlas-search/)

---

## Level 3: Advanced (4–8 tuần)

### Sharding

```javascript
// Enable sharding on database
sh.enableSharding("myapp")

// Shard a collection (choose shard key carefully!)
sh.shardCollection("myapp.orders", { customer_id: "hashed" })
// or range-based:
sh.shardCollection("myapp.events", { timestamp: 1, region: 1 })

// Check sharding status
sh.status()

// Shard key selection guidelines:
// - High cardinality (many unique values)
// - Distribute writes evenly
// - Support common query patterns
// - Cannot be changed after sharding!
```

### Replication

```javascript
// Replica Set config
rs.initiate({
  _id: "myReplicaSet",
  members: [
    { _id: 0, host: "mongo1:27017", priority: 2 },  // primary
    { _id: 1, host: "mongo2:27017", priority: 1 },  // secondary
    { _id: 2, host: "mongo3:27017", priority: 1 },  // secondary
  ]
})

// Read from secondaries
db.getMongo().setReadPref("secondaryPreferred")

// Check replica set status
rs.status()
```

### Transactions (Multi-document)

```python
# PyMongo transaction example
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/?replicaSet=myReplicaSet")
db = client.myapp

def transfer_funds(from_account: str, to_account: str, amount: float):
    """Transfer money between accounts (ACID transaction)."""
    with client.start_session() as session:
        with session.start_transaction():
            # Debit source
            result = db.accounts.update_one(
                {"_id": from_account, "balance": {"$gte": amount}},
                {"$inc": {"balance": -amount}},
                session=session
            )
            if result.modified_count == 0:
                session.abort_transaction()
                raise ValueError("Insufficient funds")
            
            # Credit destination
            db.accounts.update_one(
                {"_id": to_account},
                {"$inc": {"balance": amount}},
                session=session
            )
            
            # Record transaction
            db.transactions.insert_one({
                "from": from_account,
                "to": to_account,
                "amount": amount,
                "timestamp": datetime.utcnow()
            }, session=session)
            
            # Auto-commits if no exception
    print(f"Transferred ${amount} from {from_account} to {to_account}")
```

### Change Streams

```python
# Real-time change detection
from pymongo import MongoClient
import json

client = MongoClient("mongodb://localhost:27017/?replicaSet=myReplicaSet")
db = client.myapp

# Watch collection for changes
pipeline = [
    {"$match": {"operationType": {"$in": ["insert", "update"]}}},
    {"$match": {"fullDocument.status": "critical"}}
]

with db.alerts.watch(pipeline=pipeline, full_document="updateLookup") as stream:
    for change in stream:
        print(f"Change detected: {change['operationType']}")
        print(f"Document: {json.dumps(change['fullDocument'], default=str)}")
        # Trigger notification, sync to another system, etc.
```

### Atlas Data Federation

```javascript
// Query across MongoDB + S3 + Atlas data lake
// Configure in Atlas UI: Data Federation > Create Federated Instance

// Query S3 Parquet files via MongoDB query language
db.s3_sales_data.aggregate([
  { $match: { year: 2024, region: "APAC" } },
  { $group: {
    _id: "$product_category",
    total_sales: { $sum: "$amount" }
  }},
  { $sort: { total_sales: -1 } }
])
```

### Bài tập

1. **Transactions** — Implement transfer_funds with ACID guarantees
2. **Change streams** — Build real-time sync giữa MongoDB và Elasticsearch
3. **Sharding design** — Design shard key cho high-write IoT application
4. **Performance** — Profile slow queries, optimize with indexes + explain

### Resources

- 📖 [Sharding Guide](https://www.mongodb.com/docs/manual/sharding/)
- 📖 [Transactions](https://www.mongodb.com/docs/manual/core/transactions/)
- 📖 [Change Streams](https://www.mongodb.com/docs/manual/changeStreams/)
- 📖 [Atlas Data Federation](https://www.mongodb.com/docs/atlas/data-federation/)
- 📖 [Performance Best Practices](https://www.mongodb.com/docs/manual/administration/analyzing-mongodb-performance/)

---

## Alternatives & Công cụ tương đương

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| **MongoDB** | Document DB | Flexible schema, rich queries, Atlas | Memory-heavy, no true joins |
| **PostgreSQL (JSONB)** | RDBMS + JSON | ACID + JSON flexibility, mature | Less native document feel |
| **DynamoDB** | Key-value/Document | Serverless, AWS-native, auto-scale | Limited query patterns, expensive |
| **CouchDB** | Document DB | Offline-first, sync, REST API | Smaller ecosystem |
| **Firestore** | Document DB | Real-time sync, Firebase ecosystem | GCP lock-in, limited queries |
| **Cassandra** | Wide-column | Write-heavy, linear scale, HA | Complex data modeling |

### Khi nào chọn gì?

- **MongoDB** → Flexible schema, rapid development, rich queries, general-purpose
- **PostgreSQL JSONB** → Need ACID + relational + JSON, already using Postgres
- **DynamoDB** → Serverless, predictable latency, simple access patterns
- **CouchDB** → Offline-first apps, peer-to-peer sync
- **Firestore** → Mobile/web apps, real-time sync, Firebase stack
- **Cassandra** → Massive write throughput, time-series, global distribution
