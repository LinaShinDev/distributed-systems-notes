# System Design Interview Notes

## Database

### 1. Relational vs Non-Relational (NoSQL)
* **Relational Database (RDBMS)**: Stores data in tables with predefined schemas; supports ACID transactions and JOIN operations.
* **Non-Relational Database (NoSQL)**: Key-Value, Document, Column, and Graph stores. Join operations are generally not supported.

> **When to use NoSQL?**
> * Requires **super-low latency**.
> * Data lacks relational structure or only needs serialization/deserialization (e.g., JSON, XML).
> * Storing a **massive volume of data** across distributed nodes.

---

### 2. Database Scaling Strategy

| Vertical Scaling (Scale-Up) | Horizontal Scaling (Scale-Out) |
| :--- | :--- |
| Adding more power (CPU, RAM) to a single server | Adding more servers to the pool |
| Simple; no architectural change needed | Highly scalable; better fault tolerance |
| Hard hardware limits & Single Point of Failure (SPOF) | Complex; requires **Load Balancers** and **Stateless** servers |

* **Load Balancer**: Communicates with servers using private IPs for secure traffic distribution.
* **Primary / Secondary Replication (Master / Slave)**:
  * **Primary (Master)**: Handles **Write** operations (Insert, Update, Delete).
  * **Secondary (Follower)**: Handles **Read** operations and replicates data from Primary.

---

### 3. Cache Layer
* **In-Memory Data Store**: Reduces DB load by serving frequent read queries directly from memory (e.g., Redis).
* **Caching Strategy**: Cache-aside (Read-through) pattern is recommended to check cache before querying the database.
* **High Availability**: Use distributed cache clusters (e.g., Redis Sentinel/Cluster) to avoid SPOF as data grows.

---

### 4. CDN (Content Delivery Network)
* A network of geographically dispersed servers used to deliver static content (images, JS, CSS, videos).
* Significantly reduces latency by serving assets from the closest edge server to the user.

---

### 5. Stateless Architecture

* **Stateless Web Tier**: Moves session state and user data out of individual web servers and into a shared persistent data store (e.g., NoSQL / Redis).

| Feature | Stateful Architecture | Stateless Architecture |
| :--- | :--- | :--- |
| **State Management** | Remembers client state across multiple requests | Stores no client state on the web server |
| **Scalability** | Hard to scale out; requires sticky sessions on the load balancer | Highly scalable; any server can process any request behind a Load Balancer |

> * **Stateful**: Server and client are tied 1:1, often using sticky sessions configured on the load balancer.
> * **Stateless**: Client state is stored in a shared data store, making the architecture simple, resilient, and horizontally scalable.

---

### 6. Multi-Data Center Architecture

* **GeoDNS Routing**: When a client requests a DNS resolution, GeoDNS routes the request to the geographically nearest data center based on the user's IP address.
* **Key Challenges**:
  * **Traffic Redirection**: Accurately routing users to the correct data center.
  * **Data Synchronization**: Replicating data across multiple data centers to ensure consistency.
  * **Testing and Deployment**: Managing multi-datacenter testing and automated deployments.

---

### 7. Message Queue

* **Asynchronous Communication**: Enables decoupled, asynchronous processing between system components.
* **Architecture**: `Producer/Publisher` $\rightarrow$ `Message Queue` $\rightarrow$ `Consumer/Subscriber` (e.g., Apache Kafka, RabbitMQ)
* **Key Advantage**: Producers can publish messages to the queue even when consumers are offline or under heavy load.
> **Note**: Widely used in real-world distributed systems for loose coupling, fault tolerance, and workload smoothing.

---

### 8. Database Scaling

* **Vertical Scaling (Scale-up)**: Adding more CPU/RAM to a single server.
  * *Limitations*: Hardware capacity limits and exponentially increasing costs.
* **Horizontal Scaling (Scale-out / Sharding)**: Dividing large datasets into smaller, more manageable parts called **shards**.
  * Each shard shares the same schema but holds unique subset data.
  * **Sharding Key (Partition Key)**: Crucial for determining how data is distributed and routed efficiently for retrieval and modification.
* **Key Challenges of Sharding**:
  * **Resharding Data**: Required when a single shard grows too large due to uneven data distribution.
  * **Celebrity Problem (Hotspot Key)**: Excessive traffic concentrated on a specific shard (e.g., high-profile user data).
  * **Join and Denormalization**: Performing cross-shard joins becomes extremely difficult, often requiring database denormalization.



--- 
### 9. Latency
- fast memory & slow disk
- simple compression algorithems
- compress data before sending it over, it will take less time 


---
### 10. Rate Limiter 
- Controls the rate of traffic sent by a client (e.g., prevent DoS attacks, limit resource overuse).
- **Client-side vs. Server-side API Rate Limiter**: 
  - Client-side rate limiters are unreliable because client environments are easily controlled or bypassed by users.
  - Server-side API rate limiters provide reliable enforcement.
- **Key Requirements**: Low latency, minimal memory usage, and handling distributed environments gracefully.
- **Implementation Placement**:
  - **Middleware**: Intercepts requests and returns an HTTP `429 Too Many Requests` status code if the limit is exceeded.
  - **API Gateway**: Supports built-in rate limiting for various protocols (e.g., REST API, WebSocket, HTTP API).

---

### 11. Rate Limiting Algorithms
Understanding different rate-limiting algorithms helps choose the right combination for specific system requirements.

> #### 1. Token Bucket Algorithm
> - Works conceptually like a bucket with a pre-defined capacity that refills with tokens at a constant rate.
> - Can maintain different buckets for different API endpoints or user tiers.
> - **Pros**: Easy to implement, memory-efficient, allows short bursts of traffic up to the bucket capacity.
> - **Cons**: Tuning bucket size and refill rate parameters can be challenging.

> #### 2. Leaking Bucket Algorithm
> - Processes requests at a fixed, constant rate using a First-In-First-Out (FIFO) queue.
> - **Pros**: Smooths out traffic spikes; has a limited queue size to control memory overhead.
> - **Cons**: If the queue fills up with old requests, recent incoming requests will be dropped or rate-limited.

> #### 3. Fixed Window Counter Algorithm
> - Divides the timeline into fixed-sized time windows (e.g., 1-minute blocks) and assigns a counter to each window.
> - When the counter exceeds the threshold, new requests are dropped until the next time window begins.
> - **Pros**: Easy to understand.
> - **Cons**: Traffic spikes around the edge of windows (e.g., right before and after a reset) can allow double the allowed requests within a short actual timeframe.

> #### 4. Sliding window log algorithm 
> - 

