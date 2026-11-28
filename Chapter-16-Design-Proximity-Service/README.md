
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

