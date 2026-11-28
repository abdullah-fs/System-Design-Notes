
# Chapter 16: Proximity Service

## Step 1: Understanding the Problem and Establishing Scope

### Introduction

A **Proximity Service** helps users find nearby places or businesses based on their geographic location.

Examples include Google Maps and Yelp, where users can search for nearby restaurants, hotels, gas stations, and other businesses.

### Functional Requirements

1. Search for businesses based on user latitude, longitude, and search radius.
2. Allow business owners to add, update, or delete business information.
3. Provide detailed information about a business when requested.

### Non-Functional Requirements

- **Low Latency:** Search results should be returned quickly.
- **Data Privacy:** Protect user location and comply with privacy regulations such as GDPR and CCPA.
- **High Availability:** Keep the service available during peak hours and traffic spikes.

### Back-of-the-Envelope Estimation

| Metric | Estimate |
|---|---:|
| Daily Active Users (DAU) | 100 million |
| Total Businesses | 200 million |
| Searches per User per Day | 5 |
| Daily Searches | 500 million |

#### Search QPS Calculation

Daily searches:

\[
100M \times 5 = 500M
\]

Average Search QPS:

\[
\frac{500M}{86,400} \approx 5,787
\]

Approximately **5,000–5,800 search requests per second** on average.

QPS (Queries Per Second) represents the number of search requests handled by the system per second.

Note: Peak-hour traffic can be significantly higher than average QPS, so the system must be designed to handle traffic spikes.




## Step 2: High-Level Design

### 1. API Design

#### Search Nearby Businesses

```http
GET /v1/search/nearby
```

Request Parameters:

- `latitude`: User's latitude.
- `longitude`: User's longitude.
- `radius`: Search radius in meters (default: 5000m).

#### Business APIs

| API Endpoint | Description |
|---|---|
| `GET /v1/businesses/{id}` | Fetch business details |
| `POST /v1/businesses` | Add a new business |
| `PUT /v1/businesses/{id}` | Update business details |
| `DELETE /v1/businesses/{id}` | Delete a business |

### 2. Data Model

A relational database such as **MySQL** is a suitable choice because:

- Nearby business searches are frequent.
- Users frequently view business details.
- Business information is structured.

#### Key Database Tables

1. **Business Table:** Stores business details such as name, address, category, and contact information.
2. **Geospatial Index Table:** Stores geographic location data (latitude and longitude) to support efficient nearby searches.

### 3. High-Level System Architecture
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/8e4687f0-c1bd-4016-b14b-334499725b8a" />


The system consists of two main services:

#### A. Location-Based Service (LBS)

- Processes location-based search queries.
- Read-heavy service with no write requests.
- Handles high QPS, especially during peak hours in densely populated areas.
- Stateless architecture.

#### B. Business Service

Handles two types of requests:

- Business owners create, update, and delete businesses.
- Customers view detailed business information.

#### C. Load Balancer

- Routes incoming requests to the appropriate service.
- Nearby search requests go to LBS.
- Business management and detail requests go to Business Service.

#### D. Database Cluster

Uses a **Primary-Replica Architecture** to handle read-heavy workloads.

- **Primary Database:** Handles write operations (create, update, delete).
- **Replica Databases:** Handle read operations.

### 4. Data Inconsistency

- Replication between the primary database and replicas may introduce delays.
- LBS may read outdated data from a replica.
- This inconsistency is acceptable because business information does not require real-time updates.

**Example:** A business owner updates a restaurant's address in the primary database. A replica may temporarily return the old address until replication completes.

### Key Takeaway

The system separates location-based searches from business-related operations. LBS handles nearby searches, while Business Service manages business information. Primary-replica architecture supports high read traffic.




## Step 3: Algorithms for Fetching Nearby Businesses

### 1. Two-Dimensional Search (Naive Approach)
<img width="1194" height="648" alt="image" src="https://github.com/user-attachments/assets/0af62442-c5b2-4a28-8383-c1d4ebbfea54" />


