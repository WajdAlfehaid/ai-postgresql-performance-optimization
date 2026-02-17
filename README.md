# Data Engineering with Postgres & Docker MCP
**AI-Assisted Database Orchestration & Performance Tuning**

**Author:** Wajd Alfehaid  
**Email:** wajdalfehaid@gmail.com  
**Tech Stack:** Cursor (MCP), Docker, PostgreSQL, Python (uv)

---

## 🚀 Project Overview
This project explores the cutting edge of **AI-assisted Data Engineering** by integrating **Cursor** with the **Model Context Protocol (MCP)**. 

Instead of manually running CLI commands, I configured Cursor to interact directly with **Docker** and **PostgreSQL**. The AI agent was tasked with orchestrating the entire lifecycle: instantiating a containerized database, securing user roles, ingesting 40,000+ rows of synthetic e-commerce data, and performing advanced query optimization audits.



### 🔑 Key Concepts & Tools
* **Cursor MCP:** A bridge protocol allowing the AI editor to "break out" of the text box and execute commands in the local environment (Docker/DB).
* **Docker:** Used for containerization to ensure an isolated, reproducible PostgreSQL environment. 
* **uv:** An extremely fast Python package manager (written in Rust) used for efficient project dependency management.
* **PostgreSQL:** The core relational database engine used for storage and structured querying. 
* **Performance Tuning:** utilizing `EXPLAIN ANALYZE` to identify bottlenecks and optimize schema indexing.

---

## 🛠 Environment Setup
The foundation of the project relies on a modern, high-performance toolchain.

### 1. Containerization with Docker
I installed **Docker Desktop** to abstract away the database installation. This allows the PostgreSQL instance to run in an isolated container, ensuring that the development environment is consistent across different machines.

### 2. Fast Package Management with `uv`
Instead of standard `pip`, I utilized **uv** for Python project management.
* **Why?** It is 10-100x faster than traditional tools.
* **Setup:** The command `uv init` generated the project structure, including `pyproject.toml` for dependencies and a virtual environment for the MCP servers.

---

## 🔌 Connecting MCP Servers
The critical innovation in this project is the **Model Context Protocol (MCP)**.
* **Without MCP:** An AI editor can only see and edit code files.
* **With MCP:** The AI gains "hands" and "eyes" in the system. It can query the running Docker daemon, execute SQL inside the container, and read system logs.

I configured Cursor to connect to:
1.  **Docker MCP:** To manage container lifecycles.
2.  **PostgreSQL MCP:** To execute queries and manage database schemas directly from the chat interface.

---

## 💾 Database Construction & Security

### 1. Schema & Ingestion
I prompted the AI to spin up a PostgreSQL container and ingest a demo dataset.
* **Data Volume:** ~40,000 rows.
* **Domain:** E-commerce (Customers, Products, Orders, Order Items).
* **Method:** Piped raw SQL directly into the container:
    ```bash
    cat demo_data.sql | docker exec -i pg-local psql -U app -d demo
    ```

### 2. Security: The Principle of Least Privilege
Instead of relying on the default `postgres` superuser, I enforced security best practices by creating a dedicated application user (`app`).
* **Benefit:** If the application is compromised, the attacker is confined to specific tables and cannot alter the database configuration or access other databases.

---

## ⚡ Performance Engineering Audit
A major focus of this project was moving beyond *functioning* code to *performant* code. I tasked the AI with running a database performance audit.

### The Audit Process
We utilized the `EXPLAIN ANALYZE` command to visualize the query execution plan. This reveals whether the database is performing efficient **Index Scans** or slow **Sequential Scans**.

### Critical Findings
* **Bottleneck Identified:** The audit revealed that joining `Orders` and `Order_Items` was triggering sequential scans.
* **Root Cause:** Missing indexes on Foreign Keys (`order_items.order_id`, `order_items.product_id`).
* **Current Metrics:** * Execution Time: ~5.3ms (acceptable now, but non-scalable).
    * Cache Hit Rate: 99.93% (indicating good memory usage).

### Optimization Strategy
To resolve the bottleneck, I applied **B-Tree Indexes** to the foreign key columns. This converts the operation from an $O(N)$ full table scan to an $O(log N)$ index lookup, ensuring the database remains fast as data volume grows to millions of rows.

---

## 📝 Key Takeaways
* **AI as an Orchestrator:** MCP turns the IDE into a command center, allowing AI to perform infrastructure tasks, not just write code.
* **Database Hygiene:** Security (least privilege) and Performance (indexing) must be designed from Day 1, not added as an afterthought.
* **Modern Tooling:** Combining `uv` and Docker creates a development environment that is both blazing fast and strictly reproducible.
