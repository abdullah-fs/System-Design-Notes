# Chapter 5: Design Consistent Hashing

## Introduction

Consistent Hashing is a technique used to distribute data and requests across multiple servers. It minimizes data redistribution when servers are added or removed, making horizontal scaling more efficient.

## Key Objectives

- **Horizontal Scaling:** Distribute data across multiple servers.
- **Minimal Redistribution:** Move only a small portion of data when servers are added or removed.
- **Load Distribution:** Distribute data and requests across servers.
- **Reduce Hotspots:** Prevent excessive load on a single server.

## Example

Consider an application with three servers:

- Server A
- Server B
- Server C

If Server B fails or a new Server D is added, traditional hashing may require redistributing a large amount of data.

Consistent Hashing minimizes this redistribution by moving only the affected data to maintain efficient distribution.

## Real-World Applications

- Distributed Databases
- Redis Clusters
- Distributed Caching
- Load Balancing
- Amazon DynamoDB

## Key Benefits

- Supports horizontal scalability.
- Minimizes data redistribution.
- Improves resource utilization.
- Helps maintain system availability.
- Supports distributed systems efficiently.

## Summary

Consistent Hashing is an effective technique for distributing data across multiple servers while minimizing data movement when servers are added or removed.



## The Rehashing Problem

### Explanation

Traditional hashing methods distribute data across servers using the following formula:

```javascript
serverIndex = hash(key) % N
```

Where:
- `hash(key)`: Hash value of the data key.
- `N`: Total number of servers.
- `%`: Modulo operator.

When the number of servers changes, the modulo result changes, causing many keys to be reassigned.

### Problems

**1. Adding a Server**
- Adding a new server changes the total server count.
- Many keys get assigned to different servers.
- Existing cache entries may become inaccessible through their previous mappings.

**2. Removing a Server**
- Removing a server changes the server count.
- Most keys may be reassigned to other servers.
- This leads to increased cache misses.

### Key Issues

- **Cache Misses:** Previously cached data cannot be found at the newly calculated server.
- **Data Redistribution:** A large number of keys need reassignment.
- **Performance Degradation:** Additional database requests increase latency.
- **Server Overload:** Increased requests can overload backend servers.

### Limitations

Traditional hashing works well when the number of servers remains fixed. However, it becomes inefficient when servers are frequently added or removed.

### Solution

Consistent Hashing solves this problem by minimizing key redistribution when the server pool changes.