- Draws a circle around the user's location using a predefined radius.
- Searches for businesses within the latitude and longitude range.

**SQL Query:**

```sql
SELECT business_id, latitude, longitude
FROM business
WHERE (latitude BETWEEN :lat - radius AND :lat + radius)
AND (longitude BETWEEN :long - radius AND :long + radius);
```

**Problems:**
- May require scanning a large number of database records.
- Traditional indexes are not efficient for two-dimensional spatial searches.
- Creating separate indexes on latitude and longitude provides limited improvement.

**Better Approach:** Convert 2D geographic data into a 1D representation using geospatial indexing.

Common techniques:
- Hash-based: Even Grid, Geohash.
- Tree-based: Quadtree, Google S2, R-Tree.

### 2. Evenly Divided Grid
<img width="980" height="495" alt="image" src="https://github.com/user-attachments/assets/fa9f9674-4f89-4616-806d-592ce1a0045d" />


- Divides the world into fixed-size geographic grids.
- Each business is assigned to a grid based on its location.

**Limitation:**
- Business distribution is uneven.
- Cities have high business density, while rural areas have fewer businesses.
- Some grids contain too many businesses, creating an imbalance.

### 3. Geohash
Geohash converts latitude and longitude into a single alphanumeric string.

**How It Works:**

1. Divides the world into four quadrants using the equator and prime meridian.
2. Recursively divides each quadrant into four smaller grids.
3. Encodes longitude and latitude bits during subdivision.
4. Generates a Geohash string representing the location.

**Key Features:**
- Provides a hierarchical grid structure.
- Supports multiple precision levels.
- Longer Geohash strings represent smaller geographic areas.
- Locations sharing longer prefixes are generally geographically closer.

**Boundary Problem:**
- Nearby businesses may belong to different Geohash cells.
- Two nearby locations may have very different or no common prefixes.

**Solution:** Search neighboring Geohash cells along with the current cell.
<img width="918" height="778" alt="image" src="https://github.com/user-attachments/assets/6aa62aba-52cf-4b6a-b056-9e8dc25bd42f" />
<img width="1190" height="870" alt="image" src="https://github.com/user-attachments/assets/4efa7142-3e8f-42d7-a691-ee801c0eec8c" />
<img width="2971" height="1814" alt="image" src="https://github.com/user-attachments/assets/17ade1fc-938b-4991-82a3-81bcfc1597db" />
<img width="1025" height="800" alt="image" src="https://github.com/user-attachments/assets/fa64bb4c-96b4-4ea8-8f68-86cae4fdc37f" />
<img width="606" height="426" alt="image" src="https://github.com/user-attachments/assets/44ea5f9e-394a-48c5-94fc-7490fe0b8cfe" />
<img width="939" height="594" alt="image" src="https://github.com/user-attachments/assets/1944471c-70e4-472c-80ff-5bf7dbca1716" />
<img width="1400" height="764" alt="image" src="https://github.com/user-attachments/assets/e054055c-d3e2-4d18-a0a1-cc7b036ab8ce" />
<img width="1200" height="701" alt="image" src="https://github.com/user-attachments/assets/d548ea03-490c-4431-bb6d-1ad12e964362" />
<img width="1680" height="1005" alt="image" src="https://github.com/user-attachments/assets/7fc60d3c-1c4b-4f80-b537-d5637e62547b" />
<img width="1400" height="764" alt="image" src="https://github.com/user-attachments/assets/eee2a96e-2518-47af-8df1-bc850f3cbc76" />
<img width="1128" height="573" alt="image" src="https://github.com/user-attachments/assets/8ebd6012-44c0-4c18-91ff-be67930253f1" />
<img width="1400" height="1190" alt="image" src="https://github.com/user-attachments/assets/d2f7dd33-9c8d-4d91-9721-4fe257de9497" />

### 4. Quadtree

A Quadtree is a tree data structure that recursively divides a 2D space into four quadrants.

**How It Works:**

