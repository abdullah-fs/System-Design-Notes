
# Chapter 17: Nearby Friends

## Introduction

Nearby Friends is a system that allows users to share their locations and discover friends who are nearby.

### Key Concept

The main challenge is handling continuously changing user locations while providing low-latency nearby friend searches.

### Proximity Service vs Nearby Friends

| Proximity Service | Nearby Friends |
|---|---|
| Finds nearby businesses. | Finds nearby friends. |
| Business locations are mostly static. | User locations change frequently. |
| Location updates are less frequent. | Requires frequent location updates. |
| Example: Nearby restaurants. | Example: Live friend location sharing. |

### Key Challenges

- Handling frequent location updates.
- Tracking users' latest locations.
- Efficiently finding nearby friends.
- Maintaining low-latency responses.
- Scaling with a large number of active users.



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

### Functional Requirements

1. Users can see nearby friends on their mobile app.
2. Each friend displays distance and location update timestamp.
3. Nearby friends list refreshes every few seconds.

### Non-Functional Requirements

- **Low Latency:** Location updates should be received with minimal delay.
- **Reliability:** Occasional data point loss is acceptable, but the system should remain generally available.
- **Eventual Consistency:** A few seconds of replication delay is acceptable; strong consistency is not required.

### Back-of-the-Envelope Estimation

| Metric | Estimation |
|---|---:|
| Nearby radius | 5 miles |
| Location refresh interval | 30 seconds |
| Daily active feature users | 100 million |
| Concurrent users (10%) | 10 million |
| Average friends per user | 400 |
| Friends displayed per page | 20 |

### Location Update QPS

Assuming 10 million concurrent users update their locations every 30 seconds:

Location Update QPS = 10,000,000 / 30

**Approximately 334,000 updates per second.**

### Key Challenge

The system must efficiently handle approximately 334K location updates per second while providing low-latency nearby friend discovery.
