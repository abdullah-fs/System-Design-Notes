
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