1. The root node represents the entire geographic area.
2. Divides the root into four quadrants.
3. If a quadrant contains more than a predefined number of businesses (e.g., 100), it is divided again.
4. Continues until each region contains an acceptable number of businesses.

**Key Features:**
- In-memory data structure.
- Built on each Location-Based Service (LBS) server during startup.
- Typically requires memory in the GB range.
- Building the tree takes approximately O(n log n) time.
- Efficient for K-nearest-neighbor searches, such as finding the closest gas station.

**Operational Considerations:**
- Building a Quadtree for 200 million businesses may take several minutes.
- A server cannot serve traffic while its tree is being rebuilt.
- Deploy updates incrementally across servers.
- Business additions and updates require tree updates or rebuilding.
- On-the-fly updates are possible but require more complex logic and locking mechanisms.
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/0701882d-2a78-4625-9477-aaf28ff27ba2" />
<img width="1200" height="685" alt="image" src="https://github.com/user-attachments/assets/68279bd2-521c-4389-9eae-4c96fc0f9419" />
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/6ff34f0d-38aa-4424-8817-094e7a1044f2" />
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/ceb566c7-a1cd-4d87-a341-a6c684e1d3c3" />
<img width="1500" height="1496" alt="image" src="https://github.com/user-attachments/assets/dcf6b336-daba-4228-a615-3aa8039a4e0b" />
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/45178987-597e-44d8-aa39-d18abffd3d4c" />
<img width="2414" height="2414" alt="image" src="https://github.com/user-attachments/assets/1b15ca0f-aaa9-4581-a4b8-8c2386d09b0c" />
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/239d2a7c-4135-4d16-98e2-fab32eb725d6" />
<img width="1121" height="1081" alt="image" src="https://github.com/user-attachments/assets/2e8f8b01-324e-454a-947c-8ffcfeb8f9e7" />

### 5. Google S2

Google S2 is a geospatial indexing system that maps Earth's spherical surface into a one-dimensional index using a Hilbert Curve.

**Hilbert Curve:**
- A space-filling curve that maps 2D space into a 1D path.
- Nearby points in 2D space are generally mapped to nearby positions in 1D space.

**Key Features:**
- Divides Earth into small geographic cells.
- Uses Hilbert Curve-based indexing.
- Supports different levels of geographic precision.
- Useful for geofencing.
- Can cover arbitrary geographic areas using multiple cells.
- Supports minimum level, maximum level, and maximum cell count.

**Geofencing:**
- Defines a virtual boundary around a geographic area.
- Applications can trigger actions when users enter or leave that area.
<img width="1161" height="855" alt="image" src="https://github.com/user-attachments/assets/02c44f2d-6da6-4a70-a069-a76512d80bca" />
<img width="900" height="742" alt="image" src="https://github.com/user-attachments/assets/08edf5ed-db9c-4e25-bdae-bd2d4583700f" />
<img width="2048" height="1536" alt="image" src="https://github.com/user-attachments/assets/72e585d0-2d15-4339-afd3-7e9dffcbd5bf" />
<img width="3840" height="2787" alt="image" src="https://github.com/user-attachments/assets/a221603a-56da-4152-b418-7ac22fc86aae" />
<img width="593" height="588" alt="image" src="https://github.com/user-attachments/assets/6380d894-41fa-4f3e-bc6d-608d49608e31" />
<img width="1454" height="826" alt="image" src="https://github.com/user-attachments/assets/bd7d8fd4-2e7c-40f8-ba56-96a22150a655" />
<img width="924" height="752" alt="image" src="https://github.com/user-attachments/assets/670cfa81-aa61-4c19-8f11-17aaf6637d52" />
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/bee28975-f815-4cce-85d0-f213af5acee2" />
<img width="1351" height="702" alt="image" src="https://github.com/user-attachments/assets/453bd8d6-99d2-4363-9498-b7065987f234" />
<img width="1914" height="1658" alt="image" src="https://github.com/user-attachments/assets/a765353b-f5b7-40ce-b78e-f070f71dc34a" />
<img width="2048" height="1536" alt="image" src="https://github.com/user-attachments/assets/12775b51-3422-4d26-864a-4904d6af135f" />
<img width="1349" height="888" alt="image" src="https://github.com/user-attachments/assets/1cddbd8e-3ee0-4d9c-8f99-83c380bcf77a" />

