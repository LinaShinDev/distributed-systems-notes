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

### 5. Stateless Web Tier
* Moves session state and user data out of web servers into a shared persistent data store (e.g., NoSQL / Redis).

| Stateful Server | Stateless Server |
| :--- | :--- |
| Remembers client state across requests; hard to scale out | Keeps no state information; easily scalable behind a Load Balancer |