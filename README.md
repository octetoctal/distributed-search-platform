# Distributed Search Engine
A high-performance, fault-tolerant distributed search engine architecture

This system scales horizontally by separating the search lifecycle into two distinct phases: Sharding/Ingestion and Two-Phase Query Execution.

### 1. Indexing & Sharding
* **Document Ingestion:** Incoming documents are routed to specific shards using a consistent hashing algorithm: `hash(routing_key) % number_of_shards`.
* **Resilience:** Every primary shard maintains configured replica shards across separate nodes to guarantee high availability and data durability during node failures.

### 2. Search Lifecycle (Scatter-Gather)
* **The Query Phase (Scatter):** The coordinating node broadcasts incoming queries to all active shards. Each shard creates a local priority queue of matching document IDs sorted by relevance (BM25 score) and returns only these IDs and scores to the coordinator.
* **The Fetch Phase (Gather):** The coordinator merges all local lists, performs global re-ranking, and selects the absolute top hits. It then issues direct point-to-point requests to the specific shards holding those top documents to fetch the full raw payloads and returns them to the client.
