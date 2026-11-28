
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




# Chapter 16: Proximity Service

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




# Chapter 16: Proximity Service

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

