# Data Engineering with Postgres & Docker MCP


**Author:** Wajd Alfehaid  
**Email:** wajdalfehaid@gmail.com



## Introducing Today's Project!

In this project, I'm going to connect Cursor to Docker and PostgreSQL MCPs, then prompt Cursor to create a Docker container running PostgreSQL, load 40,000+ rows of data, and finally query, visualize, and optimize the database.

PostgreSQL is an advanced, open-source relational database system for storing and managing structured data with SQL.

Docker is a containerization platform that packages PostgreSQL with all dependencies into an isolated, portable container, eliminating the need for local installation.

### Key tools and concepts

Key tools I used include:

Docker Desktop - for containerized PostgreSQL databases
uv - for fast Python project management
Cursor MCP servers - connecting AI to external services
PostgreSQL - database operations and querying
EXPLAIN ANALYZE - query performance profiling

Key concepts I learnt include:
Database containerization - running databases in isolated environments
MCP architecture - how AI connects to external tools
Application user security - least privilege principle for database access
Database indexing - adding indexes on foreign keys to eliminate slow sequential scans
Query performance auditing - identifying bottlenecks through execution plans
Cache hit rate analysis - measuring memory vs. disk usage efficiency



## Setting Up Docker and UV

In this step, I'm setting up my development environment by installing Docker Desktop and uv.
Docker Desktop will help me run and manage containers locally, allowing me to containerize my applications for consistent, reproducible development environments.
uv is a fast Python package manager that replaces tools like pip and virtualenv. It will handle Python package installation and project management 10-100x faster than traditional tools.

### What's Docker and why containers?

### Verifying installations

The tools I set up in my environment were Docker Desktop and uv (a fast Python package manager).
I confirmed they were installed correctly by running their version commands (docker --version and uv --version), which returned the installed versions and verified they're accessible from the command line.


## Connecting MCP Servers

In this step, I'm going to initialize a Python project with uv and configure Cursor to use MCP servers.
MCP servers (Model Context Protocol) are programs that connect Cursor to external tools and data sources.
Without MCPs, Cursor can only see the code in your current editor.
With MCPs, Cursor can access databases, external APIs, files, and other services. This turns it from a code editor into a powerful assistant that interacts with your entire development environment.

### Python project setup

The command uv init set up a Python project because MCP servers need an organized environment to manage dependencies and configurations.
The files created include:
pyproject.toml - defines project metadata and dependencies
.gitignore - specifies files to exclude from version control
README.md - for project documentation
main.py - entry point with example code
This structure provides a foundation for installing and configuring the MCP servers we'll add next.



## Building My Database

In this step, I'm going to use the Docker MCP to create a PostgreSQL container and the PostgreSQL MCP to configure users and load demo data.

By the end, I'll have a fully functional PostgreSQL database running in a container with sample data ready to use.

### Database security

Every PostgreSQL database comes with a default superuser (postgres) that has unlimited permissions.
I created an application user named app to connect my application with limited, specific permissions instead of using the superuser.
This is security best practice because:
It follows the principle of least privilege - the app only gets what it needs
If compromised, the attacker can't access other databases
It prevents accidental schema changes or data loss from the application

### Loading demo data

I loaded the demo data by piping the SQL file directly into the PostgreSQL container using cat demo_data.sql | docker exec -i pg-local psql -U app -d demo, which reads the SQL file and executes it inside the running container. The data contains e-commerce sample data with over 40,000 rows spread across customers, products, orders, and order items, giving us realistic data to test queries and visualize relationships.


## Performance Audit

I'm going to perform a database performance audit which analyzes how efficiently my queries run, identifies slow operations, missing indexes, and bottlenecks.
Database administrators care about this because slow queries in production can cause application delays, poor user experience, and higher cloud costs due to increased CPU, memory, and disk usage.
This optimization helps by finding and fixing inefficient queries, ensuring the database runs smoothly, responds quickly, and scales effectively as data grows.

### Audit findings

Cursor's audit analyzed query performance, missing indexes, and cache efficiency by running EXPLAIN ANALYZE which shows exactly how PostgreSQL executes queries, including scan types, join methods, and execution times.

It found bottlenecks in foreign key columns lacking indexes on orders.customer_id, order_items.order_id, and order_items.product_id, causing sequential scans on joins. While the current execution time of 5.3ms for a customer-orders join is acceptable now, it will degrade as data grows. The cache hit rate was excellent at 99.93%, meaning data fits well in memory.

The recommendations included adding three targeted indexes on the foreign key columns, which will replace slow sequential scans with efficient index scans, allowing the database to scale smoothly as more data is added.
