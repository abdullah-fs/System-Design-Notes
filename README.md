# System-Design-Notes
My learning journey and notes on System Design.

# Chapter 1: Scale from Zero to Millions of Users

## Section 1: Single Server Setup

### 1. Single Server Setup
- Initially, all application components run on a single server.
- Components include web application, backend, database, and cache.
- Suitable for applications with low traffic.

### 2. Request Flow
1. Users enter a domain name in their browser.
2. DNS resolves the domain into an IP address.
3. The browser connects to the web server.
4. The web server receives the HTTP request.
5. The server returns an HTML or JSON response.

### 3. Traffic Sources

**Web Applications**
- Use HTML, CSS, and JavaScript for the frontend.
- Use Node.js, Python, or Java for backend logic.

**Mobile Applications**
- Communicate with web servers using HTTP.
- Commonly exchange data in JSON format.

## Section 2: Database Separation

### 4. Database Separation
- The database is moved to a dedicated server as the user base grows.
- Web and database servers operate independently.
- Allows independent scaling of both components.

### 5. Independent Scaling
- Increasing the capacity of different system components separately.
- Web servers and database servers can be upgraded independently.
- Example: Adding more web servers without changing the database server.

### 6. Relational Databases (SQL)
- Store structured data in tables with rows and columns.
- Support relationships between tables.
- Examples: MySQL and PostgreSQL.

### 7. Non-Relational Databases (NoSQL)
- Store data using different data models.
- Support flexible data structures.
- Examples: MongoDB, Redis, Cassandra, and Neo4j.

### 8. Types of NoSQL Databases

| Type | Description | Example |
|---|---|---|
| Key-Value Store | Stores key-value pairs | Redis |
| Document Store | Stores data in documents | MongoDB |
| Graph Database | Stores nodes and relationships | Neo4j |
| Column Store | Organizes data by columns | Cassandra |

### 9. When to Use NoSQL?
- When very low latency is required.
- When data is unstructured or has a flexible structure.
- When serialization and deserialization are needed.
- When storing massive amounts of data.

## Important Terminologies

| Term | Definition |
|---|---|
| Scaling | Increasing system capacity |
| DNS | Resolves domain names into IP addresses |
| HTTP | Communication protocol between client and server |
| JSON | Lightweight data exchange format |
| Hosting | Making an application available on the internet |
| Server | A computer that processes requests |
| Latency | Time delay in communication |
| Serialization | Converting data into a transferable format |
| Deserialization | Converting serialized data into a usable format |


