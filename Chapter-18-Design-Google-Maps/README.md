
# Chapter 18: Google Maps

## Introduction

Google Maps is a mapping and navigation service that helps users explore locations, view maps, check traffic conditions, and find routes between places.

### Main Services

1. **Satellite Imagery:** Displays satellite images of the Earth's surface.
2. **Street Maps:** Shows roads, streets, buildings, and important places.
3. **Real-time Traffic:** Provides current traffic conditions on roads.
4. **Route Planning:** Calculates routes between locations and provides navigation.

### Important Facts

- Google Maps was launched in 2005.
- By 2021, it had approximately 1 billion daily active users.
- It covered around 99% of the world's population.
- It processed approximately 25 million real-time location updates daily.

## Real-Life Example

Suppose a user wants to travel from Peshawar to Islamabad.

Google Maps:
1. Detects the user's current location.
2. Identifies the destination.
3. Finds available roads between the locations.
4. Checks current traffic conditions.
5. Calculates the route and estimated travel time.
6. Provides turn-by-turn navigation.

## System Design Goal

Design a simplified version of Google Maps that supports:

- Viewing maps and locations.
- Searching for places.
- Finding routes between locations.
- Displaying real-time traffic conditions.
- Providing navigation with low latency and high availability.




# Chapter 18: Google Maps

## Step 1: Understand the Problem and Establish Design Scope

### Functional Requirements

Focus on three main features:

1. **User Location Update:** Update the user's current location.
2. **Navigation Service:** Find routes and calculate ETA.
3. **Map Rendering:** Display maps smoothly on mobile devices.

Out of scope:
- Multi-stop directions
- Business places and photos

### Non-functional Requirements

- **Accuracy:** Provide correct directions.
- **Smooth Navigation:** Render maps without noticeable lag.
- **Low Data and Battery Usage:** Minimize mobile resource consumption.
- **Availability:** Keep the service accessible.
- **Scalability:** Support large numbers of users.

## Map 101

### 1. Positioning System

A geographic location is represented using:

- **Latitude:** North or south position relative to the equator.
- **Longitude:** East or west position relative to the prime meridian.

Example: A location in Peshawar can be identified using its latitude and longitude.

### 2. Map Projection

Map projection converts the Earth's 3D surface into a 2D map.

Different projections introduce different types of distortion. Google Maps uses a modified version of the Mercator projection called Web Mercator.

### 3. Geocoding

Geocoding converts a human-readable address into geographic coordinates.

Example:
`Address -> Latitude, Longitude`

**Reverse Geocoding** converts coordinates into a readable address.

Example:
`Latitude, Longitude -> Address`

### 4. Geohashing

Geohashing encodes a geographic area into a string of letters and digits.

- The world is recursively divided into four quadrants.
- Each subdivision represents a smaller geographic area.
- Nearby locations often share similar geohash prefixes.

Geohashing is useful for geographic indexing and nearby-location searches.

### 5. Map Rendering

Maps are rendered using small square images called **tiles**.

- The world is divided into tiles.
- Different zoom levels use different tiles.
- The client downloads only the tiles needed for the visible area.
- The client combines tiles to render the map.
- Tile caching reduces repeated downloads and data usage.

Example: Viewing Peshawar loads the relevant map tiles instead of downloading the entire world map.

### 6. Road Data and Routing Graph

Road networks are represented as a graph:

- **Nodes:** Road intersections.
- **Edges:** Roads connecting intersections.

Routing algorithms such as Dijkstra and A* find suitable paths through the graph.

### Routing Tiles

The entire world's road graph is too large to load into memory at once.

To improve performance:

- Divide the world into smaller routing tiles.
- Store references to neighboring tiles.
- Load only relevant tiles for a route.
- Connect tiles while traversing the road network.

### Hierarchical Routing

Routing tiles can have different levels of detail.

- High-level tiles help find major roads for long-distance routes.
- Detailed tiles help navigate local roads near the source and destination.

This reduces memory usage and improves routing performance.

## Back-of-the-Envelope Estimation

| Metric | Estimate |
|---|---:|
| World map storage | ~70 PB |
| Metadata | Negligible |
| Navigation QPS | ~200K |
| Peak navigation QPS | ~1M |

The course estimates approximately 5 billion usage minutes per day based on 1 billion DAU and 35 minutes of weekly usage per user.

Batching GPS updates helps reduce request volume.

**QPS (Queries Per Second):** The number of requests processed by a system per second.




## Step 2: Propose High-Level Design and Get Buy-In

The system consists of three main services:

1. Location Service
2. Navigation Service
3. Map Rendering Service

### 1. Location Service

Responsible for recording user location updates.

- Clients send location updates every `t` seconds.
- Location data includes latitude, longitude, and timestamp.
- Location streams can improve ETA, traffic detection, road closure detection, and user behavior analysis.

#### Client-side Batching

Instead of sending every GPS update separately, clients batch multiple updates into a single request.

Benefits:
- Reduces request overhead.
- Saves network bandwidth.
- Reduces mobile data usage.

Example request:

`POST /v1/locations`

Payload contains an array of:
`(latitude, longitude, timestamp)`

#### Backend Components

- **Cassandra:** Stores high-volume location updates using a write-optimized database.
- **Kafka:** Streams location events for analytics and further processing.

Example: Location updates from many users can help detect traffic congestion on a road.

### 2. Navigation Service

Calculates routes between an origin and destination with reasonable latency and high accuracy.

Example request:

`GET /v1/nav?origin=Peshawar&destination=Islamabad`

The response may contain:
- Distance
- Estimated duration (ETA)
- Start and end locations
- Navigation instructions
- Encoded route polyline
- Travel mode

**Polyline:** An encoded sequence of geographic points used to draw the route on the map.

Traffic changes and rerouting are handled in the deep dive section.

### 3. Map Rendering Service

The entire map dataset is too large to store on mobile clients.

Therefore, map tiles are fetched on-demand based on:
- User location
- Zoom level
- Visible map area

Clients request new tiles while zooming or moving into a new area during navigation.

#### Static Map Tiles

Dynamically generating tiles creates high server load and makes caching difficult.

A better approach is to:
- Pre-generate map tiles.
- Store and serve tiles statically.
- Use geohash or tile coordinates to identify tiles.
- Cache tiles on a CDN.

#### CDN

A Content Delivery Network (CDN) serves map tiles from nearby Point of Presence (POP) servers.

Benefits:
- Lower latency
- Reduced origin server load
- Better scalability
- Efficient caching

Example: A user in Peshawar can download map tiles from a nearby CDN location instead of a distant origin server.

#### Tile URL Calculation

Two approaches:

1. **Client-side calculation:** The client calculates tile URLs. This avoids an extra API call, but changing the calculation logic later may require client updates.
2. **Server-side calculation:** An API calculates tile URLs for the client. This allows server-side changes but requires an additional API request.
