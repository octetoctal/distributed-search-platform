# Distributed Search Engine
A high-performance, fault-tolerant distributed search engine architecture

This system scales horizontally by separating the search lifecycle into two distinct phases: Sharding/Ingestion and Two-Phase Query Execution.

### 1. Indexing & Sharding
* **Document Ingestion:** Incoming documents are routed to specific shards using a consistent hashing algorithm: `hash(routing_key) % number_of_shards`.
* **Resilience:** Every primary shard maintains configured replica shards across separate nodes to guarantee high availability and data durability during node failures.

### 2. Search Lifecycle (Scatter-Gather)
* **The Query Phase (Scatter):** The coordinating node broadcasts incoming queries to all active shards. Each shard creates a local priority queue of matching document IDs sorted by relevance (BM25 score) and returns only these IDs and scores to the coordinator.
* **The Fetch Phase (Gather):** The coordinator merges all local lists, performs global re-ranking, and selects the absolute top hits. It then issues direct point-to-point requests to the specific shards holding those top documents to fetch the full raw payloads and returns them to the client.

### Key Technical FeaturesHorizontal Scalability: Add nodes seamlessly to handle increased write throughput or intensive search volumes.Fault Isolation & Failover: Automatically promotes replicas if a primary node drops offline, ensuring zero data loss.Optimized Network I/O: The scatter phase transmits only IDs and floats (scores) rather than full objects, preventing network bottlenecks during deep pagination. [1] (/goto?url=CAESdgHrOzAV_mzSkp1_UffVoXx-j1NjORDtUqTsbY6FaNYWo3zttk8kRVzxJa052xEH4ZKkcjLDQvUKtKhCG6wORXnsit1faN-yRfYop9tMYj2e6f-U6JZLDM4v1rbUk8hA75HYqrVLh2-vDay77h6Hy2sajbq5F6M), [2] (/goto?url=CAESpwEB6zswFfuiec5pRN-aYko8KE0qEqRWCPt-3fa2pJVMldxxA_oCFbvJzICtSajf4ssdlBKU5xUcf6xVRvZjpckxRvsQ299Vwd-lSmVZr6ZSIXfmnt09pPS7a9KN74otk1hEgrFSq908bijsNAKV_mU2pISGDwLZNpMw-TzWmWRdMZA2wki7yvbcHZl-UoO5l6Lcz2qHSPBr7zMi5tkuaJZOW0Kc2upLxQ), [3] (/goto?url=CAESowEB6zswFXHSKHXr2nAFs7v0G_6LoQ5r_9F9sSYcru4idHn9JPP6SNSBj4gcuDov2ASPeOQnuYsIc3ASuP7d_HLxIoR70HQcvULBopM59xU4z_jeaIMftPqezT57Sk2wJ4I6XCubCiUCoGDF9xkpjPNA5_QchrFkX6_hvaxpw_wW7dSC492uaUu_9-JXmUQRSVknvSKM-6Jyku73n6c133SR1Rnv), [4] (/goto?url=CAESiAEB6zswFWuO-Lt1sFXAO8wSQD3raU5bRNdN76sRyH9lA2ORls8-x9p-oBTTrKelp8jjVwZyqwj0iRCChDsRbXtrezxJtGujABWRc_9OZHhsxiGTh-JKFzGmj03Xo-_y3zqhwGtFRgKlyiuPkqELDIm5pKAr8nYUoHbfqwC_VQPRcYn54B1lGq2c)
