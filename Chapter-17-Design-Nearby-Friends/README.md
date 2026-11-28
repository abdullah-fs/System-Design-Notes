
# Chapter 17: Nearby Friends

## Introduction

Nearby Friends is a system that allows users to share their locations and discover friends who are nearby.

### Key Concept

The main challenge is handling continuously changing user locations while providing low-latency nearby friend searches.

**Daily-Life Example:**

Imagine you are at a shopping mall and want to find your friends. Nearby Friends shows their current locations, distance from you, and when their location was last updated. As your friends move, their locations are updated.

### Proximity Service vs Nearby Friends

| Proximity Service | Nearby Friends |
|---|---|
| Finds nearby businesses. | Finds nearby friends. |
| Business locations are mostly static. | User locations change frequently. |
| Location updates are less frequent. | Requires frequent location updates. |
| Example: Nearby restaurants. | Example: Live friend location sharing. |

**Daily-Life Example:**

A restaurant usually stays at the same address, so its location does not need frequent updates. However, your friend walking through a park changes their location continuously.

### Key Challenges

- Handling frequent location updates.
- Tracking users' latest locations.
- Efficiently finding nearby friends.
- Maintaining low-latency responses.
- Scaling with a large number of active users.

**Daily-Life Example:**

If millions of people are sharing their locations while traveling, the system must process their updates quickly and still show nearby friends without noticeable delay.

---

## Step 1: Understand the Problem and Establish Design Scope

### Design Assumptions

| Question | Assumption |
|---|---|
| Nearby radius | 5 miles (configurable) |
| Distance calculation | Straight-line distance |
| Total users | 1 billion |
| Feature adoption | 10% of total users |
| Location history | Stored for future ML use cases |
| Inactive users | Disappear after 10 minutes |
| GDPR compliance | Not considered in this simplified design |

**Daily-Life Examples:**

- **Nearby Radius:** If the radius is set to 5 miles, friends within that distance can appear in the nearby list.
- **Straight-Line Distance:** If a river separates two friends, the system still calculates the direct distance between them, not the road distance.
- **Location History:** Previous location records can help analyze movement patterns or support future machine learning use cases.
- **Inactive Users:** If a friend stops sharing location updates, they disappear from the nearby list after 10 minutes.

### Functional Requirements

1. Users can see nearby friends on their mobile app.
2. Each friend displays distance and location update timestamp.
3. Nearby friends list refreshes every few seconds.

**Daily-Life Example:**

When you open the app, it shows a friend 500 meters away and indicates that their location was updated 20 seconds ago. A few seconds later, the list refreshes with their latest location.

### Non-Functional Requirements

- **Low Latency:** Location updates should be received with minimal delay.
- **Reliability:** Occasional data point loss is acceptable, but the system should remain generally available.
- **Eventual Consistency:** A few seconds of replication delay is acceptable; strong consistency is not required.

**Daily-Life Example:**

Your friend moves to a new location. One server may receive the update immediately, while another server receives it a few seconds later. This delay is acceptable as long as the location eventually becomes consistent.

### Back-of-the-Envelope Estimation

| Metric | Estimation |
|---|---:|
| Nearby radius | 5 miles |
| Location refresh interval | 30 seconds |
| Daily active feature users | 100 million |
| Concurrent users (10%) | 10 million |
| Average friends per user | 400 |
| Friends displayed per page | 20 |

**Daily-Life Example:**

Suppose 10 million users are actively sharing their locations at the same time. Each user sends a location update every 30 seconds. The backend must handle millions of updates continuously, even though each user only sees 20 friends on a page.

### Location Update QPS

Assuming 10 million concurrent users update their locations every 30 seconds:

Location Update QPS = 10,000,000 / 30

**Approximately 334,000 updates per second.**

**Daily-Life Example:**

If 10 million people are using the feature, the system receives around 334,000 location updates every second. This is similar to a huge crowd continuously sending their current positions to a central service.

### Key Challenge

The system must efficiently handle approximately 334K location updates per second while providing low-latency nearby friend discovery.

The major challenge is managing frequently changing location data at a massive scale.




## Step 2: Propose High-Level Design and Get Buy-In

### Communication Protocol

Peer-to-peer (P2P) communication is not practical for mobile apps because of unstable connections, battery constraints, and frequent online/offline transitions.

A shared backend is a more practical approach. It receives location updates and forwards them to relevant friends using a **fan-out mechanism**.

**Example:** When a user updates their location, the backend distributes it to online friends who are within the configured distance.
<img width="1074" height="497" alt="image" src="https://github.com/user-attachments/assets/1239c2c2-eece-4e91-8432-9d147e6fd3d7" />
<img width="562" height="522" alt="image" src="https://github.com/user-attachments/assets/6192c0fa-3437-41a2-a84c-91bb2eac76b5" />
<img width="637" height="591" alt="image" src="https://github.com/user-attachments/assets/e7515d88-2275-401b-abc4-37b7e6ab6591" />
<img width="822" height="452" alt="image" src="https://github.com/user-attachments/assets/c9aa874c-32b5-49a3-8d87-cc82a5fa598a" />
<img width="890" height="472" alt="image" src="https://github.com/user-attachments/assets/3edf4ceb-e6ec-49da-9930-d46a08b83177" />
<img width="5187" height="3440" alt="image" src="https://github.com/user-attachments/assets/0fcc24a6-beec-4f31-9e9e-1c2700858ea1" />

### High-Level Architecture

