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
<img width="1222" height="712" alt="image" src="https://github.com/user-attachments/assets/5a8b5f4f-2be7-42ed-aae3-f0696b762d8c" />



## Section 2: Database Separation
<img width="1204" height="700" alt="image" src="https://github.com/user-attachments/assets/3d02e9a3-268c-48c2-870b-9416952a2c5e" />


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



## Section 3: Vertical vs Horizontal Scaling

### 1. Vertical Scaling (Scale Up)
- Increases resources (CPU, RAM, storage) of an existing server.
- Improves the capacity and performance of that server.
- Has hardware limitations.
- Does not provide built-in redundancy.

**Example:** Upgrading a server from 4 GB RAM to 16 GB RAM.

### 2. Horizontal Scaling (Scale Out)
- Adds more servers to the existing server pool.
- Distributes the workload across multiple servers.
- Suitable for large-scale applications.
- Provides better redundancy and scalability.

**Example:** Adding three Node.js servers instead of running the application on a single server.

### Key Difference

- **Vertical Scaling:** Makes one server more powerful.
- **Horizontal Scaling:** Adds more servers to handle increased traffic.

## Section 4: Load Balancer
<img width="1192" height="1004" alt="image" src="https://github.com/user-attachments/assets/b4cc5995-594e-442e-a147-bf9b6bf110c9" />

A **Load Balancer** distributes incoming network traffic among multiple servers.

### Benefits

#### 1. Redundancy
- If one server goes offline, traffic is redirected to other available servers.
- Keeps the application running even if a server fails.

**Example:** If Server 1 goes offline, traffic is routed to Server 2.

#### 2. Scalability
- Allows additional servers to be added when traffic increases.
- Helps handle sudden traffic spikes.

**Example:** If website traffic grows, additional servers can be added to handle incoming requests.

### Key Points
- Distributes traffic among multiple servers.
- Improves availability and reliability.
- Supports horizontal scaling.




