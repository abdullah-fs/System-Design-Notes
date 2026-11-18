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


## Section 5: Database Replication
<img width="1202" height="1282" alt="image" src="https://github.com/user-attachments/assets/7fdc154a-b1e4-4b49-9b29-035a93068d76" />


Database Replication is the process of maintaining copies of a database across multiple servers to improve performance, availability, and reliability.

### Master-Slave Model

#### 1. Master Database
- Handles all write operations.
- Insert, Update, and Delete commands are sent to the Master database.
- Replicates data to Slave databases.

#### 2. Slave Databases
- Primarily handle read operations.
- Receive replicated data from the Master database.
- Distribute read traffic across multiple servers.

### Benefits

1. **Improved Performance:** Multiple Slave databases handle read requests in parallel.
2. **High Availability:** Other healthy Slave databases can handle requests if one fails.
3. **Data Redundancy:** Multiple database copies improve data reliability.

### Failure Handling

- **Slave Failure:** Read requests are redirected to other healthy Slaves. If no Slave is available, reads can temporarily go to the Master.
- **Master Failure:** A healthy Slave can be promoted to become the new Master.
- **Data Recovery:** The new Master may need synchronization or recovery if it does not contain the latest data.

### Key Points
- Master handles write operations.
- Slaves primarily handle read operations.
- Data is replicated from Master to Slaves.
- Database replication improves performance and availability.



## Section 6: Caching
<img width="1160" height="372" alt="image" src="https://github.com/user-attachments/assets/e5256de9-e05b-43d8-9371-df965c127e3c" />


Caching is the process of storing frequently accessed data temporarily in memory to reduce database load and improve application performance.

### How Caching Works
1. The backend first checks the cache for requested data.
2. If data is found (**Cache Hit**), it is returned directly from the cache.
3. If data is not found (**Cache Miss**), the backend fetches it from the database.
4. The fetched data is stored in the cache for future requests.

### Cache vs Database
- **Database:** Stores original and permanent application data.
- **Cache:** Stores frequently accessed data temporarily in memory.
- Cache is much faster than a traditional database.

### Caching Considerations

#### 1. Use Case
- Best for data that is frequently read but rarely modified.

#### 2. Expiration Policy (TTL)
- Defines how long cached data remains in memory.
- Expired data is automatically removed.

#### 3. Consistency
- Ensures cache and database contain updated data.
- Inconsistency occurs when cached data is outdated.

#### 4. Mitigating Failures
- Multiple cache servers can reduce the risk of a Single Point of Failure (SPOF).

#### 5. Eviction Policies
- Remove data when the cache memory becomes full.
- **LRU (Least Recently Used):** Removes the data that has not been accessed for the longest time.

### Benefits
- Reduces database load.
- Improves response time.
- Increases application performance.

### Example
Redis can be used as a caching layer in a MERN application to store frequently requested MongoDB data.


## Section 7: Content Delivery Network (CDN)
<img width="908" height="832" alt="image" src="https://github.com/user-attachments/assets/49725cad-75d6-4698-913a-686b62530c29" />


A **CDN** is a network of geographically distributed servers that caches and delivers static content (images, CSS, JavaScript) from locations closer to users.

### Workflow
1. User requests static content.
2. The request is directed to a nearby CDN server.
3. If the content is available (**Cache Hit**), it is delivered directly.
4. If unavailable (**Cache Miss**), the CDN fetches content from the Origin Server and caches it.

### Important Terminologies

- **Static Content:** Images, CSS, JavaScript, and other files.
- **Origin Server:** The original server where website content is stored.
- **Cache Hit:** Requested content is found in the CDN cache.
- **Cache Miss:** Requested content is not found in the CDN cache.

### CDN Considerations

1. **Cost:** CDN providers charge for data transfers and usage.
2. **Cache Expiry:** Determines how long cached content remains available.
3. **CDN Fallback:** If the CDN becomes unavailable, clients should be able to request resources from the Origin Server.
4. **Invalidating Files:** Updated files should replace or invalidate old cached versions.

### Using a CDN Worldwide
- A single CDN provider can offer servers across multiple countries.
- Users receive static content from nearby CDN servers.
- There is no need to configure a separate CDN for every country.

### Popular CDN Providers
- Cloudflare
- Amazon CloudFront
- Akamai
- Cloudinary and ImageKit (for image storage, optimization, and delivery)

### Key Benefits
- Faster content delivery.
- Reduced load on the Origin Server.
- Improved website performance.
- Better experience for users worldwide.


## Section 8: Stateless Web Tier
<img width="902" height="884" alt="image" src="https://github.com/user-attachments/assets/e1d76b17-aec9-4206-8458-08c3d95fe767" />


A Stateless Web Tier is an architecture where web servers do not store user session data locally. Instead, session data is stored in a shared datastore such as Redis or a database.

### How It Works
1. A user logs in through Server 1.
2. Session information is stored in shared storage (e.g., Redis).
3. The user's next request may go to Server 2.
4. Server 2 retrieves the session information from the shared storage.
5. The user remains logged in without logging in again.

### Benefits
1. **Easy Horizontal Scaling:** New servers can be added easily because session data is not stored on individual servers.
2. **Auto-Scaling:** Servers can automatically increase or decrease based on traffic.

### Key Difference
- **Stateful:** Each server stores its own session data.
- **Stateless:** Session data is stored in shared storage and accessed by all servers.

### Key Point
Stateless web servers make applications easier to scale because any server can handle a user's request.



## Section 9: Multi-Data Center Setup
![Uploading image.png…]()

Multi-Data Center Setup means deploying an application across multiple geographical locations to improve availability and reduce latency.

### Main Strategies

1. **GeoDNS Routing:** Directs users to the nearest data center based on their geographical location.
2. **Data Replication:** Synchronizes data across multiple data centers to keep data available and consistent.

### Key Considerations

1. **Traffic Redirection:** Use effective tools to route users to the correct data center.
2. **Data Synchronization:** Replicate data across multiple data centers to maintain consistency.
3. **Testing and Deployment:** Use automated tools to keep services consistent across all data centers.

### Key Benefits

- **High Availability:** If one data center fails, another can handle requests.
- **Low Latency:** Users receive responses from nearby data centers.
- **Reliability:** Data is available across multiple locations.

### Key Difference

- **Load Balancer:** Distributes traffic between multiple servers.
- **GeoDNS:** Routes users between different geographical data centers.