The system uses the following components:

| Component | Responsibility |
|---|---|
| **Load Balancer** | Distributes traffic across REST API and WebSocket servers. |
| **REST API Servers** | Handle auxiliary tasks such as managing friends and updating profiles. |
| **WebSocket Servers** | Maintain persistent client connections, receive location updates, and forward updates to friends. |
| **Redis Location Cache** | Stores the latest location of each active user with a TTL. |
| **User Database** | Stores user profiles and friendship relationships. |
| **Location History Database** | Stores historical location data for analytics and ML use cases. |
| **Redis Pub/Sub** | Distributes location updates through user-specific channels. |

**Example:** Redis location cache stores a friend's current location, while the location history database stores previous location updates for future analysis.
<img width="1074" height="497" alt="image" src="https://github.com/user-attachments/assets/4949248a-bd79-4c76-9dab-bc70e4a7c4a0" />
<img width="562" height="522" alt="image" src="https://github.com/user-attachments/assets/c33f070a-6f57-4fbb-b288-ac697e1e0403" />
<img width="617" height="473" alt="image" src="https://github.com/user-attachments/assets/00b8208e-7c38-4e97-b922-c497aa4ff9e2" />
<img width="637" height="591" alt="image" src="https://github.com/user-attachments/assets/8b459292-8ce1-4f1f-82f4-ceabee1153e2" />
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/beafa4b4-795a-4a75-8c45-1539a6c709a4" />
<img width="800" height="334" alt="image" src="https://github.com/user-attachments/assets/e82b7a7f-453d-4e99-97ba-5795f2efa988" />

### Fan-Out Backend

Fan-out means distributing one message to multiple relevant recipients.

For each location update, the backend:

1. Finds active friends who may receive the update.
2. Calculates the distance between the users.
3. Forwards the update only if the friend is within the configured radius.

**Example:** If a user has 400 friends, the backend does not send the location to all 400 friends blindly. It filters recipients based on their online status and distance.

### Redis Pub/Sub

Each user has a dedicated channel for location updates.

WebSocket servers subscribe to channels for users whose friends are connected to them.

When a location update is published:

1. The update is published to the user's channel.
2. Redis Pub/Sub broadcasts it to subscribed WebSocket servers.
3. WebSocket servers identify relevant connected friends.
4. The update is forwarded to their mobile clients.

**Example:** A WebSocket server subscribed to `user_123` receives that user's location updates and forwards them to appropriate friends connected to the server.

### Periodic Location Update Flow

1. Mobile client sends a location update to the Load Balancer.
2. Load Balancer forwards it to the client's WebSocket server.
3. WebSocket server saves the location in the Location History Database.
4. Latest location is updated in Redis Location Cache.
5. WebSocket server also stores the user's location in memory for distance calculations.
6. Location update is published to the user's Redis Pub/Sub channel.
7. Subscribed WebSocket servers receive the update.
8. Servers calculate which connected friends are within the configured radius.
9. Location and timestamp are sent to relevant friends through WebSocket connections.

**Example:** When a user moves to a new location, the backend updates Redis and publishes the new location. Nearby friends receive the update without repeatedly querying the database.
<img width="1074" height="497" alt="image" src="https://github.com/user-attachments/assets/d77945e5-e2ff-4424-ac0b-37eef7b05b15" />

### Location Update Fan-Out Estimation

Assumptions:

- Average friends per user: 400
- Online friends: 10%

Average online friends:

`400 × 10% = 40`

Therefore, each location update may have approximately **40 potential online recipients** before applying the distance filter.

**Example:** If 40 friends are online, but only 12 are within the configured radius, the location update is forwarded to those 12 friends.

### WebSocket API Design

| WebSocket Routine | Purpose |
|---|---|
| **Periodic Location Update** | Client sends its current location to the server. |
| **Receive Location Update** | Client receives a friend's location and timestamp. |
| **Client Initialization** | Client sends its location; server returns the initial nearby friends list. |
| **Subscribe to Friend** | Client is notified to track a newly available friend. |
| **Unsubscribe Friend** | Client is notified to stop tracking a friend who is offline or no longer relevant. |

HTTP REST APIs are used for auxiliary tasks such as friend management and profile updates.

**Example:** When a friend becomes online, the server sends their ID so the client can start tracking them. When they go offline, the client receives an unsubscribe instruction.

### Data Model

#### Location Cache – Redis

Stores the latest location of each active user.

`user_id → latitude, longitude, timestamp`

Redis is suitable because:

- It provides fast access to current location.
- It supports TTL-based expiration.
- Inactive users can automatically disappear from the cache.

**Example:** If a user stops sending location updates, their Redis entry expires after the configured TTL.

#### Location History Database – Cassandra

Stores historical location records with the following fields:

- `user_id`
- `latitude`
- `longitude`
- `timestamp`

Cassandra is suitable for write-heavy workloads because the system receives frequent location updates.

**Example:** Redis stores a user's current location, while Cassandra stores previous location updates that may be used for analytics or machine learning.

### Key Takeaways

- Use a shared backend instead of mobile P2P communication.
- WebSocket provides persistent, bidirectional communication.
- Redis Location Cache stores the latest location with TTL.
- Redis Pub/Sub distributes location updates to subscribed WebSocket servers.
- Cassandra stores historical location data for write-heavy workloads.
- Fan-out forwards location updates only to relevant nearby friends.
- Approximately 40 online friends may be potential recipients per update, before distance filtering.