### Algorithm Comparison

| Algorithm | Main Idea | Limitation |
|---|---|---|
| 2D Search | Direct latitude/longitude search | Slow for large datasets |
| Even Grid | Fixed-size geographic cells | Uneven business distribution |
| Geohash | Encodes location into a string | Boundary problem |
| Quadtree | Recursively divides space into four regions | Build and update complexity |
| Google S2 | Uses Hilbert Curve-based indexing | More complex implementation |

### Key Takeaway

Geospatial indexing improves nearby business searches by converting two-dimensional geographic locations into efficient index structures.

Geohash, Quadtree, and Google S2 are different approaches to solving this problem.




## Tradeoff Comparison: Geohash vs Quadtree

### 1. Geohash

**Advantages:**
- Easy to understand and implement.
- No need to build or rebuild a tree.
- Supports fixed-radius searches.
- Business location updates are easy.

**Disadvantages:**
- Cannot dynamically adjust grid size based on business or population density.
- Boundary issues may require searching neighboring grids.

### 2. Quadtree

**Advantages:**
- Supports K-nearest business searches.
- Dynamically adjusts grid size based on business density.
- Divides densely populated areas into smaller regions for efficient searching.

**Disadvantages:**
- More complex to implement than Geohash.
- Updating the index is more complicated.
- Business updates may require rebuilding the entire tree.

### Quick Comparison

| Feature | Geohash | Quadtree |
|---|---|---|
| Implementation | Easy | More complex |
| Fixed-radius Search | Supported | Supported |
| K-nearest Search | Requires additional logic | Efficient |
| Dynamic Grid Size | No | Yes |
| Index Updates | Easy | More complicated |
| Tree Rebuilding | Not required | May be required |

### Key Takeaway

- **Geohash:** Suitable for simple implementation and fixed-radius searches.
- **Quadtree:** Useful for dynamic geographic partitioning and K-nearest searches.



## Step 4: Scaling the Database and Caching Strategy

### 1. Scaling the Business Table

- Sharding by **Business ID** ensures even data distribution across multiple database servers.
- Each business has a separate row in the business table.
- Sharding distributes storage and workload across multiple servers.

**Example:**

| Geohash | Business ID |
|---|---|
| 9q9hvu | 343 |
| 9q9hvu | 347 |
| 9q9hvu | 112 |

Multiple businesses can belong to the same Geohash grid.

### 2. Scaling the Geospatial Index

- Sharding may not be necessary for the geospatial index table.
- The entire index can fit on a single server in this design.
- Read replicas are a better approach to handle high read traffic.
- Read replicas distribute search requests and reduce the read load on the primary database.

### 3. Cache Strategy

#### Problem with GPS Coordinates as Cache Keys

Using exact GPS coordinates as cache keys has several problems:

- GPS coordinates are not always accurate.
- User movement changes location coordinates.
- Small coordinate changes can generate different cache keys.

#### Better Approach: Geohash as Cache Key

Use Geohash instead of exact GPS coordinates to group nearby locations into geographic grids.

**Cache Structure:**

| Cache Key | Cache Value |
|---|---|
| `geohash` | List of business IDs in that grid |
| `business_id` | Business details (name, address, reviews, etc.) |

#### Cache Workflow

1. Receive user's latitude and longitude.
2. Generate the corresponding Geohash.
3. Use the Geohash as the cache key.
4. If data exists in the cache, return the business IDs.
5. If there is a cache miss, fetch data from the database and store it in the cache.

### Key Takeaway

- **Business Table:** Scale using sharding by Business ID.
- **Geospatial Index:** Use read replicas if the index fits on a single server.
- **Caching:** Use Geohash instead of exact GPS coordinates as the location-based cache key.
- Cache business IDs by Geohash and business details by Business ID.
