# System Design Interview Notes

## Database

### 1. Relational vs Non-Relational (NoSQL)
* **Relational Database (RDBMS)**: Stores data in tables with predefined schemas and supports ACID transactions and JOIN operations.
* **Non-Relational Database (NoSQL)**: Divided into Key-Value, Document, Column, and Graph stores. Join operations are generally not supported.

> **When to use NoSQL?**
> * Requires **super-low latency**.
> * Data lacks relational structure or only needs serialization/deserialization (e.g., JSON, XML).
> * Storing a **massive volume of data** across distributed nodes.

---
### 2. Database Scaling Strategy

| Feature | Vertical Scaling (Scale-Up) | Horizontal Scaling (Scale-Out) |
| :--- | :--- | :--- |
| **Definition** | Adding more power (CPU, RAM) to a single server | Adding more servers to the pool |
| **Pros** | Simple; no architectural change needed | Highly scalable; better fault tolerance |
| **Cons** | Hard hardware limits & Single Point of Failure (SPOF) | Complex; requires **Load Balancers** and **Stateless** servers |